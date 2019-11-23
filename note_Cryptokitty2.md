# Cryptokittyコードを読む第２章
ソースコードを読んだメモ

## 参照リンク
 - https://blockgeeks.com/guides/cryptokitties/
 - https://github.com/cryptocopycats/awesome-cryptokitties/blob/master/contracts
 - https://medium.com/loom-network/how-to-code-your-own-cryptokitties-style-game-on-ethereum-7c8ac86a4eb3

# コントラクトの構成
ここで用いているコードは　こちらのGithub https://github.com/cryptocopycats/awesome-cryptokitties/blob/master/contracts 
からの抜粋にコメントをつけたもの

#### 実際にデプロイされているコントラクト
 - KittyCore (Open Source)
 - SaleClockAuction (Open Source)
 - SiringClockAuction (Open Source)

Contractの家系図(継承関係)
```Solidity

contract KittyAccessControl
contract KittyBase is KittyAccessControl
contract KittyOwnership is KittyBase, ERC721
contract KittyBreeding is KittyOwnership
contract KittyAuction is KittyBreeding
contract KittyMinting is KittyAuction
contract KittyCore is KittyMinting
```
## コードを読む2 
コードを読む１の続き

#### 4.KittyBreeding
https://github.com/onibakuchi/awesome-cryptokitties/blob/master/contracts/KittyBreeding.sol

KittyOwnershipを継承
```solidity
    /// @dev Internal check to see if a given sire and matron are a valid mating pair. DOES NOT
    ///  check ownership permissions (that is up to the caller).
    /// @param _matron A reference to the Kitty struct of the potential matron.
    /// @param _matronId The matron's ID.
    /// @param _sire A reference to the Kitty struct of the potential sire.
    /// @param _sireId The sire's ID
    function _isValidMatingPair(
        Kitty storage _matron,
        uint256 _matronId,
        Kitty storage _sire,
        uint256 _sireId
    )
        private
        view
        returns(bool)
    {
        // A Kitty can't breed with itself!
        if (_matronId == _sireId) {
            return false;
        }

        // Kitties can't breed with their parents.
        if (_matron.matronId == _sireId || _matron.sireId == _sireId) {
            return false;
        }
        if (_sire.matronId == _matronId || _sire.sireId == _matronId) {
            return false;
        }

        // We can short circuit the sibling check (below) if either cat is
        // gen zero (has a matron ID of zero).
        if (_sire.matronId == 0 || _matron.matronId == 0) {
            return true;
        }

        // Kitties can't breed with full or half siblings.
        if (_sire.matronId == _matron.matronId || _sire.matronId == _matron.sireId) {
            return false;
        }
        if (_sire.sireId == _matron.matronId || _sire.sireId == _matron.sireId) {
            return false;
        }

        // Everything seems cool! Let's get DTF.
        return true;
    }
```
 - 引数をチェックするだけの関数
 - if と returnで綺麗にかける

#### 5.KittyAuction
2種類の販売がある．
 - `saleAuction` 
refers to the auction for gen0 and p2p sale of kitties.
 - `siringAuction` 
refers to the auction for siring rights of kitties.
猫の販売や猫を雄猫として交配させる権利を販売するオークションを作成するのを扱うコントラクトで，ここでの変数はKitttyBaseで定義されている．
```solidity
/// @dev Sets the reference to the sale auction.
    /// @param _address - Address of sale contract.
    // SaleClockAuction は，KittyCoreや，その親の継承関係に含まれず，独立したコントラクト
    function setSaleAuctionAddress(address _address) external onlyCEO {
        SaleClockAuction candidateContract = SaleClockAuction(_address);

        // NOTE: verify that a contract is what we expect - https://github.com/Lunyr/crowdsale-contracts/blob/cfadd15986c30521d8ba7d5b6f57b4fefcc7ac38/contracts/LunyrToken.sol#L117
        require(candidateContract.isSaleClockAuction());

        // Set the new contract address
        saleAuction = candidateContract;
    }
```

_Approve()によって，コードを読む前章１のところで出てきた，KittyIndexToapprofed(map)にオークションに出された猫が保存される.猫の所有者がコントラクトとなり，エスクローとしての役割を果たす．
```solidity
    /// @dev Put a kitty up for auction.
    ///  Does some ownership trickery to create auctions in one tx.
function createSaleAuction(
        uint256 _kittyId,
        uint256 _startingPrice,
        uint256 _endingPrice,
        uint256 _duration
    )
        external
        whenNotPaused
    {
        // Auction contract checks input sizes
        // If kitty is already on any auction, this will throw
        // because it will be owned by the auction contract.
        require(_owns(msg.sender, _kittyId));
        // Ensure the kitty is not pregnant to prevent the auction
        // contract accidentally receiving ownership of the child.
        // NOTE: the kitty IS allowed to be in a cooldown.
        require(!isPregnant(_kittyId));
        _approve(_kittyId, saleAuction);
        // Sale auction throws if inputs are invalid and clears
        // transfer and sire approval after escrowing the kitty.
        saleAuction.createAuction(
            _kittyId,
            _startingPrice,
            _endingPrice,
            _duration,
            msg.sender
        );
    }
```

雄猫の権利を関数はあるが，ここでは，販売されているな猫を買う関数は，定義されてないようだ．
```solidity
/// @dev Completes a siring auction by bidding.
    ///  Immediately breeds the winning matron with the sire on auction.
    /// @param _sireId - ID of the sire on auction.
    /// @param _matronId - ID of the matron owned by the bidder.
    function bidOnSiringAuction(
        uint256 _sireId,
        uint256 _matronId
    )
        external
        payable
        whenNotPaused
    {
        // Auction contract checks input sizes
        require(_owns(msg.sender, _matronId));
        require(isReadyToBreed(_matronId));
        require(_canBreedWithViaAuction(_matronId, _sireId));

        // Define the current price of the auction.
        uint256 currentPrice = siringAuction.getCurrentPrice(_sireId);
        require(msg.value >= currentPrice + autoBirthFee);

        // Siring auction will throw if the bid fails.
        siringAuction.bid.value(msg.value - autoBirthFee)(_sireId);
        _breedWith(uint32(_matronId), uint32(_sireId));
    }

```

## 疑問点．わからないとこ
 - 販売されているな猫を買う関数は，定義されてないようだが，どこにあるのか
## 理解したところ
親のコントラクトのメソッドには，通常の書き方でアクセスできる．

## コードを読む第３章に続く
