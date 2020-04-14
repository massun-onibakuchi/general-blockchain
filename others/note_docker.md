## Note

### Imageからcontenerを作成する

Docker run  "imagename"  コンテナ作成

「-it」…ホストとコンテナの標準入出力を繋げる
「-d」…バックグラウンドで実行
「-p」…「ホストマシンのポート：コンテナのポート」でポートフォワード（転送）
「--name」…コンテナに名前をつける

### Dockerコンテナに接続（ログイン）するには、「docker exec」コマンドか「docker attach」コマンドを実行

「docker exec -it container_name bash」を実行することで、コンテナの中で新しく「/bin/bash」プロセスを作成してログインします。

「docker attach container_name」の場合は、コンテナの標準入出力に接続しているだけであり、新たに接続しているわけではありません。

### コンテナを起動する　docker start 

「docker run」はコンテナの作成と起動を同時におこなっ

### Dockerコンテナを停止するには、「docker stop」コマンドを実行
### Dockerイメージを作成するには、
 1.「docker commit」コマンドを実行
 2. Dockerfile を使う


やること

https://hub.docker.com/r/trufflesuite/ganache-cli/dockerfile

https://qiita.com/kane-hiro/items/81db353b5b1e50b6aae5

http://docs.docker.jp/engine/userguide/dockerimages.html

