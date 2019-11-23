# Cryptokittycオードを読む第１章

## ソースコードを読んで理解しことのメモ
 [blockgeeks.com](https://blockgeeks.com/guides/cryptokitties/)

 [Githubに公開されているソースコード](https://github.com/cryptocopycats/awesome-cryptokitties/blob/master/contracts/KittyBase.sol)

[ソースコードの解説(英語）](https://medium.com/loom-network/how-to-code-your-own-cryptokitties-style-game-on-ethereum-7c8ac86a4eb3)

### CryptokittyBase contract
キティをどう保存するか
=>structとして扱う(sire = father , matron = mother の意味)
ERC721に従う

### 疑問点．わからないとこ
 - kittybaseの_createKitty() function のuint32をuint256で変換してrequireするところ
=> 同じ型でないと，== で比較できないからだろうか？

 - 同上のコントラクトの７８行目，
 - たびたび現れる決まった表現．アドレスを引数として，どうなっている？
``` solidity
// The contract that will return kitty metadata
    ERC721Metadata public erc721Metadata;
/// @dev Set the address of the sibling contract that tracks metadata.
    ///  CEO only.
    function setMetadataAddress(address _contractAddress) public onlyCEO {
        erc721Metadata = ERC721Metadata(_contractAddress);
    }
```

## 基本に戻る必要があるところ，理解の弱い用語・事柄
 - event-log ? event って結局？
 - Etherの支払いの仕方

## 個人的ポイント
 -  ERC721の扱い方
 - 猫の所有権の写し方/transferの仕方
 - blockchainを用いる線引き・分散化の応用箇所の理解
 - auctionの仕方

### 注目すべきとこ，素晴らしい点，理解したところ
 - 全てがブロックチェーンに格納されてない，必要なところにのみ用いている．
　ネコの画像はサーバに保存されていて，ネコのデータとして画像はブロックチェーンに格納されていない，ethの支払いを少なくするためでもある

 - KittyCore, SaleClockAuction, AiringClockAuctionに分けられている
こうすることで，キティの保管をするKittyCore と　複雑な仕組みをいくらかもち，バグを含むかもしれなくても，分けておくことで，アップグレードする際にキティの保管をするkittyCoreに影響は与えないようにしている

 - pausableにしてる．非常時に別のコントラクトを使えるようにしてある


# コントラクトの構成詳解
#### 実際にデプロイされているコントラクト
 - KittyCore (Open Source)
 - SaleClockAuction (Open Source)
 - SiringClockAuction (Open Source)

KittyCoreの構成
KittyCoreはクリプトキティのメインコントラクトである．論理的な区分にコードを分けるために，２つのやり方で，分けてある．
```solidity
// This is the main CryptoKitties contract. In order to keep our code seperated into logical sections,
    // we've broken it up in two ways. First, we have several seperately-instantiated sibling contracts
    // that handle auctions and our super-top-secret genetic combination algorithm. The auctions are
    // seperate since their logic is somewhat complex and there's always a risk of subtle bugs. By keeping
    // them in their own contracts, we can upgrade them without disrupting the main contract that tracks
    // kitty ownership. The genetic combination algorithm is kept seperate so we can open-source all of
    // the rest of our code without making it _too_ easy for folks to figure out how the genetics work.
    // Don't worry, I'm sure someone will reverse engineer it soon enough!
    //
    // Secondly, we break the core contract into multiple files using inheritence, one for each major
    // facet of functionality of CK. This allows us to keep related code bundled together while still
    // avoiding a single giant file with everything in it. The breakdown is as follows: 
 - KittyBase 
 - KittyAccessControl
 - KittyOwnership 
 - KittyBreeding 
 - KittyAuction 
 - KittyMinting 
```
継承関係
```Solidity
contract KittyAccessControl
contract KittyBase is KittyAccessControl
contract KittyOwnership is KittyBase, ERC721
contract KittyBreeding is KittyOwnership
contract KittyAuction is KittyBreeding
contract KittyMinting is KittyAuction
contract KittyCore is KittyMinting
```
## 詳解
#### 1.KittyAccessControl
運営だけが実行できるように一部の関数を制限するためのmodifierが定義されている． CEO CFO CTO ?の三段階の権力レベルがある．最高レベルのCEOは，CFO，CTOの権力を無効化でき，CFO，CTOのアカウントが乗っ取られたなどの非常時以外は通常使われないようだ．
#### 2.KittyBase
Kittyの構造体が定義，uint256,uin64,uin64,uint32,uint32,uint32が含まれる．Ethereumのbyte-packing rulesによりこの順序が大事．
 Ref: http://solidity.readthedocs.io/en/develop/miscellaneous.html
```solidity
Kitty[] kitties;
```
全ての猫をこのKitty[]配列に格納．indexがIdとして猫に固有の値として女媧のところで用いられる．

```solidity
    /// @dev A mapping from cat IDs to the address that owns them. All cats have
    ///  some valid owner address, even gen0 cats are created with a non-zero owner.
    mapping (uint256 => address) public kittyIndexToOwner;

    // @dev A mapping from owner address to count of tokens that address owns.
    //  Used internally inside balanceOf() to resolve ownership count.
    //    所有者の持っている猫の数を格納．
    mapping (address => uint256) ownershipTokenCount;

    /// @dev A mapping from KittyIDs to an address that has been approved to call
    ///  transferFrom(). Each Kitty can only have one approved address for transfer
    ///  at any time. A zero value means no approval is outstanding.
    //譲渡される猫のidと前の所有者によって許可された新しい所有者の対応を格納.
    mapping (uint256 => address) public kittyIndexToApproved;

    /// @dev A mapping from KittyIDs to an address that has been approved to use
    ///  this Kitty for siring via breedWith(). Each Kitty can only have one approved
    ///  address for siring at any time. A zero value means no approval is outstanding.
    //最後のmappingは所有者以外の他のユーザが持つ猫と交配が許可された猫のidと許可されたユーザの対応を格納．

    mapping (uint256 => address) public sireAllowedToAddress;
```

下のコードは,コードを読む第二章で扱う販売を行うためのコード
```solidity
/// @dev The address of the ClockAuction contract that handles sales of Kitties. This
    ///  same contract handles both peer-to-peer sales as well as the gen0 sales which are
    ///  initiated every 15 minutes.
    SaleClockAuction public saleAuction;

    /// @dev The address of a custom ClockAuction subclassed contract that handles siring
    ///  auctions. Needs to be separate from saleAuction because the actions taken on success
    ///  after a sales and siring auction are quite different.
    SiringClockAuction public siringAuction
```
**このコントラクトや親のコントラクトに，SaleClockAuctionコントラクトがなくても，定義していいらしい．**  
のちに，functionでSaliClockAuctionのアドレスが与えられる．

あとは，_transfer,_createKitty という関数が定義されている．
ここは，略．  
参照すべきサイト
https://medium.com/loom-network/how-to-code-your-own-cryptokitties-style-game-on-ethereum-7c8ac86a4eb3  
https://blockgeeks.com/guides/cryptokitties/

#### 3.KittyOwnership
KittyBaseとERC721を継承している．

``` solidity
// The contract that will return kitty metadata
    ERC721Metadata public erc721Metadata;
/// @dev Set the address of the sibling contract that tracks metadata.
    ///  CEO only.
    function setMetadataAddress(address _contractAddress) public onlyCEO {
        erc721Metadata = ERC721Metadata(_contractAddress);
　　　//ERC721に従うメタデータのアドレスをセットする．?
    }
```


KittyBaseで定義したmappingに手放す猫と新しい所有者のアドレスを格納する関数_approve．
[Ref:](https://github.com/cryptocopycats/awesome-cryptokitties/blob/master/contracts/KittyAuction.sol) のcreateSaleAuctionという関数に登場
```solidity
    /// @dev Marks an address as being approved for transferFrom(), overwriting any previous
    ///  approval. Setting _approved to address(0) clears all transfer approval.
    ///  NOTE: _approve() does NOT send the Approval event. This is intentional because
    ///  _approve() and transferFrom() are used together for putting Kitties on auction, and
    ///  there is no value in spamming the log with Approval events in that case.
    // 
    function _approve(uint256 _tokenId, address _approved) internal {
        kittyIndexToApproved[_tokenId] = _approved;
    }
```


以下のtransferは猫を購入者に所有権を移すメソッド．
transferを実行する前は，オークションにかけられた猫の保有者は，エスクローとしてコントラクトであるが，実行後は，購入者に移される

```solidity
/// @notice Transfers a Kitty to another address. If transferring to a smart
    ///  contract be VERY CAREFUL to ensure that it is aware of ERC-721 (or
    ///  CryptoKitties specifically) or your Kitty may be lost forever. Seriously.
    /// @param _to The address of the recipient, can be a user or contract.
    /// @param _tokenId The ID of the Kitty to transfer.
    /// @dev Required for ERC-721 compliance.
    function transfer(
        address _to,
        uint256 _tokenId
    )
        external
        whenNotPaused
    {
        // Safety check to prevent against an unexpected 0x0 default.
        require(_to != address(0));
        // Disallow transfers to this contract to prevent accidental misuse.
        // The contract should never own any kitties (except very briefly
        // after a gen0 cat is created and before it goes on auction).
        require(_to != address(this));
	//新しい飼い主のアドレスがこのコントラクトthisでないかチェック
        // Disallow transfers to the auction contracts to prevent accidental
        // misuse. Auction contracts should only take ownership of kitties
        // through the allow + transferFrom flow.
        require(_to != address(saleAuction));
        require(_to != address(siringAuction));
	//新しい飼い主のアドレスがsaleAuction,siringAuctionコントラクトでないかチェック

        // You can only send your own cat.
        require(_owns(msg.sender, _tokenId));

        // Reassign ownership, clear pending approvals, emit Transfer event.
	//ここまでのチェックを合格して，ようやく KittyBaseで定義した_transfer関数で猫を譲渡
	//すなわち，kittyindexToOwner(maping)のOwnerを書き変える．
        _transfer(msg.sender, _to, _tokenId);
    }
```
　
ユーザの保有している全ての猫のidを返す
```solidity
/// @notice Returns a list of all Kitty IDs assigned to an address.
    /// @param _owner The owner whose Kitties we are interested in.
    /// @dev This method MUST NEVER be called by smart contract code. First, it's fairly
    ///  expensive (it walks the entire Kitty array looking for cats belonging to owner),
    ///  but it also returns a dynamic array, which is only supported for web3 calls, and
    ///  not contract-to-contract calls.
    //全てのKittyが格納される配列をループするから，めっちゃコスト高いでけでなく，コントラクトからは動的配列の返り値は返せないので，web3からのみでサポートされる．このメソッドはスマコンによって呼び出されてはいけない．
    function tokensOfOwner(address _owner) external view returns(uint256[] ownerTokens) {
        uint256 tokenCount = balanceOf(_owner);

        if (tokenCount == 0) {
            // Return an empty array
            return new uint256[](0);
        } else {
            uint256[] memory result = new uint256[](tokenCount);
            uint256 totalCats = totalSupply();
            uint256 resultIndex = 0;

            // We count on the fact that all cats have IDs starting at 1 and increasing
            // sequentially up to the totalCat count.
            uint256 catId;

            for (catId = 1; catId <= totalCats; catId++) {
                if (kittyIndexToOwner[catId] == _owner) {
                    result[resultIndex] = catId;
                    resultIndex++;
                }
            }

            return result;
        }
    }
```

### コードを読む第２章に続く

