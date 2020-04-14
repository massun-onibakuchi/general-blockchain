# Sharding＆Beacon chain
Beacon ChainとShardingについてのメモ．
eth2はいくつかのフェーズに分けられて段階的に移行する．
EthereumやBitcoin，PoSについての土台があることを前提とする

eth2についてはこちらを参照
　https://medium.com/@chromaticcapital/eth2-for-dummies-11ff9b11509f

eth2のロードマップについては　https://ethos.dev/ethereum-2020-roadmap/


参考文献[0]は[1],[2]を集約したフレンドリーな解説で英語も優しめ　

以下の本編は主に参考文献[0],[2]を参考にした和訳．
## Sharding
簡単にいうと，今までひとつのブロックチェーンに読み書きしてきたけど，たくさんのチェーンで行えば，スケーリングの向上できるよね.
名前はデータベースシャーディングに由来

Generally, a shard chain has a subset of nodes processing it. Virtual miners, validators, are assigned to shards, and only process and validate transactions in that shard (chain).

Ethereum’s shards have a dynamic subset of nodes processing it block-by-block.

64個のShardsが予定される

## Beacon chain
Beacon chainはバリデーターセットとシャードの両方のステートやattestationsを追跡する．attestationは，提案されているブロックへのattester(バリデータ)が行う投票である．
attestationはBeacon Chainに記録される．
## Sharding Consensus
  
> the beacon chain tracks the state of both the set of     
 validators and the shards. In practice this means that if you (periodically) follow what is happening on the beacon chain, you will know enough to verify anything said to be happening within eth2. Trust, but verify.  
  
Beacon chainはバリデーターセットとシャードの両方のステートを追跡する．実際に，Beacon chainに起きていることを定期的に追えば，eth2で起きていると言われていることを検証するのに必要なだけの知識があることを意味する．  
  
PoSシステムが機能するために，誰がバリデーターであるかのコンセンサスと，それぞれのバリデーターの票がどれくらいの重みがあるかを把握したり，行動に対して適切に報酬や罰を与えるためにステイクがどれくらいであるかのコンセンサスが必要である．さらに，Beacon chainはシャードの現在のステートを追跡するだけでなく，バリデーターに仕事を割り当てることで，shardを管理運営する．  
  
他のPoSシステムとeth2の異なる部分はプロトコルに参加できるバリデータの数である．eth2は comimitteeというバリデータのグループによって中間レベルのコンセンサスがなされるためにこれを可能にしている．Beacon chainはランダムにバリデータをcommitteに割り当てる．一つのcommiteeの票は暗号化されて一つのattestationに集約される．つまりこれは，commitee全体の票を検証することは一つの票をチェックすることよりもわずかに労力を伴うに過ぎない．したがって，Beacon chainの正当性をチェックするため，多くのバリデータの票を評価するために検討する必要があるのは，わずかな集約された署名だけである．  

用語  
 - slot    
 新しいブロックがチェーンに追加されることが予想される12秒の時間枠 
 Beacon chainに1ブロックとShradにブロックが追加される．slotは空になりうる

 - epoch     
 32スロット（6.4分）で構成され，その間，ビーコンチェーンはチェーンの維持に関連する計算を実行する．これには新しいブロックの正当化とファイナライズ，バリデーターへの報酬とペナルティの発行が含まれる．  
  
バリデーターは作業を行うためのcommitteeに編成される．常に，各バリデーターは1つのビーコンチェーンと1つのシャードチェーンcommitteeのメンバーであり，エポックごとに1回だけattestationを行うように要求される．  
  
eth2の分割されたコンセンサスのセキュリティモデルは，committeeは多かれ少なかれバリデーターセット全体の正確な統計的表現であるという考えに基づいている．  
そのため，全体のバリデーターの33％が悪意のある状況である場合，これらの悪意のあるバリデーターが同じcommitteeに偏って入る可能性がある．セキュリティモデルにとっては惨事  
  
そこで，1．ランダムにcommitteeに割り当てる．2.committeeの最小人数(少なくとも128バリデータ)を決めることで，この問題を確率的に回避できる．  

[[0]ethos.dev The Beacon Chain Ethereum 2.0 explainer you need to read first](https://ethos.dev/beacon-chain/) //Feb 25 ,2019

[[1]Ethereum Blog Validated: Staking on eth2 #0](https://blog.ethereum.org/2019/11/27/validated-staking-on-eth2-0/) // November 27 ,2019

[[2]Ethereum Blog Validated, staking on eth2: #3 - Sharding Consensus](https://blog.ethereum.org/2020/03/27/sharding-consensus/) // March 27, 2020