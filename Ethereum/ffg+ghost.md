# CasperFFG + LMD-GHOST

## LMD-GHOST
PoS仕様の[Greedy Heaviest Observed Subtree (GHOST)](https://eprint.iacr.org/2013/881.pdf)であるLMD-GHOSTをフォークルールとして用いる．

https://blog.ethereum.org/2020/02/12/validated-staking-on-eth2-2-two-ghosts-in-a-trench-coat/
によると，
 > GHOST selects the head of the chain by choosing the fork which has the most votes (it does this by considering all of the votes for each fork block and their respective child blocks).

つまり，フォークがあるたびに，GHOSTはより多くの最新のメッセージが支持するブロックのサブツリー側を選択する．このアルゴリズムは子を持たないブロックに到達するまで，行う

> In particular, eth2 uses a variation of GHOST which has been adapted to a PoS context called Latest Message Driven GHOST (LMD-GHOST). The idea behind LMD-GHOST is that when calculating the head of the chain, one only considers the latest vote made by each validator, and not any of the votes made in the past. This dramatically decreases the computation required when running GHOST, since the number of forks that need to be considered to execute the fork choice cannot be greater than the number of validators ($O(v)$ in Big O notation). 

> Under the rules of GHOST, validators/miners can always try to add a new block to the blockchain (liveness), and they can do this at any point in the chain’s history (asynchronous). Since it is live and fully asynchronous, thanks to our friend FLP, we know it can’t be safe.

### GHOST+The friendly finality gadget
GHOSTでは担保されない安全性をCasperFFGによってファイナライズと安全性をチェーンに与える

[[1]Ethereum CasperFFG + LMD-GHOSTのペーパー](https://github.com/ethereum/research/blob/master/papers/ffg%2Bghost/paper.pdf)

[[2]Ethereum blog Validated, staking on eth2: #2](https://blog.ethereum.org/2020/02/12/validated-staking-on-eth2-2-two-ghosts-in-a-trench-coat/)