# `Elasticsearch` + `Kibana`を`Docker Compose`で動かす

## 概要
- `Docker Compose`を利用して、`Elasticsearch`を3ノード1クラスタ構成で起動させた
- `Kibana`も立ち上げて、`Elasticsearch`と連携させた
- `Docker Compose`の設定値について、自分の理解した内容をまとめた

### 更新履歴
- 2022/03/13
  - Elasctic Stackのver.を`7.9.1`=>`8.1.0`へ変更
- 2020/09/17
  - Elasctic Stackのver.を`7.7`=>`7.9.1`へ変更
  - Docker Compose(Mac)のver.を`1.25.5`=>`1.27.2`で動作確認
- 2020/06/07
  - Elasctic Stackのver.を`6.3.1`=>`7.7`へ変更
  - Docker Compose(Mac)のver.を`1.22.0`=>`1.25.5`で動作確認

## GitHub
- https://github.com/sugikeitter/elasticsearch-kibana-docker

## 環境
- Elasticsearch 8.1.0
- Kibana 8.1.0
- Docker version 20.10.7
- Docker Compose 1.29.2

## 必要なファイル
```
.
├── docker-compose.yml
└── .env # PASSWORD の設定が必要
```

## 動作確認

### 起動
以下の手順で`Elasticsearch`と`Kibana`のコンテナを起動させる

```shell
## リポジトリをcloneして、docker-compose.ymlが配置されているディレクトリに移動
$ git clone https://github.com/sugikeitter/elasticsearch-kibana-docker.git
$ cd elasticsearch-kibana-docker

## docker-compose.ymlからDockerイメージをビルドして起動
$ docker-compose up -d

## docker-compose.ymlからDockerイメージをビルドして起動（起動時のログが見たい場合）
$ docker-compose up

## クラスターを停止
$ docker-compose down

## クラスターを停止して起動したリソースを削除
$ docker-compose down
```

### 起動時のよくあるエラー
- `docker-compose up` して `max virtual memory areas vm.max_map_count [XXXXX] is too low, increase to at least [262144]` が出た場合、以下の設定でカーネル設定を変更する
```shell
$ sysctl -w vm.max_map_count=262144
```

### Kibanaへ接続
ブラウザで`http://localhost:5601/`へアクセス

## docker-compose.ymlの設定について
[今回作成したdocker-compose.ymlはこちら](https://github.com/sugikeitter/elasticsearch-kibana-docker/blob/master/docker-compose.yml)

### services
- この下にコンテナを定義していく
- **ここで定義したservice名はコンテナ間で通信する時のホスト名のような役割になる**
  - 下で出てくる`container_name`と違い、`Docker Compose`で利用される名称
  - ElasticSearchの設定である`node.name`や`discovery.seed_hosts`の値をコンテナ間の通信する時の名前として利用するようなので、**serviceの名前とこの設定値は合わせておいた方が良い**
- 今回の場合だと`es01`, `es02`, `es03`, `kibana01`という4つ定義している

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

## 参考
- [Install Elasticsearch with Docker](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)
- [Running Kibana on Docker](https://www.elastic.co/guide/en/kibana/current/docker.html)
- [Compose file version 3 reference](https://docs.docker.com/compose/compose-file/)
- [はじめての Elasticsearch - Qiita](https://qiita.com/nskydiving/items/1c2dc4e0b9c98d164329)
- [Dockerを利用して Elasticsearch + Kibana の環境を作る - Qiita](https://qiita.com/akym03/items/f981a35a95598d7ab97b)
