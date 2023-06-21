# はじめてのQuay.io

コンテナイメージの保管、管理、配布を行うレジストリーを、コンテナイメージレジストリーと呼びます。代表的なレジストリーサービスとして、Docker Hub、Quay.io、AWS Container Resistryがあります。本ハンズオンではRed Hat社の提供するコンテナイメージレジストリ「Quay.io」にアカウントを作成し、各自の環境からコンテナをpushしてみましょう。

Quayは「キー」と発音します（クエィじゃないです）


## Quay.ioでアカウント作成
Red Hatアカウントを持っていることが前提です。

(Red Hat アカウントの作成方法はUIが変更されやすいこともあり、特にガイドはありません。やり方は「Red Hatアカウント作成」などで検索してみてください。）

[https://quay.io](https://quay.io) にアクセスします。

右上のSIGN INをクリックしてください。

注) 「Try for Free on Cloud」をクリックしないで下さい

Red Hat アカウントの SSO ページが表示されたら、自身のRed Hat アカウントを入力して下さい。

**DO180 Appendix に Red HatアカウントやQuay.ioの設定方法が記載されているので、そちらを参照下さい。**

## Quay.io にサインインする
コンソール作業に戻ります。loginコマンドでサインインします。
```
podman login quay.io
```

ユーザ名とパスワードを求められるので、Quay.ioで設定した情報を入力します。

## Quay.io を使ってみよう

こちらのページをpodmanに置き換えて実行します。

https://docs.quay.io/solution/getting-started.html

### 新しいコンテナを作成する
コンテナイメージ ubuntu を実行し、新しいファイルを含むコンテナを新たに作成します。

```
podman run ubuntu echo "fun" > newfile
```
コンテナはすぐに終了します。psコマンドでコンテナIDを表示し、どこかに書き留めましょう。(次で必要になります)

```
podman ps -a
```

### コンテナーをイメージにタグ付けする
次に、コンテナーを既知のイメージ名にタグ付けする必要があります

```
podman commit <container id> quay.io/<username>/<reponame>
```

`<container id>`: 先ほど書き留めたコンテナID<br/>
`<username>`: Quay.io のユーザ名<br/>
`<reponame>`: 新しく作成したコンテナの名前


### コンテナイメージを Quay.io にプッシュする
```
podman push quay.io/<username>/<reponame>
```

これで、コンテナイメージがコンテナレジストリに登録されました。

### 登録されたコンテナイメージを確認しましょう

ブラウザで以下にアクセスしましょう
https://quay.io/repository/

Quay.ioにRed Hatアカウントでログインすると、自分のアカウントのリポジトリが参照できます。  
そこに、今回登録したコンテナイメージがあれば、登録は成功です。

### Quay.io からイメージをダウンロードする
他のユーザがpushしたイメージをpullしてrunしてみましょう！
