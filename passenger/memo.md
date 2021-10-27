# Passenger
- `Passengerfile.json` はルート直下に置く - [参考](https://www.phusionpassenger.com/library/config/standalone/intro.html#location-of-passengerfile-json)
  
- `--nginx-config-template`
  - nginxの設定ファイルを指定できる
  - 
    ```
    $ passenger start --nginx-config-template nginx.conf.erb
    ```
  - めんどい時は`Passengerfile.json` で指定しておけば良い - [参考](https://www.phusionpassenger.com/library/config/standalone/reference/#--nginx-config-template-nginx_config_template)

- `--debug-nginx-config`
  - nginxの設定ファイルを実際に出力してくれる

- 設定の方法
  - > 1. The first is through command line arguments passed to the passenger start command.
    >2. The second is through environment variable (since 5.0.22).
    >3. The third is through the configuration file Passengerfile.json.
    >4. There is also a fourth, more powerful but also more complicated mechanism. Namely configuration through an Nginx configuration template. This is reserved for more advanced uses.

- 設定の優先順位
  - >This information only applies to Passenger 5.0.0 and later.
    Configuration sources are respected in the following precedence (ordered from most to least precedence):
    >- Command line options.
    >- Environment variables (only since 5.0.22).
    >- Passengerfile.json.
## 良い感じの資料
- [nginxで設定する時のreference](https://www.phusionpassenger.com/library/config/nginx/reference/) 

- [standaloneで設定する時のreference](https://www.phusionpassenger.com/library/config/standalone/reference/) 