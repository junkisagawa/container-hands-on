# OpenShift Localの実行

**公式ページ**：[Red Hat OpenShift Local](https://access.redhat.com/documentation/ja-jp/red_hat_openshift_local/2.19/html/getting_started_guide/index)

## Podmanの起動
**Red Hat OpenShift Local** は、OpenShift Container Platformおよび Podman コンテナーランタイム、Red Hat Device Edgeのプリセットを提供します。"crc set preset" コマンドで使用するプリセットを選択した後、*setup, start* コマンドを実行することで起動します。プリセット名はそれぞれ**openshift**、 **microshift** です。第一部 コンテナ研修ではPodmanを使用していくので、preset podmanを指定します。  

以下3つのコマンドを実行し、Podmanを起動してください。

```shell
crc config set preset podman
```

```shell
crc setup
```

```shell
crc start
```

<details>
<summary>実行例</summary>
```shell title='出力例'
❯ crc config set preset podman
Changes to configuration property 'preset' are only applied when the CRC instance is created.
If you already have a running CRC instance with different preset, then for this configuration change to take effect, delete the CRC instance with 'crc delete', setup it with `crc setup` and start it with 'crc start'.
❯ crc setup
INFO Using bundle path /Users/junki/.crc/cache/crc_podman_vfkit_4.3.1_arm64.crcbundle
INFO Checking if running as non-root
INFO Checking if crc-admin-helper executable is cached
INFO Checking for obsolete admin-helper executable
INFO Checking if running on a supported CPU architecture
INFO Checking minimum RAM requirements
INFO Checking if crc executable symlink exists
INFO Checking if running emulated on a M1 CPU
INFO Checking if vfkit is installed
INFO Checking if CRC bundle is extracted in '$HOME/.crc'
INFO Checking if /Users/junki/.crc/cache/crc_podman_vfkit_4.3.1_arm64.crcbundle exists
INFO Checking if old launchd config for tray and/or daemon exists
INFO Checking if crc daemon plist file is present and loaded
Your system is correctly setup for using CRC. Use 'crc start' to start the instance
❯ crc start
WARN A new version (2.19.0) has been published on https://developers.redhat.com/content-gateway/file/pub/openshift-v4/clients/crc/2.19.0/crc-macos-installer.pkg
INFO Checking if running as non-root
INFO Checking if crc-admin-helper executable is cached
INFO Checking for obsolete admin-helper executable
INFO Checking if running on a supported CPU architecture
INFO Checking minimum RAM requirements
INFO Checking if crc executable symlink exists
INFO Checking if running emulated on a M1 CPU
INFO Checking if vfkit is installed
INFO Checking if old launchd config for tray and/or daemon exists
INFO Checking if crc daemon plist file is present and loaded
INFO Loading bundle: crc_podman_vfkit_4.3.1_arm64...
INFO CRC instance is running with IP 127.0.0.1
INFO CRC VM is running
INFO Configuring shared directories
INFO Adding new bearer token for cockpit webconsole
podman runtime is now running.

Use the 'podman' command line interface:
```  

</details>
  


## Podman 動作確認
以下コマンドでPodmanが正常に動作しているかを確認します。Client情報やVersionが表示されれば正常動作しています。
```shell
podman version
```

### Hello World

podman runコマンドを入力し、コンテナを起動してみましょう。
podmanは指定された名前のイメージがローカル環境に存在するか確認し、存在しない場合はイメージリポジトリを探しにいきます。見つかった場合はローカル環境にpullし、イメージをもとにコンテナを起動します。
```
podman run hello-world
```

以下が出力されればOKです。
```
!... Hello Podman World ...!

         .--"--.
       / -     - \
      / (O)   (O) \
   ~~~| -=(,Y,)=- |
    .---. /`  \   |~~
 ~/  o  o \~~~~.----. ~~
  | =(X)= |~  / (O (O) \
   ~~~~~~~  ~| =(Y_)=-  |
  ~~~~    ~~~|   U      |~~
```
[Quay.io](https://quay.io/)からhelloという名前のコンテナイメージを取得(pull)、自分のpodman engine環境で実行（run）した結果です。

podmanのもぐらくん達を素敵なアスキーアートで表現していますね。  
もぐらに見えますが、実はセルキーというアザラシの妖精らしいです。ノルウェーの民間伝承で登場する神話上の動物で、陸にあがるときは「あざらしの皮」を脱いで人間人間に化けるのだとか...。

!!! success

    第一部コンテナを進めていく準備が完了しました！podmanの基本コマンドはdockerコマンドと共通です。ハンズオンの中で色々なpodman コマンドを実行していきますが、ご興味がある方は[参考: Podmanの基本を学ぶ](/container-hands-on/option1-podman/) を実施してみてください




