# Sharding＆Beacon Chain
Beacon ChainとShardingについてのメモ．

eth2はいくつかのフェーズに分けられて段階的に移行する．
EthereumやBitcoin，PoSについての土台があることを前提とする

[eth2についてはこちらを参照](https://medium.com/@chromaticcapital/eth2-for-dummies-11ff9b11509f)

[eth2のロードマップについてはこちらを参照](https://ethos.dev/ethereum-2020-roadmap/)

参考文献[0]は[1],[2]を集約したフレンドリーな解説で英語も優しめ　

以下の本編は主に参考文献[2],[0]の和訳．

Beacon Chainはバリデーターセットやattestaion，各シャードチェーンのステートを追跡する，また，バリデーターをcommitteに割り当てたりすることも行う．シャードチェーンはデータを保存するチェーンで従来のEthereumブロックチェーンに近い位置付けである．64のシャードチェーンを実装して並列処理する．Beacon　chainが各シャードチェーンにファイナリティを与える．

## ValidatorのAttestationの流れ

Proposerという選ばれたバリデーターが割り当てられたslotにブロックを生成する．バリデーターは割り当てられたシャードのコンセンサスに参加しているので，そのシャードのヘッドに投票できる．バリデーターはシャードヘッドをslotのBeaconブロックにリンクさせる．
## Sharding
簡単にいうと，今までひとつのブロックチェーンに読み書きしてきたけど，たくさんのチェーンで行えば，スケーリングの向上できるよね.  
名前はデータベースシャーディングに由来  

Generally, a shard Chain has a subset of nodes processing it. Virtual miners, validators, are assigned to shards, and only process and validate transactions in that shard (Chain).

Ethereum’s shards have a dynamic subset of nodes processing it block-by-block.

64個のShardsが予定される

## Beacon Chain
Beacon Chainはバリデーターセットとすべてのシャードのステートやattestationsを追跡する．attestationは，提案されているブロックへのattester(バリデータ)が行う投票である．
attestationはBeacon Chainに記録される．
Beacon nodeというBeacon Chain用のクライアントを用いる
## Sharding Consensus
  
> the beacon chain tracks the state of both the set of     
 validators and the shards. In practice this means that if you (periodically) follow what is happening on the beacon Chain, you will know enough to verify anything said to be happening within eth2. Trust, but verify.  
  
Beacon Chainはバリデーターセットとシャードの両方のステートを追跡する．実際に，Beacon Chainに起きていることを定期的に追えば，eth2で起きていると言われていることを検証するのに必要なだけの知識があることを意味する．  
  
PoSシステムが機能するために，誰がバリデーターであるかのコンセンサスと，それぞれのバリデーターの票がどれくらいの重みがあるかを把握したり，行動に対して適切に報酬や罰を与えるためにステイクがどれくらいであるかのコンセンサスが必要である．さらに，Beacon Chainはシャードの現在のステートを追跡するだけでなく，バリデーターに仕事を割り当てることで，shardを管理する．  
  
他のPoSシステムとeth2の異なる部分はプロトコルに参加できるバリデータの数である．eth2は comimitteeというバリデータのグループによって中間レベルのコンセンサスがなされるためにこれを可能にしている．Beacon Chainはランダムにバリデータをcommitteに割り当てる．一つのcommiteeの票は暗号化されて一つのattestationに集約される．つまりこれは，commitee全体の票を検証することは一つの票をチェックすることよりもわずかに労力を伴うに過ぎない．したがって，Beacon Chainの正当性をチェックするため，多くのバリデータの票を評価するために検討する必要があるのは，わずかな集約された署名だけである．  

 - slot    
 新しいブロックがチェーンに追加されることが予想される12秒の時間枠 
 基本的には，Beacon Chainに1ブロックとShradにブロックが追加されるが，slotは空になりうる

 - epoch     
 32スロット（6.4分）で構成され，その間，ビーコンチェーンはチェーンの維持に関連する計算を実行する．これには新しいブロックの正当化とファイナライズ，バリデーターへの報酬とペナルティの発行が含まれる．  
  
バリデーターはcommitteeに編成される．常に，各バリデーターは1つのビーコンチェーンと1つのシャードチェーンcommitteeのメンバーであり，エポックごとに1回だけattestationを行うように要求される．  
  
eth2の分割されたコンセンサスのセキュリティモデルは，committeeはバリデーターセット全体の事実上の正確な統計的表現であるという考えに基づいている．  
全体のバリデーターの33％が悪意のある状況である場合，これらの悪意のあるバリデーターが同じcommitteeに偏って入る可能性がある．セキュリティモデルにとっては惨事  
  
そこで，1．ランダムにcommitteeに割り当てる．2.committeeの最小人数(少なくとも128バリデータ)を決めることで，この問題を確率的に回避できる．  

シャードブロックに対する十分なattestaionが集められると，Beacon Chainのブロックに対してcrosslinkがつくられる．crosslinkは非同期のクロスシャードコミュニケーションに必要．

[[0]ethos.dev The Beacon Chain Ethereum 2.0 explainer you need to read first](https://ethos.dev/beacon-Chain/) //Feb 25 ,2019

[[1]Ethereum Blog Validated: Staking on eth2 #0](https://blog.ethereum.org/2019/11/27/validated-staking-on-eth2-0/) // November 27 ,2019

[[2]Ethereum Blog Validated, staking on eth2: #3 - Sharding Consensus](https://blog.ethereum.org/2020/03/27/sharding-consensus/) // March 27, 2020

[[3]Status Two Point Oh Justification and Finalization](https://our.status.im/two-point-oh-justification-and-finalization/)
