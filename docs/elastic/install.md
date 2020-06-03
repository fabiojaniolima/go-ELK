# Obter e executar o Elastic

> As palavras-chave "DEVE", "NÃO DEVE", "REQUER", "DEVERIA", "NÃO DEVERIA", "PODERIA", "NÃO PODERIA", "RECOMENDÁVEL", "PODE", e "OPCIONAL" neste documento devem ser interpretadas como descritas no [RFC 2119](http://tools.ietf.org/html/rfc2119). Tradução livre [RFC 2119 pt-br](http://rfc.pt.webiwg.org/rfc2119).

## Obter

1 - Baixe o pacote do Elasticsearch:

Para Windows:
- https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.6.2-windows-x86_64.zip

Para Linux ou MacOS:
- https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.6.2-linux-x86_64.tar.gz

2 - Extraia o arquivo `.zip` ou `.tar.gz` baixado

3 - Exclua o arquivo `.zip` ou `.tar.gz` e renomeei o diretório extraído para:

```
elasticsearch-7.6.2
```

4 - Mova o diretório para um local de sua preferência, por exemplo:

No Windows:
```
E:\DevOps
```

No Linux ou MacOS:
```
/DevOps
```

## Executar

> "No caso do Windows pode ser necessário ter o perfil de administrador para executar a ferramenta, já no Linux ou MacOS atribua as permissões 755 ao diretório."

1 - Execute:

No Windows:
```
E:\DevOps\elasticsearch-7.6.2\bin\elasticsearch.bat
```

No Linux ou MacOS:
```
\DevOps\elasticsearch-7.6.2\bin\elasticsearch
```

> Agora você tem um cluster Elasticsearch de nó único em funcionamento. Para incluir novos nós no cluster consulte a documentação: [Incluir novos nós no cluster](./join-cluster.md).

2 - Acompanhe o start pelo prompt de comandos. Exemplo considerando as linhas finais do processo de start:

```
[2020-04-10T20:58:22,195][INFO ][o.e.h.AbstractHttpServerTransport] [HOSTNAMEXPTO] publish_address {127.0.0.1:9201}, bound_addresses {127.0.0.1:9200}, {[::1]:9200}
[2020-04-10T20:58:22,196][INFO ][o.e.n.Node               ] [HOSTNAMEXPTO] started
```

> Na penúltima linha temos o ip e porta de acesso, neste caso `127.0.0.1:9200` e na última linha a indicação de quer o serviço já está online (`started`).

3 - Para conferir se o Elastic está de fato online acesse:
- http://localhost:9200

Se o serviço já estiver online você receberá um output em formato JSON. Exemplo:

```
{
  "name" : "hostnameXPTO",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "X2Dy2TyRRvuZakAwoitQHA",
  "version" : {
    "number" : "7.4.2",
    "build_flavor" : "default",
    "build_type" : "zip",
    "build_hash" : "2f90bbf7b93631e52bafb59b3b049cb44ec25e96",
    "build_date" : "2019-10-28T20:40:44.881551Z",
    "build_snapshot" : false,
    "lucene_version" : "8.2.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

4 - Primeiro boot

Caso seja o primeiro boot do Elasticsearch, é recomendado que após o passo 3 você pare e inicie um novo processo.
