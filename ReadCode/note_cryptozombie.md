## cryptozombie
lesson1

contract ZombieFactory中に
event とzombie構造体を作った
zombies配列を宣言　*zombie構造体はクラスlike、インスタンス作成時引数とる*

mappingでzombieとownerを辞書として格納
owner一人に対してcountとる０、１・・・

function_ceatezombie() でzombies配列に作ったzombieをその場で格納して、**pushメソッドは可変長配列の最後に引数の値を格納して、戻り値に新しい長さを返す** 0,1・・・
```
zombieToOwner[id] = msg.sender; //zombieのownerをmsg.senderで格納
ownerZombieCount[msg.sender]++;　
//デフォルト０＋＋？？　カウントしていく０、１・・・
```
新しいzombie作る関数を実行

requireでownerが初めて作ったかを確認
zombieの名前からランダムなDNAを作成、して次の行で、下２桁が００になるように演算して、代入し直した

contract ZombieFeeding is ZombieFactory {
子クラス的なのを作成

 KittyInterface kittyContract = KittyInterface(ckAddress);
 **謎、contractから何しtれる？**

 feedAndMultplyでrequireでownerが初めて作ったかを確認

 
