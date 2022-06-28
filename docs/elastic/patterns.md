# Boas práticas

> As palavras-chave "DEVE", "NÃO DEVE", "REQUER", "DEVERIA", "NÃO DEVERIA", "PODERIA", "NÃO PODERIA", "RECOMENDÁVEL", "PODE", e "OPCIONAL" neste documento devem ser interpretadas como descritas no [RFC 2119](http://tools.ietf.org/html/rfc2119). Tradução livre [RFC 2119 pt-br](http://rfc.pt.webiwg.org/rfc2119).

## Heap

Arquivo de configuração: `./config/jvm.options`

`Xmx` e `Xms`:
- DEVEM possuir o mesmo valor
- DEVERIAM corresponder a 50% da memória RAM total
- O valor máximo ideal considerando as condições anterior DEVERIA ser 26GB
- O valor máximo NÃO DEVE ser superior a 32GB

Referência: https://www.elastic.co/guide/en/elasticsearch/reference/current/heap-size.html

## Shards

Não existe um valor ideal para o tamanho dos shards ou quantidade, mas existem algumas recomendações feitas pela Elastic, sendo elas:

- O número de shards que u nó pode conter é proporcional ao espaço de heap reservado. Como regra geral o número de shards por GB de espaço de head DEVE ser menor que 20;
- Não existe um tamanho máximo ou mínimo exato para um shard, porém, é recomendado que este valor esteja entre 20GB a 40GB. Muitas literaturas recomendam não ultrapassar o valor de 50GB.

**Atenção**: cada shard é naturalmente uma instância do Apache Lucene, logo shards muito pequenos podem gerar muita sobrecarga (overhead), por outro lado shards muito grandes podem dificultar os processos de realocação e balanceamento do cluster.

Referência: https://www.elastic.co/guide/en/elasticsearch/reference/current/scalability.html

## Segmentos e mesclagem

O número de segmentos tem impacto direto na performance de busca e indexação de dados. Quanto maior o número de segmentos mais poder de indexação você terá, porém, mais lenta serão as queries (consultas), logo, quanto menor o número de segmentos mais performance as queries terão.

Resumo:
  - *Mais segmentos*: maior poder de indexão e maior consumo de memória (RAM). Os segmentos possuem estados/metadados que precisam ser mantidos em memória
  - *Menos segmentos*: mais poder de busca e maior consumo de CPU. Menos segmentos pode significar mais operações de mesclagem em segundo plano, isso possui um custo de CPU elevado

> Os segmentos são espaços de armazenamento imutáveis. De tempos em tempos o Elasticsearch faz a mesclagem em segundo plano de segmentos menores em **novos** segmentos maiores.

Podemos configurar o número de segmentos no contexto do índice. Segue algumas das propriedades mais comuns a respeito do assunto:
  - **index.merge.policy.segments_per_tier**: número de segmentos por shard. O processo de mesclagem fará o possível para mesclar os segmentos de modo a manter o total abaixo do valor definido nesta propriedade
  - **index.merge.policy.max_merge_at_once**: limita o número de segmentos que podem ser mesclados por vez. Essa configuração impede que o cluster entre em colapso ao limitar o número de operações de mesclagem em paralelo no contexto do índice. Caso esteja tendo um consumo elevado de disco, pode ser interessante aumentar o valor da própriedade *segments_per_tier* e reduzir o número de mesclagens em paralelo, equalizando desta forma o valor entre as duas propriedades
  - **index.merge.policy.max_merged_segment**: segmentos maiores que este limite não serão mesclado. Podemos diminuir este valor para ter mesclagens mais rápidas, afinal de contas segmentos maiores são mais caros para mesclar

Referência: https://www.elastic.co/pt/blog/how-many-shards-should-i-have-in-my-elasticsearch-cluster

## Mapeamento de dados

Dados submetidos ao Elastic sem mapeamento predefinido serão automaticamente mapeados e indexados com base em um conjunto genérico. Strings normalmente serão indexadas duas vezes, uma para o tipo `text` e outra para `keyword`, já números inteiros receberão o maior tipo inteiro disponível, neste caso `long`.

Realizar o mapeamento dos dados via uso de templates é uma abordagem que orienta o Elastic a como tratar e indexar os dados submetidos, garantindo assim melhor performance e alocação de recursos.

Exemplo: ao submeter quatro campos, sendo eles **nome**, **mensagem**, **email** e **idade** o Elastic fará o seguinte mapeamento:
```
nome: ["text", "keyword"]
mensagem: ["text", "keyword"]
email: ["text", "keyword"]
idade: ["long"]
```

- `text`:  tipo para indexação de texto completo, como o corpo de um email, notícia, memorando e outros;
- `keyword`: tipo para indexação de dados estruturados como email, códigos, status, tags e outros.

Considerando os campos citados anteriormente, em meu contexto (lembre-se, cada caso é um caso) o ideal seria que os dados fossem mapeados da seguinte forma:
```
nome: ["keyword"]
mensagem: ["keyword"]
email: ["keyword"]
idade: ["byte"]
```

> Essa abordagem irá evitar indexações de tipos desnecessários e inferência inadequada de tipo. Agora imagine isso para casos onde temos milhões ou bilhões de registros com dezenas ou centenas de campos.

Consideração final:
- Você DEVERIA definir sempre que possível seu mapeamento de dados via uso de templates

Referência: https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html

## Stop / kill

Considerando que normalmente após start o processo é jogado para background, para realizar o stop é necessário basicamente matar o processo.

No Linux ou MacOS é comum ser utilizada a instrução:

```
kill -9 ELASTIC-PID
```

Mas é muito importante que você dê a chance dos subprocessos serem encerrados ao invés de simplesmente "matá-los". Para isso você DEVERIA considerar utilizar:

```
kill -15 ELASTIC-PID
```

Essa abordagem evita corromper arquivos e até mesmo evita que processos filhos virem órfãos (zumbis).

## refresh_interval

Por padrão, o Elasticsearch atualiza periodicamente os índices a cada segundo, mas apenas nos índices que receberam uma solicitação de pesquisa ou mais nos últimos 30 segundos.

Caso não necessite dos dados em períodos tão curtos após a inserção, você DEVERIA considerar valores adequados a realidade do cenário, ou seja, se estes dados podem ser aguardados por 1 minuto, 1 hora ou se forem necessários para um relatório dia+1, então configure seu valor de atualização baseado neste cenário. Exemplo:

```
PUT /NOME_DO_INDICE/_settings
{
    "index": {
        "refresh_interval": "30s"
    }
}
```

Referência: https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-update-settings.html

## number_of_shards e number_of_replicas

> Por padrão o Elastic considera o valor 1 tanto para o total de shards primários quanto réplicas, essa abordagem passou a ser adotada em algum momento da linha do tempo das release "7.x", uma vez que no passado o padrão era cada novo índice ser criado com 5 shards primários e 1 replica.

Para alterar este valor no escopo global podemos fazer uso da API `_template`. Veja um exemplo:

```
PUT /_template/NOME_DO_TEMPLATE
{
    "index_patterns": ["*"],
    "order": "-1",
    "settings": {
        "index": {
            "number_of_shards": 3,
            "number_of_replicas": 1
        }
    }
}
```

- `index_patterns`: casa com qualquer nome de índice por conta do curinga `*`;
- `order`: define a ordem de mesclagem dos templetes, neste caso valores maiores sobrepõem os menores. Caso um template com o mesmo valor de `index_patterns`, exista e sua ordem seja maior (0,1,2,3...) ele será mesclado sobrepondo os valores do template inferior pelos presentes em sua definição.

> Leia o tópico [Shards](#shards) para saber mais a respeito do assunto.

Referência: https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-templates.html

## Indexing buffer size

Se o seu nó estiver fazendo apenas indexação pesada, verifique se `indices.memory.index_buffer_size` é grande o suficiente para fornecer no máximo 512 MB de buffer de indexação por shard na indexação pesada (qualquer valor acima de 512 MB não resultará em melhor performance). O Elasticsearch utiliza essa configuração, podendo ser ela uma porcentagem da heap ou um tamanho abosoluto em bytes para um buffer compartilhado em todos os shards ativos. Os shards muito ativos naturalmente usarão esse buffer mais do que os shards que estão executando uma indexação leve.

O padrão é 10%, o que geralmente é suficiente: por exemplo, se você fornecer à JVM 10 GB de memória, ela fornecerá 1 GB ao buffer de índice, o que é suficiente para hospedar dois shards com forte indexação.

Referência: https://www.elastic.co/guide/en/elasticsearch/reference/current/tune-for-indexing-speed.html

## Index Sorting

Ao criar um novo índice no Elasticsearch, é possível configurar como os Segmentos dentro de cada Shard serão classificados. Por padrão, o Lucene não aplica nenhum tipo, porém, é recomendado que você defina uma classificação no momento da criação do índice, essa abordagem torna mais eficiente processos de comparação/filtragem de conjuntos.

> Campos aninhados não são compatíveis com a classificação do índice.

Referência: https://www.elastic.co/guide/en/elasticsearch/reference/master/index-modules-index-sorting.html

## Recomendações de leitura

- [Considerações e boas práticas na hora de subir um ambiente ELK em produção](https://medium.com/@fqueirooz80/elasticsearch-tudo-que-você-precisa-saber-sobre-a-ferramenta-de-buscas-da-elastic-parte-4-1-f22b5ca8aa72)
