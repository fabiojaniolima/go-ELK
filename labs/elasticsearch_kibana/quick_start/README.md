# Elasticsearch QuickStart

Este é um laboratório desenhado no contexto de estudo e experimentação, ou seja, NÃO DEVE ser considerado como produto final para um ambiente produtivo.

> As palavras-chave "DEVE", "NÃO DEVE", "REQUER", "DEVERIA", "NÃO DEVERIA", "PODERIA", "NÃO PODERIA", "RECOMENDÁVEL", "PODE", e "OPCIONAL" neste documento devem ser interpretadas como descritas no [RFC 2119](http://tools.ietf.org/html/rfc2119). Tradução livre [RFC 2119 pt-br](http://rfc.pt.webiwg.org/rfc2119).

## O que é

Este laboratório é composto de 3 instâncias do Elasticsearch e 1 instância do Kibana, obviamente você tem total liberdade para alterar estes números conforme necessidade.

## Como executar

Crie a network elk:

```shell
docker network create -d bridge elk
```

Para subir os containres:

```shell
docker-compose up -d
```
