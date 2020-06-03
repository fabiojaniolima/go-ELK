# Estado do cluster

> As palavras-chave "DEVE", "NÃO DEVE", "REQUER", "DEVERIA", "NÃO DEVERIA", "PODERIA", "NÃO PODERIA", "RECOMENDÁVEL", "PODE", e "OPCIONAL" neste documento devem ser interpretadas como descritas no [RFC 2119](http://tools.ietf.org/html/rfc2119). Tradução livre [RFC 2119 pt-br](http://rfc.pt.webiwg.org/rfc2119).

## Status

Todo cluster do Elasticsearch possui os seguintes níveis de integridade:
- **green** (verde)
- **yellow** (amarelo)
- **red** (vermelho)

No nível do fragmento, um status vermelho indica que o fragmento específico não está alocado no cluster, amarelo significa que o fragmento primário está alocado, mas as réplicas não, e verde significa que todos os fragmentos estão alocados. O status do nível do índice é controlado pelo pior status de fragmento. O status do cluster é controlado pelo pior status do índice.

Referência: https://www.elastic.co/guide/en/elasticsearch/reference/6.8/cluster-health.html

Basicamente se subirmos um cluster de nó único, ao indexar algum documento o status do cluster mudará de **green** para **yellow**, isso pode ser visto tanto no terminal quando ao chamar `http://localhost:9200`. Isso ocorre basicamente pelo fato do shard primário não ter para onde ser replicado, como o cluster não está cumprindo com o papel de redundância seu status é alterado.

## API *_cluster*

Essa API pode ser utilizada para verificar o estado do cluster. Para isso realize uma solicitação get ou acesse via navegador a URL:
- http://localhost:9200/_cluster/health
