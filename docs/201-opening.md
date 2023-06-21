## 重要なポイントを10分で理解する

先日の座学パートの復習となりますが、コンテナオーケストレーションツールについて10分で復習していきます。  


第一部のハンズオンでは、Podmanを使いコンテナの稼働を体験いただきました。DBコンテナ＆アプリコンテナの2つだけの場合はコンテナの管理は容易です。しかし、コンテナが大量になるにつれて管理が大変になることは想像に難くないでしょう。なんとGoogle社では、すべてのサービスがコンテナとして稼働しており20億個以上のコンテナがあるそうです(2014年時点で！)。一体どうやって20億ものコンテナを管理しているのでしょうか・・・？  

コンテナーが大量になってくると管理しきれない部分  
- 複数のコンテナホストの管理
- 死活監視
- スケジューリング
- スケーリング
- 障害時のリカバリー

正解は<span style="color: orange; "> **「コンテナ・オーケストレーションツールを活用する」**</span>です。  

オーケストレーションツールにはKubernetes、Docker Swarm、Apache Mesosなどがあります。その中でもデファクト的な存在が "**Kubernetes**" です。Kubernetes (k8s または「kube」とも呼ばれる) は、もともとGoogle社が "Borg" という名前で自社のコンテナを管理していた仕組みがベースとなっています。2015年にCloud Native Computing FoundationにKubernetesプロジェクトとして寄贈されました。OSSなので自前で構築することもできますが、各Public Cloudがマネージド・サービスも提供しています。（ex. [IBM Cloud Kubernetes Service](https://www.ibm.com/jp-ja/cloud/kubernetes-service)）  

エンタープライズ向けKubernetesとして、長期サポートや開発者向けの便利ツール、CI/CDの強化、脆弱性対策など企業向けのセキュリティー強化を行ったものが Red Hat OpenShiftです。2つの違いをより理解したい！という場合は[Blog Red Hat OpenShift と Kubernetes の違い](https://www.ibm.com/blogs/solutions/jp-ja/container-cocreation-center-05/)をご参照ください。


簡単にまとめると、


ここからはKubernetesの基本として、抑えておくべき単語を見ていきましょう。


