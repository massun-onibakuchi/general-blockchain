## ファイアウォール
インターネットの外から内部ネットワークやコンピュータに対する不正アクセスをブロックする仕組み

- OSにはデフォルトで組み込まれている
- セキュリティの基本
- 外部からだけではなく、内部からの不審な発信もブロック

### 制御の仕組み　どのようにブロックする通信とそうしない通信を判別するか

 - ポート制御をする
 送信元や送信先のIPアドレス、ポート番号などにより、その通信を許可するか拒否するかを定義
 - ファイアウォールは許可した通信の内容については関知しない
> 「ポート番号」とはシステムに複数ある入り口にそれぞれ割り振られた番号の事です。一国の港のような役割を持つため「ポート(港)番号」と呼ばれているのです。
港で例えると、「アメリカから日本へ、横浜港への入港は許可するが、神戸港への入港は許可しない」「イギリスから日本へは神戸港への入港のみ許可する」というようにアクセスを制御することが可能です。
ただし、ファイアウォールは、アクセスの内容までは制御しません。例えばアメリカから日本へ、横浜港を介した入港であればどんな荷物を持っているかまではチェックせずに許可してしまうというわけです。

https://www.shadan-kun.com/blog/measure/2775/

## WAF Web Application Firewall
Webアプリケーション（webページとか）の脆弱性を悪用した攻撃を検出・防御する．
普通のファイアウォールでは通信の内容まで関知しないため、SQLインジェクション,クロスサイトスクリプティングなどの手法で攻撃を試みられると防げない  

**=>WAFなら通信の内容まで確認し、脆弱性を突いた攻撃からシステムを保護**

SQLインジェクション；お問い合わせフォームやログイン画面のID・パスワードの入力欄にSQL文を混ぜて送信（インジェクション、injection）することで、データベースを不正に操作する攻撃手段．　　  
SQL(Structured Query Language)は、データベースを操作するための言語

### 制御の仕組み
シグネチャ；アクセスのパターンを定義したもの   
このパターンに一致するアクセスがあった場合に通信を許可または拒否を行う

https://ds-b.jp/ds/publics/index/262/#block2863
https://www.atmarkit.co.jp/ait/articles/1603/07/news001_2.html

## VPN  　Virtual Private Network
インターネットや多人数が利用する閉域網を介して、暗号化やトラフィック制御技術により、プライベートネットワーク間が、あたかも専用線接続されているかのような状況を実現するもの

専用線；物理的に2点間を、 他のユーザーなどと共有しない専有ネットワークとして接続すること

送信する時にデータを暗号化してインターネット上に流し、受信する側は、受け取ったデータを復号化して確認する．つまり、インターネットを介して通信しているにもかかわらず、専用線でつないで通信しているのと同じような状態

https://www.hs-juniperproducts.jp/check/img/vpn/img_vpn02.gif

https://www.ntt.com/content/dam/nttcom/hq/jp/business/services/network/vpn/vpn/vpn01/img/vpn02_11.png

https://www.nic.ad.jp/ja/newsletter/No67/0800.html

### プラットフォーム
https://www.google.com/search?client=safari&rls=en&q=プラットフォーム&ie=UTF-8&oe=UTF-8

https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=4&cad=rja&uact=8&ved=2ahUKEwiyyLG-qd7jAhXqGKYKHVAiAFYQFjADegQIARAB&url=https%3A%2F%2Fja.wikipedia.org%2Fwiki%2F%25E3%2583%2597%25E3%2583%25A9%25E3%2583%2583%25E3%2583%2588%25E3%2583%2595%25E3%2582%25A9%25E3%2583%25BC%25E3%2583%25A0&usg=AOvVaw1qY5BdNBLbFy1al-OC9NHq


## Introduction To Ethereum Scaling

#### Scalability
:システムの規模（スケール）の変化に柔軟に対応できる度合いのこと.拡張可能性．拡張性

たとえば必要な処理能力が増えたときに、システム全体を変更することなくサーバなどのリソースを追加するだけで対応できるのであればスケーラブルなシステムです。 一方で、リソースを追加できなかったり、あるいはリソースを追加しても処理能力を拡大できないシステムは、スケーラブルではありません
>https://medium.com/uniqys/blockchain-scalability-d95ee27c5092

規模の増大に対応するスケーラビリティを考慮する際には、「垂直」と「水平」の観点から捉えることができる．

 - 垂直スケーラビリティ：システムの単一のノードにリソースを追加する方法で、CPUやメモリの増強や、コンピュータ自体の買い換えなどを行うこと

 - 水平スケーラビリティ：システムにノードを追加することで、クラスタなどの並列コンピューティング技術を使用する

### On-Chain vs Off-Chain Scaling

 1. State Channels
 1. Plasma
 1. Sharding



### 参考
https://education.district0x.io/general-topics/ethereum-scaling/introduction-to-ethereum-scaling/


https://education.district0x.io/general-topics/ethereum-scaling/ethereum-scalability-trilemma/

