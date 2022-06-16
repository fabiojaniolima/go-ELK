# logstash-run

Este projeto tem por objetivo disponibilizar um ambiente (meio) para construção e testes de pipelines do **Logstash** de forma ágil e simples.

> As palavras-chave "DEVE", "NÃO DEVE", "REQUER", "DEVERIA", "NÃO DEVERIA", "PODERIA", "NÃO PODERIA", "RECOMENDÁVEL", "PODE", e "OPCIONAL" neste documento devem ser interpretadas como descritas no [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Como funciona

Em suma, este projeto utiliza o **Docker** para prover uma instância funcional e pré-configurada do **Logstash**, ou seja, abstrai a necessidade de realizar os procedimentos de instalação e configuração necessários para começar a construir pipelines.

Estrutura:
  - ```config```:  contêm os arquivos de configuração do **Logstash**
  - ```pipeline```: todos os arquivos com extensão ```.conf``` presentes neste diretório serão automaticamente carregados
  - ```data```: representa o diretório de dados montado dentro do container Docker no caminho ```/data```, ou seja, você pode jogar os arquivos a serem processados pelo **Logstash** dentro deste diretório

## Como executar

> As instruções abaixo devem ser executadas na raiz deste projeto, ou seja, no mesmo nível deste arquivo que você está lendo.

Existem basicamente duas formas de rodar este projeto, uma é utilizando o docker-compose:

```shell
docker-compose up
```

e a outra é executando a seguinte instrução:

```shell
docker run \
    --rm \
    --name logstash \
    --workdir=/data \
    -v $(pwd)/data/:/data \
    -v $(pwd)/config:/usr/share/logstash/config \
    -v $(pwd)/pipeline:/usr/share/logstash/pipeline \
    -it \
    docker.elastic.co/logstash/logstash:8.2.3
```
