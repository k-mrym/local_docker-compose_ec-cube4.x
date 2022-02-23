# docker-compose にて php-apache の SSL化

 - ローカルにDockerで立てたApacheでHTTPS通信を行います。

## server.key（秘密鍵）の作成

 $ cd .../app/ssl

 $ openssl genrsa -aes128 2048 > server.key


## server.csr（公開鍵＋認証局での署名に必要な情報）の作成

 $ openssl req -new -key server.key > server.csr

 - Common name : *IPアドレス* 又は *ホスト名 例：hoge.example.com*


## server.crt（サーバ証明書）の作成

 $ openssl x509 -in server.csr -days 3650 -req -signkey server.key > server.crt


## Apache起動時のパスフレーズ入力を省略(任意)

 $ mv server.key server.key.org

 $ openssl rsa -in server.key.org > server.key


## Dockerfile編集(対応済み)
 - Apacheに秘密鍵と公開鍵を含んだサーバ証明書を配置
 - Docker環境（Debian）の場合はdefault-ssl.conf

```
# SSL化
COPY ./ssl/server.crt /etc/ssl/certs/
COPY ./ssl/server.key /etc/ssl/private/
RUN sed -i 's!/etc/ssl/certs/ssl-cert-snakeoil.pem!/etc/ssl/certs/server.crt!g' /etc/apache2/sites-available/default-ssl.conf \
    && sed -i 's!/etc/ssl/private/ssl-cert-snakeoil.key!/etc/ssl/private/server.key!g' /etc/apache2/sites-available/default-ssl.conf
# HTTPS通信に必要なApacheのモジュール（mod_ssl）の有効化と、default-ssl.confの有効化（sites-enabledからのシンボリックリンクを貼る）
RUN a2enmod ssl \
    && a2ensite default-ssl.conf
```


## docker-compose.yml編集(対応済み)
 - 443ポートの解放

 ```
     ports:
      - "80:80"
      - "443:443"
 ```
