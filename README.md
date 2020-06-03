# go-ELK

Este repositório tem como premissa ser um guia de instalação, manutenção e aplicação de boas práticas de uso da Stack **ELK** em diferentes contextos. Artefatos como templates, scripts e outros serão igualmente contemplados por este repositório.

> As palavras-chave "DEVE", "NÃO DEVE", "REQUER", "DEVERIA", "NÃO DEVERIA", "PODERIA", "NÃO PODERIA", "RECOMENDÁVEL", "PODE", e "OPCIONAL" neste documento devem ser interpretadas como descritas no [RFC 2119](http://tools.ietf.org/html/rfc2119). Tradução livre [RFC 2119 pt-br](http://rfc.pt.webiwg.org/rfc2119).

## Importante

Você DEVE ter em mente que para os propósitos gerais deste documento o diretório *root de trabalho* será:

- **Linux ou MacOS**: `/DevOps`
- **Windows**: `E:\DevOps`

> Em alguns momentos, exceto em arquivos de configuração poderei considerar citar somente a referência `/DevOps` com o intuito de tornar o texto mais compacto, deste modo caberá a você converter este caminho para o padrão Windows caso está seja sua plataforma.

## Docs

- [Elasticsearch](./docs/elastic/README.md)
- [Logstash](./docs/logstash/README.md)
- [Kibana](./docs/kibana/README.md)

Documentação oficial da Stack: https://www.elastic.co/guide/index.html

## Artifacts

Este diretório tem por objetivo concentrar artefatos como scripts, templates, modelos de configuração e demais itens que venham a ser úteis na entrega de tarefas diárias.

> Os arquivos insomnia_workspace-main_apis.json podem ser abertos no software Insomnia Core. Estes arquivos possuem uma lista pré-fabricadas de requests. Bom proveito :)
