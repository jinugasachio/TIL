# syntax=docker/dockerfile:1

# TODO: ARGを外から渡す

#====================================
# base ステージ
#====================================
ARG RUBY_VERSION=3.1.4
FROM ruby:$RUBY_VERSION-slim-bullseye AS base

ENV APP_HOME=/freee-sign \
    BUNDLE_PATH=vendor/bundle \
    BUNDLE_WITHOUT=development:test \
    LANG=C.UTF-8 \
    NODE_ENV=production \
    RAILS_ENV=production \
    RAILS_SERVE_STATIC_FILES=true \
    TZ=Asia/Tokyo

# ビルド用なのでコンテナ実行時に上書きされる
ENV AWS_REGION=dummy \
    AWS_ACCESS_KEY_ID=dummy \
    AWS_SECRET_ACCESS_KEY=dummy \
    S3_BUCKET=dummy \
    KMS_KEY_ID=dummy \
    GOOGLE_CLIENT_ID=dummy \
    GOOGLE_CLIENT_SECRET=dummy \
    FREEE_CLIENT_ID=dummy \
    FREEE_CLIENT_SECRET=dummy \
    AUTH0_DOMAIN=dummy \
    AUTH0_API_AUDIENCE=dummy \
    AUTH0_CLIENT_ID=dummy \
    AUTH0_CLIENT_SECRET=dummy \
    SECRET_KEY_BASE=dummy \
    RELEASE_COMMIT_HASH=dummy

ARG DEBIAN_FRONTEND=noninteractive \
    BUNDLER_VERSION=2.4.1 \
    IMAGE_MAGICK_VERSION=7.1.0-62 \
    NODE_MAJOR=18 \
    PG_MAJOR=12 \
    YARN_VERSION=1.22.5

RUN apt-get update -qq && \
    apt-get install -y --no-install-recommends \
      gnupg2 \
      curl \
      wkhtmltopdf && \
    gem update --system && gem install bundler:$BUNDLER_VERSION

WORKDIR $APP_HOME

#====================================
# builder ステージ
#====================================
FROM base AS builder

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# node を sources list に追加
RUN curl -sL https://deb.nodesource.com/setup_$NODE_MAJOR.x | bash -

# postgresql (client) を sources list に追加
RUN curl -sSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor -o /usr/share/keyrings/postgres-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/postgres-archive-keyring.gpg] https://apt.postgresql.org/pub/repos/apt/" bullseye-pgdg main "$PG_MAJOR" | tee /etc/apt/sources.list.d/postgres.list > /dev/null

# yarn を sources list に追加
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor -o /usr/share/keyrings/yarn-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/yarn-archive-keyring.gpg] https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list > /dev/null

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
      build-essential \
      git \
      libpq-dev \
      libfreetype6-dev \
      libjpeg-dev \
      libpng-dev \
      libxext-dev \
      libxml2-dev \
      libx11-dev \
      nodejs \
      postgresql-client-"$PG_MAJOR" \
      pkg-config \
      yarn="$YARN_VERSION-1" \
      zlib1g-dev

# imagemagickをインストール
RUN git clone --depth 1 -b $IMAGE_MAGICK_VERSION --single-branch https://github.com/ImageMagick/ImageMagick.git \
  && cd ImageMagick \
  && ./configure \
  && make -j4 \
  && make install \
  && ldconfig /usr/local/lib

#====================================
# bundle ステージ
#====================================
FROM builder AS bundle

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

ARG BUNDLE_GEMS__CONTRIBSYS__COM
ARG GITHUB_TOKEN_FOR_GITHUB_PACKAGES

ENV BUNDLE_CLEAN=true \
    BUNDLE_DEPLOYMENT=true \
    BUNDLE_FROZEN=true \
    BUNDLE_JOBS=4 \
    GITHUB_TOKEN_FOR_GITHUB_PACKAGES=$GITHUB_TOKEN_FOR_GITHUB_PACKAGES \
    BUNDLE_RUBYGEMS__PKG__GITHUB__COM=$GITHUB_TOKEN_FOR_GITHUB_PACKAGES \
    BUNDLE_GEMS__CONTRIBSYS__COM=$BUNDLE_GEMS__CONTRIBSYS__COM

COPY Gemfile Gemfile.lock ./

RUN bundle install
RUN rm -rf "$BUNDLE_PATH"/ruby/*/cache/*
RUN find "$BUNDLE_PATH"/ruby -path "*/gems/*/ext/*/Makefile" -exec dirname {} \; | xargs -n1 -P"$(nproc)" -I {} make -C {} clean
RUN find "$BUNDLE_PATH"/ruby -path "*/gems/*" -name "Dockerfile" -or -name "docker-compose.yml" | xargs rm

#====================================
# yarn ステージ
#====================================
FROM builder AS yarn

COPY package.json yarn.lock ./

RUN yarn install --frozen-lockfile
RUN yarn cache clean

#====================================
# assets ステージ
#====================================
FROM builder AS assets

COPY . .
COPY --from=bundle $APP_HOME/$BUNDLE_PATH $APP_HOME/$BUNDLE_PATH
COPY --from=yarn $APP_HOME/node_modules $APP_HOME/node_modules

RUN bundle exec rails assets:precompile

#====================================
# poppler ステージ
#====================================
# PDFを変換する時にエラーが発生していた（https://freee-sign.slack.com/archives/C016F6BPX6Z/p1668410696243969）
# 原因は、PDF変換に使うライブラリ（poppler-utils）のバージョンが足りていない（Herokuの時よりも下がっていた）ことにあった。
# ベースとなるRubyで利用しているOS（Debian bullseye）でインストールできるPopplerのバージョンが20.09までらしい
#   Debian bullseye poppler-utils (https://packages.debian.org/ja/bullseye/poppler-utils)
# そこで、Popplerをスクラッチでインストールすることにした。
FROM builder AS poppler

