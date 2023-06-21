# APサーバコンテナとDBサーバコンテナを連携して動作させる

## Redmineの構築
RedmineというOSSのプロジェクト管理ツールがありますが、Container導入が案内されています。

詳細は[redmine - Official Images](https://hub.docker.com/_/redmine)を参照いただくとして、ここでは簡単に方法をご紹介します。

今回は、「Run Redmine with a Database Container」という方法でRedmineを導入してみます。

### ネットワークの導入
実行中のコンテナはIPアドレスを保有しており、コンテナ間で通信を行うことができます。コンテナホスト内に保護されたコンテナ専用のネットワークを利用して、アプリケーションのコンテナからデータベースコンテナにアクセスすることができるのです。さらに、アプリケーションをコンテナホストの外側のネットワークに公開することもできます。

まず、podman ネットワークの状況を確認するコマンドは、以下です。
```
podman network ls
```
結果は以下の通り、podmanネットワークのみが存在している状態です。コンテナは、デフォルトでこのpodmanネットワークに属します。
```
$ podman network ls
NETWORK ID    NAME        DRIVER
2f259bab93aa  podman      bridge
```


```
podman run -d --name docker-nginx -p 8080:80 docker.io/nginx
```
ここでは、以下の説明をしていました。<br/>
`8080ポートでnginxというWebサーバが起動しhttpリクエストを8080ポートで待っている状態です。 ブラウザで　http://localhost:8080 にアクセスしてください。nginxのデフォルトページが表示されます。`<br/>
正しくは、nginxはbridgeドライバであるpodmanネットワークに属し、デフォルトの80ポートでリクエストを待っている状況です。"-p"オプションはpodman ネットワークのゲートウェイの8080ポートをnginxコンテナの80ポートにバインドしていることを示しています。これにより外部から8080ポートでアクセスすると、nginxコンテナの80ポート(=nginxコンテンツ)を参照できるのです。

`podman ps`を見ると以下の表記になっています。"PORTS"のところで、8080ポートを80/tcpポートにバインドしていることがわかりますね。
```
$ podman ps
CONTAINER ID  IMAGE                           COMMAND               CREATED     STATUS      PORTS                 NAMES
6465fe425e17  docker.io/library/nginx:latest  nginx -g daemon o...  6 days ago  Up 6 days   0.0.0.0:8080->80/tcp  docker-nginx
```

では、特定のコンテナ同士のみが同じネットワークに属して会話を行いたい、という場合はどういった方法が取れるでしょうか。
podmanではユーザ定義のネットワークを作成することができます。これにより、新たなIPネットワークのアドレス範囲が設定され、他のコンテナネットワークとは隔離されます。

では、早速作成してみましょう。今回はRedmine用ネットワーク「r-nw」を生成します。このネットワークには、Redmineで利用するコンテナのみがアクセスします。
```
podman network create r-nw
```

すぐに作成されたことが確認できます。このネットワークへは、コンテナ起動(run)のオプションで`--network r-nw`を指定することで接続できるようになります。

### データベースの導入
データベースとして、PostgreSQLを導入します。ネットワークはr-nwを指定します。  
以下コマンドを実行してください。
```
podman run -d --name red-postgres --network r-nw -e POSTGRES_PASSWORD=secret -e POSTGRES_USER=redmine postgres
```
デタッチモードで常時起動にしています。また、"-e"は環境変数としてコンテナに変数を渡す際に使うオプションです。ここでは、PostgreSQLのユーザ名とパスワードを渡しています。(どんな環境変数を渡せるかは、コンテナイメージ作成者が決定します。大体、コンテナレジストリに説明があるのでそちらを確認しましょう。)

### Redmineの導入
続けて、Redmineを導入します。<br/>
ネットワークはr-nwを、データベースを利用するために、DBの情報を環境変数として渡しています。<br/>
ゲートウェイの8081ポートをRedmineの3000ポートにバインドしています。(redmineは3000ポートでアプリケーションを公開しています)
```
podman run -d --name red-redmine --network r-nw -e REDMINE_DB_POSTGRES=red-postgres -e REDMINE_DB_USERNAME=redmine -e REDMINE_DB_PASSWORD=secret -p 8081:3000 redmine
```

では、ブラウザでhttp://localhost:8081 にアクセスしてみましょう。Redmineのそっけないページが表示されましたか？
1. デフォルトの管理者ユーザ名とパスワードは両方`admin`です。右上の「ログイン」からログインしてみてください。
2. パスワードを変更してください。なんでもOKです。
3. 適当に操作して、永続化必須な操作をしてみてください。例えば、「test1」プロジェクトを作成しましょう。
4. 一旦ログアウトし、postgresqlコンテナを残したまま、redmineアプリケーションコンテナだけ削除します。コマンドは以下です。<br/>
Hint) rm に"-f"をつけると、コンテナを停止することなくコンテナを強制削除することができます。`stop`から`rm`という手順が面倒な場合は、こちらもご利用ください。

```
podman rm -f red-redmine
```

5. 再度、redmineアプリケーションコンテナを起動します。コマンドは以下です。
```
podman run -d --name red-redmine --network r-nw -e REDMINE_DB_POSTGRES=red-postgres -e REDMINE_DB_USERNAME=redmine -e REDMINE_DB_PASSWORD=secret -p 8081:3000 redmine
```

管理者パスワードは変更したものでログインできましたか？先程作成したプロジェクトは消えていませんか？これらはDBにデータが保存されているから実現できています。


**今回は基本を体験いただくために実施いただきましたが、実運用では複数のコンテナを連携させるとき、個別にdocker runをし、連携するためにnetworkを用意し...終わったらそれぞれdocker stop, docker rmで削除しという作業は無無駄が多くヒューマンエラーが起きやすいことが想像できると思います。まとめて管理するためのDockerComposeというものを利用したり、Podという単位で複数コンテナをまとめたり、Kubernetesを利用してDeloymentという単位でまとめたり...という運用が基本になっています。DockerComposeは今回取り扱いませんが、第二部でOpenShiftをつかったシステム全体の管理をご体感いただきます。今時点では、コンテナは便利そうだけど一つひとつ立ち上げたり削除するのは大変そうだなーと思っておいてください。
...**

