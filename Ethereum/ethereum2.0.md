## What is Ethereum?
[[1]Ethreum white paper](https://github.com/ethereum/wiki/wiki/%5BJapanese%5D-White-Paper)

 > [> district0x](https://education.district0x.io/general-topics/understanding-ethereum/what-is-ethereum/)  
 an open software platform built on blockchain technology that enables developers to build and deploy decentralized applications.  

Ethereum - 開発者がDappsをデプロイすることができるブロックチェーン技術から成るオープンソースプラットフォーム．World Computerをつくることを理念としている  

Ether - ethereumプラットフォームで使われ，Txを実行するために必要な燃料，通貨 

Smart contract - Ethereumブロックチェーンに記録されたプログラミングコード．ユーザーが十分なetherを伴ってスマートコントラクトを実行すると，そのコードがある意味自動で実行される．つまり，ユーザーがスマコンを実行しようとした場合，十分なetherやその他条件が満たされていれば，自動で契約－何らかのプログラミングコードの形をした－が実行される．  
例：自動販売機 欲しいジュースの支払いに十分な金額を入れ，ジュースのボタンを押すという条件を満たすと，ジュースを出すという契約がなされる．  

ブロックチェーン - ネットワーク(P2P,分散)上で互いに信頼しなくても何かを決定，合意に達することができる技術．数学や暗号技術によって参加者全員がある一つの状態(state)について合意できるようになっているデータベースでもありネットワークのシステム． ブロックチェーン(データ)は不特定多数の参加者(node)によってある一つの状態が共有，同期される．  
例：Bitcoin データ＝どのUTXOが誰のものであるかの金銭のリスト，要するに，これまでされてきたすべての支払いの金銭のデータがブロックチェーンに記録してあり，ネットワーク参加者全員（やや不正確な言い方か）がその同じデータ(ブロック)を共有して保持している．改ざんがないことを暗号技術的に自分で確かめることができ，改ざんを防ぐようなシステム  
Trustless Tracability Decentralised  

 > Blockchains are a way for many different people or computers to agree on something even though they don’t know or trust each other.  

# Ethereum2.0
eth2はいくつかのフェーズに分けられて段階的に移行する．
参考文献[1]より
> **Phase0**  
 Phase 0 is concerned with the beacon chain, the core of eth2, which manages validators and the coordination of shards. The beacon chain is the source of ground truth from which all other aspects of eth2 are bootstrapped.

 - ファイナリティ  
 いったんある操作が完了すると，もう取り消すことはできないこと
 金融における文脈では，決済が確定して，完了したこと．

 - 確率的ファイナリティ  
 BitcoinなどのPoWのブロックチェーンでフォークが発生した場合，ブロックが追加されていくと，いずれはフォークは淘汰されるため，あるブロックが含まれたチェーンが正当なチェーンとして生き残る確率はブロックが追加されていくほど高まる.(淘汰されたチェーンに含まれていたトランザクションは，まだ完了したことにならない．).しかし，あくまでもそれは確率的にそのブロックが正当なチェーンである確率が高まっていくにとどまる．そこで，eth2ではFFGによってチェーンにファイナリティを与える．

 - Validaters  
 PoWにおけるマイナーは，PoSにおいてはバリデーターという．ブロックの提案，検証などおこなう，  
 略  
 バリデーターは，Beacon nodeとバリデータークライアントの両方を必要とする[5]  

 バリデーターのstatus
  1. Activation Eligiblity
  2. Activation
  3. Exit
  4. Withdrable

 - シャーディング  
 Ethereumのスケーリングを向上させるためための手法の一つ．64のシャードチェーンにそれぞれノードを割り当て，他のシャードと並列処理で，トランザクションやスマコンのデータ，ステートを管理する．各シャードのブロック生成をBeacon Chainに選ばれたproposerが行う．  

 - Beacon Chain  
 Beacon Chainはバリデーターセットやattestaion，各シャードチェーンのステートを追跡する，また，バリデーターをcommitteというバリデーターのグループに分割したりも行う．Beacon chainが各シャードチェーンにファイナリティを与える．eth2の核となるチェーン．  

 - Proposer  
 BeaconChainによってランダムに選ばれたバリデーター．各slotに一人Beaconブロックを提案するproposer，各soltでシャードにブロックを提案するproposerがいる．   
 ProposerはCommittesのバリデーターを兼任する場合もある．

 - Crosslink  
 Beaconブロック内のシャードブロックへの参照のこと．各シャードはepoch？(約6分)ごとにそのステートをBeacon Chainにハッシュで記録される．このコネクションをcrosslinkという．つまり，シャードのslotが空の場合はcrosslinkは作成されないので，Beaconブロックには最大64個のcrosslinkを含めることができる．crosslinkによって，シャードはファイナリティを得る.Crosslinkは Cross-Shard Communicationするさいに必要とされる．  

 - CasperFFG  
 the Friendly Finality Gadget： EthereumのPoSライクなCasperの一種.FFGは，チェーンの成長のプロセスとは分離されたメカニズムであり，色々なチェーンに適用できるような汎用性がある．
 justification/finalizationの概念でファイナリティをチェーンに与える．finalizeされたブロックはチェーンの一部となり，ここまでのブロックは確定したものとなる．  

 - Committe  
 一人のバリデーターはBeacon ChainのRANDAOを通して，ランダムにひとつのバリデーターのグループに配属される．このバリデーターセットをCommitteといい，割り当てられた一つのslotの一つのシャード，Beaconで，提案されたブロックに対して投票（attestation）をおこなう．  
 最低128のバリデーターからなり，epochごとに，バリデーターをCommitteにランダムに配する．これにより，悪意のあるノードをシャード全体(たくさんのCommitte)に行き渡らせて(一箇所に集中させない)，シャード(Committe)が乗っ取られることを防ぐ.  

 - LMD GHOST  
 Latest Message Drivened GHOST:フォークチョイスルール．PoS仕様の[Greedy Heaviest Observed Subtree (GHOST)](https://eprint.iacr.org/2013/881.pdf).  
 ジェネシスブロックから始めて，フォークがあるたびに，LMD GHOSTはより多くの最新のメッセージ(attestaion)が支持するブロックまたは，その子孫ブロックのサブツリー側を選択する．これをリーフブロック（子を持たない）になるまで続ける．
 eth2では，CapserとGHOSTを組み合わせたプロトコルを用いる．  
 　  
 最後にfinaliseされたブロックはずっとチェーンの一部となっている．このことは，GHOSTはこのブロックより前のブロックは考える必要がないことをあらわす．  

 - Attestaions  
 Beaconやシャードブロックの正当性に対するバリデーターの投票のこと．票の重さはバリデーターのステイクの大きさでつけられる．バリデーターによって，attestaionはブロードキャスされる． 
 attestionは以下の票を含む．   
  [3]より  
   - a vote for the current beacon chain head  
   - a vote on which beacon block should be justified/finalised  
   - a vote on the current state of the shard chain  
   - the signatures of all of the validators who agree with that vote  

 - FFG votes  
 epochの終わりで前のCheckpoint(CasperFFGにおいて)から次のcheckpointにすべてのバリデーターが参加するattestionのこと．投票は（S,T)のフォーマットで，次の要素をもつ  
  - Source block (S)  
  - Traget block (T)，ただし，S の子孫である    
 　  
 Gasperのペーパーでは，chekpointはEpoch boundary blocks (EBB)という類義語に対応．  
　  
 全アクティブなバリデーターのステイク総額の2/3以上の票は supermajorityという．  
　
 - LMD GHOST votes  
 slotに割り当てられたバリデーターがBeceaon Chainのヘッドをattestすること．だから，slotごとに，投票しているブロックは同じでない．  

 - Aggregate Siggnature  
 同じCommitte内のバリデーターが同じLMD GHOST vote，FFG vote（つまり，同じ投票）をした場合，それらの署名は集約される．この集約した署名のことをAggregate signatueをいう．バリデーター全体の投票を調べることはCommitte全体での集約された票を調べることと実質的に同一であると考えられる．つまり，一旦，Committeという中間的なレベルのコンセンサスを取って票を調べる．こうすることで，効率的になって，遅延やスループットを損なうことなく，より多くのバリデーターをシステムに参加させられることができ，ネットワークの分散性が上がる．[6]  

 - Cross-Shard Communication  
 シャードが他のシャードのデータを参照したりするには，シャード間の通信の必要がある．これは，Beacon chainを介して行われる.[3]  

 - Slot  
 Proposerによってブロックが提案されるべき約12秒の期間．空になる事もある．

### 
> [1]より  
 we measure time in slots, defined as some constant number of seconds (tentatively 12 seconds ).   
 We then define an epoch to be some constant number C.The epoch j of slot i is ep(i) = j = ⌊ i ⌋.   
 In other words, the blocks belonging to epoch j have slot numbers jC + k as k runs through {0, 1, . . . , C − 1}.  

  
[[1]Ethereum Combining Ghost and Casper paper](https://arxiv.org/abs/2003.03052)  // 6 Mar 2020 (v1), last revised 12 Mar 2020 (this version, v2)  
[[2]d10nLab 公開用Ethereum2.0コンプリードガイド](https://drive.google.com/drive/folders/10Kk_KpcH20jZsPb-nW0W-wS4m8PTVL28?usp=sharing)  
[[3]Medium eth2 sharding](https://medium.com/chainsafe-systems/ethereum-2-0-a-complete-guide-scaling-ethereum-part-two-sharding-902370ac3be)  
[[4]Medium Ethereum 2.0 Terms Demystified](https://medium.com/alethio/ethereum-2-0-terms-demystified-8398357429d7) // 13 Aug,2019  
[[5]Ethereu Blog Validated: Staking on eth2 #0](https://blog.ethereum.org/2019/11/27/validated-staking-on-eth2-0/) // 2020  
[[6]Ethereu Blog Validated: Staking on eth2 #3](https://blog.ethereum.org/2020/03/27/sharding-consensus/) // 2020  

# eWasm
eWASM:Ethereum-flavored Web Assembly    

簡単にいうと，トランザクションの実行の仕組みをEVMコードをEVMで実行する仕組みから，eWasmというEthereumテイストのWebAssemblyのサブセットをeWasmVMで実行する仕組みに変えるプロジェクト． 


[[0]WebAssembly](https://developer.mozilla.org/ja/docs/WebAssembly/Concepts)  
[[3]Medium HashHubエンジニア白井さん　WebAssembly](https://medium.com/blockchain-engineer-blog/webassemblyとは-a75758785679)  
[[1]Qiita eWasm](https://qiita.com/ShuntaShirai/items/3ac92412720789576f22#ewasm)  
[[2]Gunosy ブロックチェーン ブログ](https://blockchain.gunosy.io/entry/2018/06/20/155726) 

**Ethereum Virtual Machine（EVM)**  
Ethereumのスマコンを実行するEthereum独自の仮想マシン．Solidityなどで書かれたコードがEVMバイトコードにコンパイルしてデプロイされている． 

**WebAssembly(Wasm)** 
ブラウザで実行できるバイナリコードの新しいフォーマット．仕様.Javascriptよりも高速に動き，コンパクト．プログラミング言語やライブラリの名前ではない．CやRustなどの様々な言語からもコンパイルできる．  

### EVMのよくない所 
一言で言うと，Ethereumの独自の仕様が，ボトルネックになってしまっている．  
文献[2]を見るといい 

### eWASM:Ethereum-flavored Web Assembly
Doc https://ewasm.readthedocs.io/en/mkdocs/README/  

eWasmによって改善される事 
 - パーフォマンスの向上　   
  gasをあらかじめ計算して，それをバイトコードに加える実装になるため，gasの処理がボトルネックになるのを防ぐ．  
 - WebAssemblyのエコシステムの利用  
    c，Go,Rustなどの言語が使えるようになる．WebAssemblyの利用できる，これにより，開発に参入しやすくなる． 

文献[2] 


