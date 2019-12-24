# Spring と GraalVM Native Image

## 概要

native imageの概要とSpring FWのサポートについて

### このセッションに関連するキーワード

* GraalVM native image
* Feature

## セッションの内容

### 話の流れ

GraalVMについて、というよりはGraalVMのnative imageについて。
ただし、early adopter technologyであり、来年のSpring FW 5.3リリース時に変更がある可能性があるため注意が必要。

（参考）
`https://www.graalvm.org/docs/reference-manual/native-image/`

GraalVM Native Imageを使用すると、JavaコードをNative Imageと呼ばれるスタンドアロンの実行可能ファイルに事前コンパイル（ahead-of-time AOTコンパイル）ができる。
それぞれの実行環境（OS）に合わせたライブラリ（macだとdylib、windowsだとdll）も作成が可能。
GraalVMは従来のJITコンパイルも可能

AOTコンパイルとはAOTはソースコードの静的解析でわかる範囲の情報
（エントリポイントからメソッド呼び出しをたどって得られる情報とか）だけでコンパイルを行う方法。
なので、実行時に生成されるようなものはできなかったり、苦手。
たとえば、リフレクションは単純なモノは可能だが、
複雑なモノは情報を事前に補足情報を設定することで可能になる。

ダイナミックプロキシ（IFを渡して実装クラスを実行時に生成）
これも単純なモノは可能だが、複雑なものは補助情報を渡してあげる必要がある。

JVMとの機能の差分は以下に公開されている。
`https://github.com/oracle/graal/blob/master/substratevm/LIMITATIONS.md`

* GraalVM Native Imageのメリット
  * JMVが不要
  * メモリフットプリントが小さい
  * 起動が速い
  * 実行環境に応じた共有ライブラリが作れる

* GraalVM Native Imageのデメリット
  * プラットフォーム毎にビルドが必要
  * 最適化について
  * コンパイルが時間かかる

Springも中でリレクションもダイナミックproxyたくさん使っている。
現時点だと、設定ファイルを作成しないと使えない。
ただ、Spring 5.3/Spring Boot 2.2に向けて4つのspring-graal-native-featureが作られている。

* Reflection Handler
* Dynamic Prooxy Handler  
  アノテーション使おうと思うなら必要。
* initialization Handler  
  Beanの初期化タイミングを定義
* Resouorce Handler
　リソースの取得についての定義  
　コンポーネントスキャン

### 考察したこと  

* マイクロサービス化で機能が独立して起動するようになったことからJavaの起動に時間がかかることがネックになって、GraalVMのような技術が必要となってきている。
* 別の講義で、エンタープライズこそマイクロサービスで開発することで市場のニーズに迅速に答え、競争していける。一つのシステムを持ち続けるのは技術的負債になっていく、という話があったが、まだ世界的に見てもマイクロサービス化は発展途上。
今後増えていくと思われるため、必要な技術の流れを抑えていくことがスキルアップの方向性を考える上でも大事だと考えた。

### 新たに知ったこと

* AOTコンパイルというものがあること
* （講義と直接関係ないですが）Spring FWの「[Petclinic](https://github.com/spring-petclinic/spring-framework-petclinic)」というアプリケーションがベンチマークによくつかわれること  

## 疑問点(記載したければ)

* native imageのメリットはマイクロサービスなど、実際に動かさないと実感できなさそう。

## 講義資料URL

`https://www.slideshare.net/TakuyaIwatsuka?utm_campaign=profiletracking&utm_medium=sssite&utm_source=ssslideview`