COPY . .
RUN apt-get -y update \
  && apt-get -y install \
    cmake \
    checkinstall \
    poppler-data \
    libfreetype6-dev \
    libfontconfig1-dev \
    libjpeg-dev \
    libtiff-dev \
    libopenjp2-7-dev \
    libcairo2-dev \
    libnss3-dev

RUN mkdir poppler \
  && curl -L https://poppler.freedesktop.org/poppler-21.05.0.tar.xz | tar xJ --strip-components=1 -C poppler \
  && cd poppler \
  && mkdir build \
  && cd build \
  && cmake .. \
    -DCMAKE_INSTALL_PREFIX=/poppler_build \
    -DCMAKE_BUILD_TYPE=release \
    -DBUILD_GTK_TESTS=OFF \
    -DBUILD_QT5_TESTS=OFF \
    -DBUILD_CPP_TESTS=OFF \
    -DENABLE_QT5=OFF \
    -DENABLE_LIBOPENJPEG=openjpeg2 \
    -DHAVE_CAIRO=ON -DENABLE_NSS3=ON \
  && make -j2 \
  && checkinstall -y --backup=no --fstrans=yes --pkgname=poppler --pkgversion=21.05.0

#====================================
# jemalloc ステージ
#====================================
FROM base AS jemalloc

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN DEBIAN_FRONTEND=noninteractive \
    apt-get update -qq && \
    apt-get install -y --no-install-recommends libjemalloc2

#====================================
# runner ステージ
#====================================
FROM base AS runner

LABEL org.opencontainers.image.title="freee-sign" \
      org.opencontainers.image.description="freee-sign production" \
      org.opencontainers.image.url="https://github.com/sight-visit/ninja-sign" \
      org.opencontainers.image.source="https://github.com/sight-visit/ninja-sign" \
      org.opencontainers.image.vendor="K.K. SIGHT VISIT"

RUN curl -sSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor -o /usr/share/keyrings/postgres-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/postgres-archive-keyring.gpg] https://apt.postgresql.org/pub/repos/apt/" bullseye-pgdg main "$PG_MAJOR" | tee /etc/apt/sources.list.d/postgres.list > /dev/null

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
      bash \
      build-essential \
      default-jre \
      fonts-noto-cjk \
      fonts-noto-cjk-extra \
      fonts-ipafont \
      fonts-ipaexfont \
      git \
      ghostscript-x \
      libpq-dev \
      libfreetype6-dev \
      libjpeg-dev \
      libpng-dev \
      libreoffice \
      libxext-dev \
      libxml2-dev \
      libx11-dev \
      pdftk-java \
      pkg-config \
      postgresql-client-"$PG_MAJOR" && \
    apt-get clean && \
    rm -rf /var/cache/apt/archives/* && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    truncate -s 0 /var/log/*log && \
    mkdir -p "$APP_HOME"/tmp/sockets "$APP_HOME"/tmp/pids "$APP_HOME"/log && \
    useradd --user-group app && \
    chown -R app:app "$APP_HOME" && \
    # libreofficeが使用ユーザーのホームディレクトリに一時ディレクトリやファイルを吐き出すので作成して所有者を変更している
    # /freee-sign/tmp等をユーザーのホームディレクトリに書き換えるのでも良さそうだけど、まずは標準的なやり方を選択した。この箇所が原因でなにか不具合があればホームディレクトリの向き先変更を試すと良さそう
    mkdir -p /home/app/.cache/dconf && \
    chown -R app:app /home/app

# imagemagickをインストール
RUN git clone --depth 1 -b $IMAGE_MAGICK_VERSION --single-branch https://github.com/ImageMagick/ImageMagick.git \
  && cd ImageMagick \
  && ./configure \
  && make -j4 \
  && make install \
  && ldconfig /usr/local/lib

COPY .fonts/noto_sans_jp/ /usr/share/fonts/truetype/noto_sans_jp/
COPY .fonts/noto_serif_jp/ /usr/share/fonts/opentype/noto_serif_jp/
COPY .fonts/arimo/ /usr/share/fonts/truetype/arimo/
COPY .fonts/tinos/ /usr/share/fonts/truetype/tinos/
RUN fc-cache -f -v

COPY . ./

# wkhtmltopdf_binary_gem において下記を実行するために app:app に権限を与えている、もっと厳密でも良い気がする
# $APP_HOME/$BUNDLE_PATH/ruby/${rubyのversion}/gems/wkhtmltopdf-binary-${gemのversion}/bin/wkhtmltopdf_debian_10_amd64
# https://github.com/zakird/wkhtmltopdf_binary_gem
COPY --chown=app:app --from=bundle $APP_HOME/$BUNDLE_PATH $APP_HOME/$BUNDLE_PATH

COPY --from=assets $APP_HOME/public/assets $APP_HOME/public/assets
COPY --from=assets $APP_HOME/public/packs $APP_HOME/public/packs
COPY --from=poppler /poppler_build /usr
COPY --from=jemalloc /usr/lib/x86_64-linux-gnu/libjemalloc.so.2 /usr/lib/x86_64-linux-gnu/libjemalloc.so.2

# Rubyのメモリアロケーターを jemalloc に変更
ENV LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libjemalloc.so.2

# ECS 上で nginx と UNIX ドメイン連携を可能にする
VOLUME $APP_HOME/tmp

USER app

CMD ["bundle", "exec", "puma", "-C", "config/puma.rb"]
