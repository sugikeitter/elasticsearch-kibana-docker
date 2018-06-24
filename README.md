# ElasticsearchとKibanaをDockerで動かす

## 参考
こちらを参考にした
- [はじめての Elasticsearch - Qiita](https://qiita.com/nskydiving/items/1c2dc4e0b9c98d164329)
- [Dockerを利用して Elasticsearch + Kibana の環境を作る - Qiita](https://qiita.com/akym03/items/f981a35a95598d7ab97b)
- [Install Elasticsearch with Docker](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)
- [Running Kibana on Docker](https://www.elastic.co/guide/en/kibana/current/docker.html)

## 環境
- Elasticsearch 6.3.0
- Kibana 6.3.0
- Docker Compose 1.21.1

## 必要なファイル
```
.
├── docker-compose.yml
├── es
│   ├── Dockerfile
│   └── config
│       ├── elasticsearch.yml
│       ├── ingest-geoip       # 無いとElasticSearchが起動しなかった
│       ├── jvm.options        # 無いとElasticSearchが起動しなかった
│       └── log4j2.properties
└── kibana
    └── Dockerfile
```

## 起動
- docker-compose.ymlを置いてあるディレクトリで
```
docker-compose build
```
- 成功したら
```
docker-compose up
```

## Kibanaへ接続
ブラウザで`http://localhost:5601/`へアクセス

## TODO
- [Install Elasticsearch with Docker](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)にの例にあるクラスタ構成をやりたい
  - この設定でやろうとすると、KibanaがElasticSearchに接続できなかった
  - `docker-compose.yml`の`networks:`が原因？これがあると、単体構成でもKibanaがElasticSearchに接続できなかった

