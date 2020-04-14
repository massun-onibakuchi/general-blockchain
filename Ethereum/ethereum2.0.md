## What is Ethereum?
https://github.com/ethereum/wiki/wiki/%5BJapanese%5D-White-Paper  

 > [district0x](https://education.district0x.io/general-topics/understanding-ethereum/what-is-ethereum/)  
 an open software platform built on blockchain technology that enables developers to build and deploy decentralized applications.  

 Ethereum - 開発者がDappsをデプロイすることができるブロックチェーン技術から成るオープンソースプラットフォーム．World Computerをつくることを理念としている  

 Ether - ethereumプラットフォームで使われ，Txを実行するために必要な燃料，通貨 

 Smart contract - Ethereumブロックチェーンに記録されたプログラミングコード．ユーザーが十分なetherを伴ってスマートコントラクトを実行すると，そのコードがある意味自動で実行される．つまり，ユーザーがスマコンを実行しようとした場合，十分なetherやその他条件が満たされていれば，自動で契約－何らかのプログラミングコードの形をした－が実行される．  
 例：自動販売機　欲しいジュースの支払いに十分な金額を入れ，ジュースのボタンを押すという条件を満たすと，ジュースを出すという契約がなされる．  

 > Blockchains are a way for many different people or computers to agree on something even though they don’t know or trust each other.  

ブロックチェーン - ネットワーク(P2P,分散)上で互いに信頼しなくても何かを決定，合意に達することができる技術．数学や暗号技術によって参加者全員がある一つの状態(state)について合意できるようになっているデータベースでもありネットワークのシステム．　ブロックチェーン(データ)は不特定多数の参加者(node)によってある一つの状態が共有，同期される．  
@例：Bitcoin　データ＝どのUTXOが誰のものであるかの金銭のリスト，要するに，これまでされてきたすべての支払いの金銭のデータがブロックチェーンに記録してあり，ネットワーク参加者全員（やや不正確な言い方か）がその同じデータ(ブロック)を共有して保持している．改ざんがないことを暗号技術的に自分で確かめることができ，改ざんを防ぐようなシステム  
Trustless Tracability Decentralised  

# Ethereum2.0
eth2はいくつかのフェーズに分けられて段階的に移行する．
参考文献[1]より
> **Phase0**  
 Phase 0 is concerned with the beacon chain, the core of eth2, which manages validators and the coordination of shards. The beacon chain is the source of ground truth from which all other aspects of eth2 are bootstrapped.

eth2では，CapserとGHOSTを組み合わせたプロトコルを用いる．
Casper FFGはファイナリティをもたらすツールで，LMD GHOSTはフォークチョイスルール．


> [1]より
 we measure time in slots, defined as some   
 constant number of seconds (tentatively 12
 seconds ). We then define an epoch to be some constant number C.The epoch j of slot i is ep(i) = j = ⌊ i ⌋. In other words, the blocks belonging to epoch j have slot numbers jC + k as k runs through {0, 1, . . . , C − 1}.

[[1]Ethereum Combining Ghost and Casper paper](https://arxiv.org/abs/2003.03052) // 6 Mar 2020 (v1), last revised 12 Mar 2020 (this version, v2)

# eWasm
WebAssemblyとは　　

プログラミング言語やライブラリの名前ではなく，ブラウザでプログラムを高速実行するための．「ブラウザで動くバイナリコードの新しいフォーマット．仕様」

https://qiita.com/ShuntaShirai/items/3ac92412720789576f22#ewasm

https://developer.mozilla.org/ja/docs/WebAssembly/Concepts
