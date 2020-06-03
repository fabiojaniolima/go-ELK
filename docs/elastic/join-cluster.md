# Incluir novos nós no cluster

> As palavras-chave "DEVE", "NÃO DEVE", "REQUER", "DEVERIA", "NÃO DEVERIA", "PODERIA", "NÃO PODERIA", "RECOMENDÁVEL", "PODE", e "OPCIONAL" neste documento devem ser interpretadas como descritas no [RFC 2119](http://tools.ietf.org/html/rfc2119). Tradução livre [RFC 2119 pt-br](http://rfc.pt.webiwg.org/rfc2119).

## Nó local

Para incluir um nó local no cluster você DEVE chamar o arquivo de inicialização do Elasticsearch e passar como parâmetro um diretório exclusivo para *dados* e outro para *logs*:

**No Windows:**
```
E:\DevOps\elasticsearch-7.6.2\bin\elasticsearch.bat -E path.data=data2 -E path.logs=log2
E:\DevOps\elasticsearch-7.6.2\bin\elasticsearch.bat -E path.data=data3 -E path.logs=log3
```

**No Linux ou MacOS:**
```
/DevOps/elasticsearch-7.6.2/bin/elasticsearch -Epath.data=data2 -Epath.logs=log2
/DevOps/elasticsearch-7.6.2/bin/elasticsearch -Epath.data=data3 -Epath.logs=log3
```

> Cada novo nó receberá um ID exclusivo é automaticamente inserido no cluster uma vez que esteja sendo executado localmente.

**Nós locais são uma boa alternativa para simular ambientes produtivos em máquinas desktop, ou seja, essa abordagem NÃO DEVE ser utilizada em produção.**

## Nó externo

As configurações `discovery.seed_hosts` e `cluster.initial_master_nodes` podem ser tidas como as mais essenciais e importantes no processo inicial de descoberta e formação do cluster. Você NÃO DEVE subir um cluster Elasticsearch em produção sem antes definir essas duas configurações.

> As configurações abaixo DEVEM ser feitas no arquivo `E:\DevOps\elasticsearch-7.6.2\config\elasticsearch.yml` ou `/DevOps/elasticsearch-7.6.2/config/elasticsearch.yml` dependendo do seu sistema operacional.

1 - Descomente a variável `cluster.name` e atribua um nome ao cluster:

```
cluster.name: local-dev
```

2 - Descomente a variável de descoberta e informe os hostname ou IP de todos os nós que devem fazer parte do cluster:

```
discovery.seed_hosts: ["host1", "host2"]
```

3 - Defina a lista de nós elegíveis para master na primeira inicialização do cluster ou após uma reinicialização completa:

```
cluster.initial_master_nodes: ["node-1", "node-2", "node-3"]
```

> Ao iniciar um novo cluster Elasticsearch pela primeira vez, há uma etapa de inicialização do cluster, que determina o conjunto de nós elegíveis para master cujos os votos são contados na primeira eleição. No modo de desenvolvimento, sem configurações de descoberta definidas, essa etapa é executada automaticamente pelos próprios nós. Como essa inicialização automática é possivelmente insegura, ao iniciar um novo cluster no modo de produção, você DEVE listar explicitamente os nós elegíveis para master cujos votos DEVEM ser contados na primeira eleição. Você NÃO DEVE usar essa configuração ao reiniciar um cluster ou adicionar um novo nó a um cluster existente.

4 - Ainda que não seja obrigatório, uma vez que na sua ausência o Elasticsearch considere o hostname da máquina, é recomendado que você preencha a variável abaixo atribuindo desta forma um nome ao nó:

```
node.name: node-1
```

5 - Todo nó do cluster acumula funções, podendo elas ser do tipo **master**, **data** e **ingest**. Em um ambiente crítico de produção pode ser necessário criar masters dedicados. Por padrão o Elastic considera implicitamente um nó como sendo dos três tipos:

```
node.master: true
node.data: true
node.ingest: true
```

Para criar um nó dedicado basta alterar os valores conforme abaixo, caso a configuração não exista no arquivo basta cria-la:

```
node.master: true
node.data: false
node.ingest: false
```

> **Quando finalizar as configurações você DEVE reiniciar o nó para garantir que o mesmo considere os novos valores.**

Referência:
- https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-bootstrap-cluster.html
- https://www.elastic.co/guide/en/elasticsearch/reference/current/discovery-settings.html
- https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-node.html

## Verificando a saúde do cluster

1 - No navegador acesse:
- http://localhost:9200/_cat/health?v

> Como output teremos uma visão resumida do estado atual do cluster, tais como nome, status, total de nós, total de shards e outras.
