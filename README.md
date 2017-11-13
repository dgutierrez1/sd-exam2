# Parcial 2 - Daniel Gutierrez A00320176

Este repositorio es para solucionar: 

```
Deberá	realizar	el	aprovisionamiento	de	un	ambiente	compuesto	por	los	siguientes	elementos:
En un servidor ejecutar: Un contenedor encargado de almacenar logs por medio de la aplicación Elasticsearch, un contenedor con la herramienta encargada de visualizar la información de los logs por medio de la aplicación Kibana.
En uno o varios servidores ejecutar: un contenedor web y un contenedor encargado de hacer la conversión de logs por medio de la aplicación Fluentd
```

## Comandos de linux necesarios para el aprovisionamiento de los servicios solicitados

Instalar Java 
```
sudo apt-get update
sudo apt-get install openjdk-7-jre-headless --yes
```

**Instalar Elasticsearch**
```
sudo wget https://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-1.2.2.deb
sudo dpkg -i elasticsearch-1.2.2.deb
```

Iniciar Elasticsearch

```
sudo service elasticsearch start
```


**Instalar Kibana**
```
curl -L https://download.elasticsearch.org/kibana/kibana/kibana-3.1.0.tar.gz | tar xzf -
sudo cp -r kibana-3.1.0 /usr/share/
```

**Instalar Fluentd** usando td-agent package
```
wget http://packages.treasuredata.com/2/ubuntu/trusty/pool/contrib/t/td-agent/td-agent_2.0.4-0_amd64.deb
sudo dpkg -i td-agent_2.0.4-0_amd64.deb
```

Se debe instalar el plugin `out_elasticsearch` para Fluentd pueda enviar informacion a Elasticsearch

```
sudo apt-get install make libcurl4-gnutls-dev --yes
sudo /opt/td-agent/embedded/bin/fluent-gem install fluent-plugin-elasticsearch
```


Se debe configurar Fluentd para reciba los logs y los envie a Elastcsearch. Se debe cambiar el archivo `/etc/td-agent/td-agent.conf` asi:
```
<source>
  @type forward
  port 24224
  bind 0.0.0.0
</source>
<match *.**>
  @type copy
  <store>
    @type elasticsearch
    host elasticsearch
    port 9200
    logstash_format true
    logstash_prefix fluentd
    logstash_dateformat %Y%m%d
    include_tag_key true
    type_name access_log
    tag_key @log_name
    flush_interval 1s
  </store>
  <store>
    @type stdout
  </store>
</match>
```

Iniciar Fluentd

```
sudo service td-agent start
```




