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
- ElasticSearch起動時に出る以下のエラーについて確認
```
elasticsearch    | 2018-06-24 16:19:14,400 main ERROR Could not register mbeans java.security.AccessControlException: access denied ("javax.management.MBeanTrustPermission" "register")
elasticsearch    | 	at java.base/java.security.AccessControlContext.checkPermission(AccessControlContext.java:472)
elasticsearch    | 	at java.base/java.lang.SecurityManager.checkPermission(SecurityManager.java:371)
elasticsearch    | 	at java.management/com.sun.jmx.interceptor.DefaultMBeanServerInterceptor.checkMBeanTrustPermission(DefaultMBeanServerInterceptor.java:1805)
elasticsearch    | 	at java.management/com.sun.jmx.interceptor.DefaultMBeanServerInterceptor.registerMBean(DefaultMBeanServerInterceptor.java:318)
elasticsearch    | 	at java.management/com.sun.jmx.mbeanserver.JmxMBeanServer.registerMBean(JmxMBeanServer.java:522)
elasticsearch    | 	at org.apache.logging.log4j.core.jmx.Server.register(Server.java:389)
elasticsearch    | 	at org.apache.logging.log4j.core.jmx.Server.reregisterMBeansAfterReconfigure(Server.java:167)
elasticsearch    | 	at org.apache.logging.log4j.core.jmx.Server.reregisterMBeansAfterReconfigure(Server.java:140)
elasticsearch    | 	at org.apache.logging.log4j.core.LoggerContext.setConfiguration(LoggerContext.java:556)
elasticsearch    | 	at org.apache.logging.log4j.core.LoggerContext.start(LoggerContext.java:261)
elasticsearch    | 	at org.apache.logging.log4j.core.impl.Log4jContextFactory.getContext(Log4jContextFactory.java:206)
elasticsearch    | 	at org.apache.logging.log4j.core.config.Configurator.initialize(Configurator.java:220)
elasticsearch    | 	at org.apache.logging.log4j.core.config.Configurator.initialize(Configurator.java:197)
elasticsearch    | 	at org.elasticsearch.common.logging.LogConfigurator.configureStatusLogger(LogConfigurator.java:171)
elasticsearch    | 	at org.elasticsearch.common.logging.LogConfigurator.configure(LogConfigurator.java:140)
elasticsearch    | 	at org.elasticsearch.common.logging.LogConfigurator.configure(LogConfigurator.java:119)
elasticsearch    | 	at org.elasticsearch.bootstrap.Bootstrap.init(Bootstrap.java:294)
elasticsearch    | 	at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:136)
elasticsearch    | 	at org.elasticsearch.bootstrap.Elasticsearch.execute(Elasticsearch.java:127)
elasticsearch    | 	at org.elasticsearch.cli.EnvironmentAwareCommand.execute(EnvironmentAwareCommand.java:86)
elasticsearch    | 	at org.elasticsearch.cli.Command.mainWithoutErrorHandling(Command.java:124)
elasticsearch    | 	at org.elasticsearch.cli.Command.main(Command.java:90)
elasticsearch    | 	at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:93)
elasticsearch    | 	at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:86)
elasticsearch    |
elasticsearch    | 2018-06-24 16:19:14,580 main ERROR Could not register mbeans java.security.AccessControlException: access denied ("javax.management.MBeanTrustPermission" "register")
elasticsearch    | 	at java.base/java.security.AccessControlContext.checkPermission(AccessControlContext.java:472)
elasticsearch    | 	at java.base/java.lang.SecurityManager.checkPermission(SecurityManager.java:371)
elasticsearch    | 	at java.management/com.sun.jmx.interceptor.DefaultMBeanServerInterceptor.checkMBeanTrustPermission(DefaultMBeanServerInterceptor.java:1805)
elasticsearch    | 	at java.management/com.sun.jmx.interceptor.DefaultMBeanServerInterceptor.registerMBean(DefaultMBeanServerInterceptor.java:318)
elasticsearch    | 	at java.management/com.sun.jmx.mbeanserver.JmxMBeanServer.registerMBean(JmxMBeanServer.java:522)
elasticsearch    | 	at org.apache.logging.log4j.core.jmx.Server.register(Server.java:389)
elasticsearch    | 	at org.apache.logging.log4j.core.jmx.Server.reregisterMBeansAfterReconfigure(Server.java:167)
elasticsearch    | 	at org.apache.logging.log4j.core.jmx.Server.reregisterMBeansAfterReconfigure(Server.java:140)
elasticsearch    | 	at org.apache.logging.log4j.core.LoggerContext.setConfiguration(LoggerContext.java:556)
elasticsearch    | 	at org.apache.logging.log4j.core.LoggerContext.start(LoggerContext.java:261)
elasticsearch    | 	at org.elasticsearch.common.logging.LogConfigurator.configure(LogConfigurator.java:163)
elasticsearch    | 	at org.elasticsearch.common.logging.LogConfigurator.configure(LogConfigurator.java:119)
elasticsearch    | 	at org.elasticsearch.bootstrap.Bootstrap.init(Bootstrap.java:294)
elasticsearch    | 	at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:136)
elasticsearch    | 	at org.elasticsearch.bootstrap.Elasticsearch.execute(Elasticsearch.java:127)
elasticsearch    | 	at org.elasticsearch.cli.EnvironmentAwareCommand.execute(EnvironmentAwareCommand.java:86)
elasticsearch    | 	at org.elasticsearch.cli.Command.mainWithoutErrorHandling(Command.java:124)
elasticsearch    | 	at org.elasticsearch.cli.Command.main(Command.java:90)
elasticsearch    | 	at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:93)
elasticsearch    | 	at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:86)
```
