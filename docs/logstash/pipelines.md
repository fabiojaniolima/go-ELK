# Pipelines

> As palavras-chave "DEVE", "NÃO DEVE", "REQUER", "DEVERIA", "NÃO DEVERIA", "PODERIA", "NÃO PODERIA", "RECOMENDÁVEL", "PODE", e "OPCIONAL" neste documento devem ser interpretadas como descritas no [RFC 2119](http://tools.ietf.org/html/rfc2119). Tradução livre [RFC 2119 pt-br](http://rfc.pt.webiwg.org/rfc2119).

As pipelines são esteiras de processamento, filtragem, transformação, normalização e agrupamento de dados. O Logstash possui suporte a uma ampla variedade de inputs, internamente este é o termo utilizado para nos referirmos a fontes de dados, que podem ser arquivos, requisições http, consultas jdbc e outras.

Lista oficial de inputs: https://www.elastic.co/guide/en/logstash/current/input-plugins.html.

## Principais blocos

Uma pipeline é composta basicamente por inputs, filters e outputs:

- **Inputs**: origem de dados. Representa a fonte de dados de onde virão os eventos;
- **Filters**: realiza o processamento dos eventos entregues por um ou mais inputs. Os filtros podem ser aplicados condicionalmente;
- **Outputs**: é para onde o resultado final do fluxo será direcionado.

> Na lista acima poderíamos incluir os **Codecs**, porém, como este raramente é utilizado, recomendo que consulte a referência abaixo para maiores detalhes quanto a todos os blocos de uma pipeline.

Referência: https://www.elastic.co/guide/en/logstash/current/pipeline.html

Os blocos citados acima nada mais são do que estruturas que fazem uso de plugins para adicionar funcionalidades. Você pode consultar a lista de plugins disponíveis executando:

```
/DevOps/logstash-7.6.2/bin/logstash-plugin list
```

Para saber mais a respeito de plugins: https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html

## Exemplos de pipelines

O exemplo abaixo representa uma pipeline que monitora um conjunto de logs do IIS HTTP Server da Microsoft. Veja:

```
input {
    file {
        path => "Z:/path/to/iis*.log"
    }
}

filter {
   if ([message] =~ /^#/) {
        drop{}
    }

    dissect {
        mapping => {
            message => "%{log_timestamp} %{+log_timestamp} %{S-SiteName} %{S-ComputerName} %{S-IP} %{CS-Method} %{CS-URI-Stem} %{CS-URI-Query} %{S-Port} %{CS-Username} %{C-IP} %{CS-Version} %{CS-UserAgent} %{CS-Cookie} %{CS-Referer} %{CS-Host} %{SC-Status} %{SC-SubStatus} %{SC-Win32-Status} %{SC-Bytes} %{CS-Bytes} %{TimeTaken}"
        }
    }

    date {
        match => [ "log_timestamp", "yyyy-MM-dd HH:mm:ss" ]
        target => "@timestamp"
        remove_field => ["log_timestamp"]
    }
}

output{
    elasticsearch{
        hosts => "127.0.0.1:9200"
        manage_template => false
        index => "example-%{+yyyy.MM.dd}"
    }
}
```

> Pessoalmente para o proposito acima recomendaria utilizar o Filebeat, este possui um modulo especialmente projetado para monitorar e atuar sobre os logs do IIS e inúmeros outros serviços. Por fim o Filebeat poderia jogar o output já tratado direto para o Elasticsearch ou direcionar para que o Logstash realize algum tratamento extra ou simplesmente faça a ingestão.

Agora veja um exemplo onde nossa fonte de dados é um dos Beats:

```
input {
    beats {
        port => 7002
    }
}

filter {
   if ("valor-para-comparação" in [tags]) {
        #... código omitido
    } else {
        drop{}
    }

    #... código omitido
```

Desta vez nossa pipeline não escuta um arquivo, mas sim a porta `7002` que será utilizada por um dos componentes da família [Beats](https://www.elastic.co/beats/). Desta vez nossa condicional tem por objetivo proteger o fluxo de ingestão, por exemplo, se outro individuo submeter um conjunto de dados por engano a sua pipeline, o que ocorre? Se você não tiver condicionais adequadas para verificar se o conjunto de dados cabe no escopo de tratamento da pipeline isso irá resultar em uma ingestão de dados errada, sendo assim, confira o conjunto de dados sempre que ele tiver como origem uma fonte externa.

Essa regra se aplica também a casos onde temos múltiplos inputs e lógicas de tratamento dentro da mesma pipeline. Exemplo:

```
input {
    file {
        path => "Z:/path/to/iis*.log"
        tags => ["iis"]
    }

    file {
        path => "D:/path/to/apache*.log"
        tags => ["apache", "default"]
    }
}

filter {
    if ("default" in [tags]) {
        #... um exemplo padrão a varios inputs
    }

    if ("iis" in [tags]) {
       #... lógica para tratar logs do IIS
    } else if ("apache" in [tags]) {
        #... lógica para tratar logs do Apache
    }
}
```

Referência (Glob Pattern Support): https://www.elastic.co/guide/en/logstash/current/glob-support.html

> Condicionais podem ser utilizadas inclusive para definir blocos alternativos de output.

## Sugestão de organização

Os tópicos abaixo são sugestões que visam manter a organização e qualidade operacional do processo.

1 - Crie o diretório `pipeline-files` na raiz do Logstash. Exemplo:

```
/DevOps/logstash-7.6.2/pipeline-files/
```

> Organize as pipelines dentro deste diretório. Se for necessário crie subdiretórios para organizar pipelines para diferentes sistemas.

2 - Considere nomear suas pipelines utilizando a máscara `<sistema>-<acao_da_pipeline>.conf`. Exemplo:

```
st01x-nginx_access_log.conf
```

> Caso esteja utilizando subdiretórios para organizar as pipelines considere somente a máscara `<acao_da_popeline>.conf`

## Registrando múltiplas pipelines

> Segregar lógicas em pipelines diferentes é uma boa maneira de deixar a casa em ordem e evitar pipelines gigantes e dificeis de manter.

1 - Edite o arquivo:

```
/DevOps/logstash-7.6.2/config/pipelines.yml
```

2 - Informe um identificado único para a pipeline e o caminho do arquivo que a define. Exemplo:

```
- pipeline.id: my-pipeline1
  path.config: "/path/to/pipeline.config"
- pipeline.id: my-pipeline2
  path.config: "/path/to/pipeline.config"
```

> ***Obs: você DEVE informar o endereço das pipelines em padrão Unix. Caso esteja em um ambiente Windows ignore a letra que representa a unidade e respeite os padrões de barra para referenciar níveis de diretórios.***
