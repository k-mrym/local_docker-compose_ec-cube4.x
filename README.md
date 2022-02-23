# docker-compose にて ec-cube4系 のローカル環境構築

## html 配下に ec-cube アプリケーションDirを展開

## ssl化

 OnSSL.md 参照

## ビルド

 $ docker-compose up -d --build

## コンテナ内へ

 $ docker exec -it *ec-cubeコンテナ* bash

## composer.json のライブラリをインストール（vendor/配下の生成）

 $ composer install

## インストールスクリプトを実行（{}内は環境に応じて設定）

 $ bin/console eccube:install
 Database Url
 >mysql://{user}:{pass}@{host_mysql}/{name_db}
 Mailer Url
 >smtp://{smtp}:{1025}
 Auth Magic
 >{AuthMagic}

## envファイルの設定を確認

## DBの確認
 - migration 実行等

 $ bin/console doctrine:migrations:migrate


## プロキシ生成

 $ rm -rf app/proxy/entity/src

 $ composer clear-cache

 $ composer dump-autoload
 
 $ bin/console eccube:generate:proxies
 
 $ bin/console cache:clear --no-warmup

または

 $ rm -rf /var/www/html/var/cache/*