# `Elasticsearch` + `Kibana`を`Docker Compose`で動かす

- `Docker Compose`を利用して、`Elasticsearch`を3ノード1クラスタ構成で起動させた
- `Kibana`も立ち上げて、`Elasticsearch`と連携させた
- `Docker Compose`の設定値について、自分の理解した内容をまとめた

## GitHub
- https://github.com/sugikeitter/elasticsearch-kibana-docker

## 参考
- [Install Elasticsearch with Docker](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)
- [Running Kibana on Docker](https://www.elastic.co/guide/en/kibana/current/docker.html)
- [Compose file version 3 reference](https://docs.docker.com/compose/compose-file/)
- [はじめての Elasticsearch - Qiita](https://qiita.com/nskydiving/items/1c2dc4e0b9c98d164329)
- [Dockerを利用して Elasticsearch + Kibana の環境を作る - Qiita](https://qiita.com/akym03/items/f981a35a95598d7ab97b)

## 環境
- Elasticsearch 6.3.1
- Kibana 6.3.1
- Docker Compose 1.22.0

## 必要なファイル
```
.
├── docker-compose.yml
├── es
│   ├── Dockerfile
│   └── config
│       ├── elasticsearch.yml  # 必要に応じて設定を追記する
│       └── log4j2.properties  # 必要に応じて設定を追記する
└── kibana
    └── Dockerfile
```

## 動作確認

### 起動
- docker-compose.ymlを置いてあるディレクトリで
```
docker-compose build
```
- 成功したら
```
docker-compose up
```

### Kibanaへ接続
ブラウザで`http://localhost:5601/`へアクセス

## 設定値のポイントめも

### docker-compose.yml

#### services
- この下にコンテナを定義していく
- 定義した名前がコンテナ間で通信する時のホスト名のような役割になる

#### build
- 指定したディレクトリのDockerfileを利用してコンテナを起動させることができる

#### container_name
- dockerコンテナの名前

#### environment
- 環境変数を追加することができる
  - 今回は`ES_JAVA_OPTS`という`Elasticsearch`で利用されるJava起動引数を追加で設定した
    - `Elasticsearch`としては`jvm.options`を利用すればJava起動引数を一括でまとめることもできる
    - 今回はデフォルトの`jvm.options`にヒープサイズだけ追加したかった(のと`environment`設定も使って見たかった)ため、これを利用した

#### ulimits
- デフォルトのコンテナのulimitsを上書きする

#### volumes
- ホスト側のファイルをコンテナ側へ渡したりできる
  - `HOST:CONTAINER`
- トップレベルの`volumes`に名前を定義することで以下のことができる
  - 複数サービスをまたがってボリュームを利用
  - ホスト側へデータを永続化

#### port
- 公開するポートの設定
  - `{HOST}:{CONTAINER}`とすると`localhost:{HOST}`へアクセスすると、`container:{CONTAINER}`に繋がる

#### networks
- composeの中でさらにネットワークを分けられる設定
- これを設定すると、設定していないコンテナからは接続できない
  - 最初`Kibana`のコンテナで設定していなかったので`Kibana`->`Elasticsearch`へ接続できなくてハマった
