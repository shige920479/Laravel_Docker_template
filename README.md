🐳 Laravel × Docker 開発環境テンプレート
==============================

_WSL2 + Docker Compose / Nginx / PHP-FPM / MySQL / Redis_

* * *

🚀 概要
-----

このプロジェクトは **WSL2（Ubuntu）＋ Docker Compose** を前提とした、  
**実務レベルの Laravel 開発環境テンプレート**です。

Laravel プロジェクトを毎回ゼロから構築する手間をなくし、  
安定した開発環境をすぐに立ち上げられるように設計されています。

* * *

✨ 特徴（Why このテンプレート？）
-------------------

*   Nginx / PHP-FPM / MySQL / Redis / phpMyAdmin 付き
*   developer ユーザー（UID=1000）に統一 → **権限問題ゼロ**
*   Laravel / Vite / Tailwind すべて対応
*   WSL × Windows の npm / node PATH 問題を完全回避
*   MySQL 設定（my.cnf）をプロジェクト側で管理
*   実務でもそのまま使える Dockerfile 構成
*   複数 Laravel プロジェクトのベースとして再利用可能

* * *

📁 ディレクトリ構成（概略）
---------------

```
laravel_crm/
├── docker/
│   ├── php/        # PHP-FPM の Dockerfile / php.ini
│   ├── nginx/      # Nginx 設定
│   ├── mysql/      # MySQL 設定（my.cnf）
│
├── src/            # Laravel 本体が入る場所
├── docker-compose.yml
├── .env            # 環境変数（docker用）
```

* * *

🧱 設計思想（Architecture Overview）
------------------------------

### **1\. Laravel の生成はホスト（WSL）で実施**

*   vendor / node\_modules の権限問題を完全回避
*   WSL での composer / npm の高速処理を活かす

### **2\. コンテナ内ユーザーを developer（UID=1000）に統一**

*   ホストとコンテナでファイル所有者が一致
*   権限トラブルが発生しない

### **3\. storage / cache の権限自動調整**

Laravel が必要とする書き込み権限を Dockerfile で付与

### **4\. MySQL 設定をプロジェクト内で管理**

*   my.cnf をバージョン管理できる
*   チーム全員で同じ DB 設定を再現可能

* * *

🐳 使用サービス
---------

| サービス | 説明 |
| --- | --- |
| **PHP-FPM（8.3）** | Laravel アプリケーション実行 |
| **Nginx（1.25）** | Web サーバー / Vite プロキシ |
| **MySQL 8** | データベース |
| **Redis** | セッション / キャッシュ / Queue |
| **phpMyAdmin** | MySQL GUI 管理ツール |

* * *

🟢 開発開始手順（Quick Start）
----------------------
### **事前\. .envファイルの編集**

```sh
.envファイルを作成し環境変数を設定
```

### **事前\. docker-compose-ymlの編集**

```sh
networks名やコンテナ名などをプロジェクトに合わせて命名
```

### **1\. Docker イメージのビルド**

```sh
docker compose build --no-cache
```

### **2\. コンテナ起動**

```sh
docker compose up -d
```

### **3\. Laravel のインストール（ホスト側で実行）**

```sh
composer create-project laravel/laravel src
```

### **4\. 初回のみ：書き込み権限調整**

```sh
docker compose exec php bash -c "chmod -R 775 storage bootstrap/cache"
```

* * *

🧪 動作確認
-------

| URL | 内容 |
| --- | --- |
| [http://localhost:8080](http://localhost:8080) | Laravel アプリ |
| http://localhost:8081 | phpMyAdmin |

* * *

📝 日常の開発フロー
-----------

### ✔ composer require

→ **ホスト（WSL）で実行するのがベスト**

### ✔ npm install / build

→ **必ずホスト（WSL）で実行する**

理由：Windows の npm が混入すると壊れるため

### ✔ artisan migrate など Artisan 系

→ **コンテナ内で実行**

```
docker compose exec php php artisan migrate
```

* * *

📦 このテンプレートの使い方（再利用方法）
----------------------

1.  新規プロジェクト作成時に clone
2.  `src/` を空にして Laravel を再生成
3.  そのまま開発開始