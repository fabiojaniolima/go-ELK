# Obter e executar o Logstash

> As palavras-chave "DEVE", "NÃO DEVE", "REQUER", "DEVERIA", "NÃO DEVERIA", "PODERIA", "NÃO PODERIA", "RECOMENDÁVEL", "PODE", e "OPCIONAL" neste documento devem ser interpretadas como descritas no [RFC 2119](http://tools.ietf.org/html/rfc2119). Tradução livre [RFC 2119 pt-br](http://rfc.pt.webiwg.org/rfc2119).

## Obter

1 - Baixe o pacote do Logstash:

Para Windows:
- https://artifacts.elastic.co/downloads/logstash/logstash-7.6.2.zip

Para Linux ou MacOS:
- https://artifacts.elastic.co/downloads/logstash/logstash-7.6.2.tar.gz

2 - Extraia o arquivo `.zip` ou `.tar.gz` baixado

3 - Exclua o arquivo `.zip` ou `.tar.gz` e renomeei o diretório extraído para:

```
logstash-7.6.2
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

1 - Execute:

> "No caso do Windows pode ser necessário ter o perfil de administrador para executar a ferramenta, já no Linux ou MacOS atribua as permissões 755 ao diretório."

**Antes de iniciar o Logstash você DEVE criar uma pipeline, realizar seu registro ou informar como parâmetro na etapa de inicialização, do contrário o processo será encerrado com um sinal de FATAL ERROR ainda no start. Consulte a documentação [Pipelines](./pipelines.md).**

No Windows:
```
E:\DevOps\logstash-7.6.2\bin\logstash.bat
```

No Linux ou MacOS:
```
/DevOps/logstash-7.6.2/bin/logstash
```

A instrução acima irá iniciar o conjunto de pipelines registradas no arquivo `./config/pipelines.yml` conforme descrito na página [Pipelines](./pipelines.md). Para informar um pipeline específico diretamente no comando basta adicionar esse complemento a instrução de chamada do Logstash `-f /path/to/pipeline.conf`, é importante ressaltar que neste caso o conjunto definido no arquivo `pipelines.yml` será totalmente ignorado.

> Pode ser conveniente chamar o Logstash passando como argumento o `-r` para ativar o reloading automático, porém, tenha em mente que essa função pode encerrar o processo ainda na etapa de start quando utilizada em conjunto com alguns outros argumentos, tais como `-f`, `-e` e mais alguns. Esse argumento irá verificar por padrão a cada 3 segundos se houve alguma alteração nos arquivos de configuração (isso inclui pipelines), e fará o carregamento automático das alterações caso elas existam. Para saber mais consulte a documentação oficial [Reloading the Config File](https://www.elastic.co/guide/en/logstash/current/reloading-config.html).

2 - O processo de start pode ser acompanhado pela console

3 - Para conferir se o Logstash subiu acesse:

```
http://localhost:9600
```

Se tudo correu bem você verá um output parecido com:

```
{
  "host": "hostnameXPTO",
  "version": "7.1.1",
  "http_address": "127.0.0.1:9600",
  "id": "8c14c147-902d-45b1-9592-85c0e4cc7759",
  "name": "hostnameXPTO",
  "build_date": "2019-05-23T15:06:30Z",
  "build_sha": "8b4d27f754dd284990f1d9dfd4f4b7e8d99a4506",
  "build_snapshot": false
}
```
