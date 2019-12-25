# Spring Bootアプリの運用が楽になる？ PivotalとMicrosoftが共同開発した専用PaaS「Azure Spring Cloud
## 概要
* AzureでSpringに特化した専用PaaS「Azure Spring Cloud」を開発中、その便利な機能を紹介

### このセッションに関連するキーワード
* Azure
* Spring Cloud
* Kubernetes
* CICD
* DevOps

## セッションの内容
### 話の流れ
Spring専用PaaS「Azure Spring Cloud」がリリースされる。
現時点ではプレビュー版が10月から公開されている。
正式版の時期は公にはできないが、GW終わりごろには使えるだろうとのこと。

「Azure Spring Cloud」は　「Azure AD（Azure Active Directory）」から構築が可能。
Azure AD経由なのでGUIベースで簡単に構築可能で、セキュアな構成が可能。
CUIからコマンドベースでの構築にも対応。

Azure DevOpsも利用すればBoards, Pipeline, Repos, Test Plan, Artifacts などなど様々なCICD向けの機能と連動可能。

さらにクラウドのサーバにAzure Red Hat Openshiftを使えばREHLとMS両社でパッチ適用、更新、監視を行うので開発に専念可能。

Azure Spring CloudはKubernetesで動作しているが、Kubernetesの知識なしで作業可能になっている。
むしろKubernetesを意識させない作りになっており、Kubernetesとしての操作コマンドを受け付けないように統制されている。

Tomcatなどを立ち上げたときには外部アクセス可能なURLは必要に応じてGUI上からワンボタンで払い出される。
またblue-green-deploy対応で、アプリケーションを更新しても古い画面を公開可能で、こちらもGUI上から公開資材の新旧を切り替え可能。

GitHub上に専用のYMLファイルを格納し、連動することで、YMLファイルにパイプラインを記述でき、CICDを構成可能。

### 考察したこと
* Azureのサービスの利便性が表立っていて、インフラ寄りのセッションであった。
  * Spring Cloudで作ったアプリがこんなサービスを使って運用可能ですということがメインのセッションだったのだと思う。
* blue-green-deploy対応がサービスのGUI上から触れるのは便利に見えた。
  * AWSではサービスだけで同一URLに動的な資材を新旧で公開するのは難しいように思う。可能だとしても複数のサービスにまたがって設定が必要。

### 新たに知ったこと
* AzureではWebコンソール側から簡単にコマンドを発行してサーバ内のプロセスを実行可能、SSHの必要性などが薄い作りになっている。
* Azure ADなどで様々なPaaSやSaaSをすぐに利用可能であり、今試したい既存サービス（boxやJenkinsのような）を気軽に構築可能というAWSにはない魅力が多く存在した。

## 疑問点(記載したければ)
* Kubernetes上で動いているという話は裏話的な部分だったのか？
  * Kubernetes的な操作を受け付けない環境で、ユーザもKubernetesを意識せずに構築可能であるから、そもそもKubernetesの話は表に出てこないと思われる。
* Azure Red Hat Openshiftは確かに便利そうだが、アプリケーションのバージョンは勝手に更新されてしまうのか？　そうならばあるタイミングごとに強制的に改修を強いられそう。
  * 先端技術はマイグレーション頻度が激しいので気になる箇所ではないのかもしれない。むしろセキュリティが向上して便利なのかも？

## 講義資料URL
https://www.slideshare.net/tyoshio2002/jjug-ccc-2019-fall-azure-spring-cloud
