# `Elasticsearch` + `Kibana`を`Docker Compose`で動かす

## 概要
- `Docker Compose`を利用して、`Elasticsearch`を3ノード1クラスタ構成で起動させた
- `Kibana`も立ち上げて、`Elasticsearch`と連携させた
- `Docker Compose`の設定値について、自分の理解した内容をまとめた

## GitHub
- https://github.com/sugikeitter/elasticsearch-kibana-docker

## 環境
- Elasticsearch 6.3.1
- Kibana 6.3.1
- Docker Compose 1.22.0

## 必要なファイル
```
.
├── docker-compose.yml
├── es
│   ├── Dockerfile
│   └── config
│       ├── elasticsearch.yml  # 必要に応じて設定を追記する
│       └── log4j2.properties  # 必要に応じて設定を追記する
└── kibana
    └── Dockerfile
```

## 動作確認

### 起動
以下の手順で`Elasticsearch`と`Kibana`のコンテナを起動させる

```shell
## リポジトリをcloneして、docker-compose.ymlが配置されているディレクトリに移動
$ git clone git@github.com:sugikeitter/elasticsearch-kibana-docker.git
$ cd elasticsearch-kibana-docker

## docker-compose.ymlからDockerイメージをビルド
$ docker-compose build

## ビルドが成功したらコンテナ起動
$ docker-compose up
```

### Kibanaへ接続
ブラウザで`http://localhost:5601/`へアクセス

## docker-compose.ymlの設定について
[今回作成したdocker-compose.ymlはこちら](https://github.com/sugikeitter/elasticsearch-kibana-docker/blob/master/docker-compose.yml)

### services
- この下にコンテナを定義していく
- **ここで定義したservice名はコンテナ間で通信する時のホスト名のような役割になる**
  - 下で出てくる`container_name`と違い、`Docker Compose`で利用される名称
- 今回の場合だと`es01`, `es02`, `es03`, `kibana01`という4つ定義している

### build
- 指定したディレクトリのDockerfileを利用してコンテナを起動させることができる
- `Elasticsearch`プロセス用のコンテナは3つあるが、今回は3つとも利用するDockerイメージは同じにしているため、全て`./es`を指定する
  - つまり`./es/Dockerfile`からコンテナを起動する
- `Kibana`プロセス用のコンテナは`./kibana`を指定し、`./kibana/Dockerfile`からコンテナを起動する

### container_name
- `docker ps`した時に表示されるdockerコンテナの名前
- `Docker Compose`ではなく`Docker`側の機能で、**同一ホストマシン上では同じ名前のコンテナは複数起動できないので注意**

### environment
- 環境変数を追加することができる
  - 今回は`ES_JAVA_OPTS`という`Elasticsearch`で利用されるJava起動引数を追加で設定した
    - `Elasticsearch`としては`jvm.options`を利用すればJava起動引数を一括でまとめることもできる
    - 今回はデフォルトの`jvm.options`にヒープサイズだけ追加したかった(のと`environment`設定も使って見たかった)ため、これを利用した

### ulimits
- コンテナの`ulimit`コマンドで設定できる値をデフォルト値から上書きする

### volumes
- ホスト側のファイルをコンテナ側へ渡したりできる
- `HOST:CONTAINER`の順で設定
- トップレベルの`volumes`に名前を定義することで以下のことができる
  - 複数サービスをまたがってボリュームを利用
  - ホスト側へデータを永続化

### port
- 公開するポートの設定
- `HOST:CONTAINER`の順で設定
- 今回は`es0*`はそれぞれポート`9200`を利用してプロセスを立ち上げているが、ホストマシン経由でコンテナへアクセスする場合に`localhost:920[1,2,3]`でそれぞれのコンテナの`Elasticsearch`へアクセスできるように設定している
  - `localhost:9201`->`es01:9200`
  - `localhost:9202`->`es02:9200`
  - `localhost:9203`->`es03:9200`

### networks
- `Docker Compose`の中でさらにネットワークを分けられる設定
- トップレベルの`networks`にキーを設定しておき、serviceごとの`networks`でそれを利用することができる
- これをserviceに設定すると、同じキーを設定していないserviceからは接続できない
  - 最初`kibana01`で`networks`を設定していなかったので`Kibana`->`Elasticsearch`へ接続できなくてハマった

## 参考
- [Install Elasticsearch with Docker](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)
- [Running Kibana on Docker](https://www.elastic.co/guide/en/kibana/current/docker.html)
- [Compose file version 3 reference](https://docs.docker.com/compose/compose-file/)
- [はじめての Elasticsearch - Qiita](https://qiita.com/nskydiving/items/1c2dc4e0b9c98d164329)
- [Dockerを利用して Elasticsearch + Kibana の環境を作る - Qiita](https://qiita.com/akym03/items/f981a35a95598d7ab97b)
