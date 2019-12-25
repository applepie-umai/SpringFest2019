# LINE公式アカウントのチャットシステムにおけるSpringおよびWebFluxの活用事例
## 概要
* LINE公式アカウントのチャットシステム「LINEチャット」を構築するにあたり、Spring WebFluxを利用した。その利点と導入時に困ったことを紹介。

### このセッションに関連するキーワード
* Spring WebFlux
* Spring WebMVC
* Server Sent Events

## セッションの内容
### 話の流れ
LINE公式アカウントとは企業/事業者向けに開設可能なLINEアカウントである。
ブロードキャストやターゲティング配信などの機能を有しており、一般ユーザ向けのチャットシステムとは異なる「LINEチャット」を利用してLINE公式アカウントと一般ユーザがチャットできる。

チャットシステムとして「LINEチャット」に求められるものは以下のものである。
* リアルタイム通信
* ユーザ操作起因のコアビジネスロジック
  * メッセージの送信、過去メッセージの取得、メッセージの検索、各種データのCRUD操作など
* LINEユーザが送信した画像・動画・音声の取得

#### リアルタイム通信
リアルタイム通信の実現については、検討時期にはPolling、Long Polling、Server Sent Events（SSE）、WebSocketの4択となっていたが、SSEを採用することにした。
また、採用したSpring WebFluxはNon BlockingなWebフレームワークである。
1Request=1ThreadなServlet APIと異なり、1つのEvent Loop Treadで複数のRequestをさばけることから、サーバ台数を削減できる狙いがあった。

#### ユーザ操作起因のコアビジネスロジック
ユーザ操作起因のコアビジネスロジックの実現については、Spring WebMVCを採用した。
開発時期がSpring Boot 2.0.0.M1だったということもあったが、いきなりSpring WebFluxを全適用するのではなく、小さくて確実に向いているコンポーネントから試していこうという判断があった。

#### LINEユーザが送信した画像・動画・音声の取得
LINEユーザが送信した画像・動画・音声の取得については、Spring WebFluxを採用した。
画像・動画・音声ファイルは社内CDNにあり、認証等の事情によりアカウントオーナーにアクセスさせるにはProxyを掛ける必要があったが、Spring WebFluxのContent Proxyによりスレッドを占有することなくProxyを掛けることができた。

以下は開発時に困った点やノウハウのTips
* Netty OOMEの発生（Nettyに報告・改修確認済）
* Blocking DNS Resolver
* Reactor hooks with MDC
* BlockHound

#### Netty OOMEの発生（Nettyに報告・改修確認済）
WebFlux Clientを用いたコードでAPI callのtimeoutが大量に発生するとOutOfMemoryError（OOME）でサーバが落ちることがあった。
NettyのReadTimeoutExceptionがシングルトンであったため、ReadTimeoutExceptionが保持しているsuppressedExceptions(List<Throwable>)が肥大化してOOMEが発生した。
現在はIssueを起票して修正済である。

参考：
```
https://github.com/netty/netty/pull/9152
```

#### Blocking DNS Resolver
Reactor NettyではInetSocketAddressを使ったBlocking DNS Resolverを使用しているので、NettyのNon-Blocking DNS Resolverを使うように変更する必要がある。
また、ごくまれにTreadがハングしてしまう現象があったためIssueを起票済。（現在0.10.x向けBacklog）
また、デフォルトをNon-Blocking DNS Resolverとする案もReactor Nettyに上がっている。

参考：
```
https://github.com/reactor/reactor-netty/issues/710
https://github.com/reactor/reactor-netty/issues/569
```

#### Reactor hooks with MDC
ServletのようにMDCにRequest情報を渡してログに出力させたかったがWebFluxのEvent Loop Threadと相性が悪かった。
WebFilter、Reactor Context、Hooksを活用して何とか実現した。
* WebFilterでRequest情報をContextに入れる
* 各種onXXXXメソッド（onSubscribeなど）のラッパーメソッドを実装する
* ラッパーメソッドでContextからWebFilterで登録したRequest情報を取得する
* onXXXXメソッドを呼び出す前にMDC#putを実行し、呼出し後にMDC#removeする
* ラッパーメソッドが利用されるようにHooksに登録する

#### BlockHound
BlockHoundはNon BlockingスレッドにおけるBlockingコールを検出してくれるJavaエージェント。
あまり日本語で言及されている記事を見かけなかったので紹介。
GradleやMavenで依存関係に追加し、テストケースにてBlockHoundを有効化しておくと、テストケース実行時にBlockingコール発生部分でErrorを出力してくれる。
しばしば予期しないBlockingコールもあり、コードレビューで漏れることがあるので導入をお勧めする。

### 考察したこと
* チャットシステム全体に対してSpring WebFluxを全採用せず、部分部分で対応させていく判断は正解だと思った。
確かに検討時のSpring BootのバージョンがM1だったというのもあるだろうが、ノウハウを貯める意味も含めてある程度規模を絞って新技術を試していくのは比較的安全のはず。
* Non Blocking I/Oはうまく実装できればいろいろとCPUを節約した実装が可能のように思えた。実際セッション後に軽く検索を掛けたところリアクティブプログラミングがヒットした。
非同期処理で待ち時間を効率よく扱うという発想とSpring WebFluxのEvent Loop Treadが複数のRequestを捌けるのは同じような仕組みだろう。

## 講義資料URL
https://speakerdeck.com/line_developers/examples-of-using-spring-and-webflux-in-the-chat-system-for-line-official-accounts
