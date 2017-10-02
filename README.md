# Gogs on CentOS7 for Scaleway
Vagrant + Ansible で [Scaleway](https://www.scaleway.com/") にCentOS7のインスタンスを起動しGogsをセットアップします。

- CentOS7 インスタンスを起動
- CentOS7 基本設定
    - 基本ツール類導入
    - firewalld起動
- CEFSプロジェクト提供のエラッタに基づきセキュリティアップデート
- MySQL 5.7 導入
- Nginx 導入
- Gogs 導入

Scalewayのトークン(SCALEWAY_TOKEN)、組織ID(SCALEWAY_ORG)などの公開するべきではない情報は.envファイルで管理します。


## 使用 Vagrant Plugin
未導入の場合は導入してください。

```
$ vagrant plugin install vagrant-scaleway
$ vagrant plugin install dotenv
```

## 準備
```
$ git clone git@github.com:jmurabe/scaleway-centos7gogs.git
$ cd scaleway-centos7gogs
```

.env.exampleを参考に.envファイルを作成してください。

.env.example

```
#TARGET = 'centos/7'
TARGET = 'scaleway'
# for Scaleway
SCALEWAY_TOKEN = 'your Scaleway token'
SCALEWAY_ORG = 'your Scaleway organization id'
SCALEWAY_PKEY = '~/.ssh/id_rsa'
# for MySQL
MYSQL_ROOT_PW = 'validated password'
# for Gogs
GOGS_MYSQL_PW = 'validated password'
```

- TARGET
	- scalewayにするScalewayで、他だとbox名として扱いVirtualBoxで動作します。
- SCALEWAY_TOKEN
    -  Scalewayコントロールパネル、ユーザーのCredentialsで作成します。
- SCALEWAY_ORG
	- 組織ID
	- 確認方法の例: curl https://account.cloud.online.net/organizations -H "X-Auth-Token: SCALEWAY_TOKEN"
- SCALEWAY_PKEY
	- Scalewayに登録した公開鍵の秘密鍵を指定します。
- MYSQL_ROOT_PW
	- MySQLのrootユーザーのパスワード
- GOGS_MYSQL_PW
	- MySQLのgogsユーザーのパスワード

MySQLのパスワードはデフォルトでvalidate_passwordプラグインが有効になっている状態なので8文字以上で英大文字小文字数字記号の4種類を含めてください。

## 使用方法

インスタンス起動・初期設定

```
$ vagrant up
```

完了したら vagrant ssh-config 等でIPアドレスと確認してブラウザでアクセスします。

- MySQLのユーザをgogsに変更し、パスワードには.envのGOGS_MYSQL_PWに設定した文字列を入力
- ドメイン、アプリケーションのURLをIPベースで入力(とりあえず)。
     - NginxでリバースプロキシしているのでURLの3000番ポート指定ははずしてください。
- 「Gogsをインストール」をクリック

以上でGogsが起動します。最初に登録したユーザーが管理者となります。

細かい設定は vagrant ssh でssh接続して直接 /home/git/gogs/custom/conf/app.ini を編集して下さい。

```
[root@scw-****** ~]# su - git
[git@scw-****** ~]$ vi gogs/custom/conf/app.ini
[git@scw-****** ~]$ exit
[root@scw-****** ~]# systemctl restart gogs.service
```

## 参照
- [Scalewayに30分でGitHubクローンを建てるよ](http://www.lancard.com/blog/2017/10/02/scaleway%e3%81%ab30%e5%88%86%e3%81%a7github%e3%82%af%e3%83%ad%e3%83%bc%e3%83%b3%e3%82%92%e5%bb%ba%e3%81%a6%e3%82%8b%e3%82%88/)
- [jmurabe/scaleway-centos7base](https://github.com/jmurabe/scaleway-centos7base)
