# Obter e executar o Kibana

> As palavras-chave "DEVE", "NÃO DEVE", "REQUER", "DEVERIA", "NÃO DEVERIA", "PODERIA", "NÃO PODERIA", "RECOMENDÁVEL", "PODE", e "OPCIONAL" neste documento devem ser interpretadas como descritas no [RFC 2119](http://tools.ietf.org/html/rfc2119). Tradução livre [RFC 2119 pt-br](http://rfc.pt.webiwg.org/rfc2119).

## Obter

1 - Baixe o pacote do Kibana:

Para Windows:
- https://artifacts.elastic.co/downloads/kibana/kibana-7.6.2-windows-x86_64.zip

Para Linux ou MacOS:
- https://artifacts.elastic.co/downloads/kibana/kibana-7.6.2-linux-x86_64.tar.gz

2 - Extraia o arquivo `.zip` ou `.tar.gz` baixado

3 - Exclua o arquivo `.zip` ou `.tar.gz` e renomei o diretório extraido para:

```
kibana-7.6.2
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

No Windows:
```
E:\DevOps\kibana-7.6.2\bin\kibana.bat
```

No Linux ou MacOS:
```
\DevOps\kibana-7.6.2\bin\kibana
```

2 - O processo de start pode ser acompanhado pela console

3 - Para conferir se o Kibana subiu acesse:

```
http://localhost:5601
```

Se tudo correu bem você irá cair na página inicial da ferramenta, caso esteja visualizando uma mensagem "Kibana server is not ready yet" pode ser que o start ainda não finalizou ou o Elastisearch não está rodando.
