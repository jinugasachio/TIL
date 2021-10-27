# Passenger
- `--nginx-config-template`
  - nginxの設定ファイルを指定できる
  - 
    ```
    $ passenger start --nginx-config-template nginx.conf.erb
    ```
  - めんどい時は`Passengerfile.json` で指定しておけば良い（[参考](https://www.phusionpassenger.com/library/config/standalone/reference/#--nginx-config-template-nginx_config_template)）

## 良い感じの資料
- [nginxで設定する時のreference](https://www.phusionpassenger.com/library/config/nginx/reference/) 

- [standaloneで設定する時のreference](https://www.phusionpassenger.com/library/config/standalone/reference/) 