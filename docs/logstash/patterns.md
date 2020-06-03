# Boas práticas

> As palavras-chave "DEVE", "NÃO DEVE", "REQUER", "DEVERIA", "NÃO DEVERIA", "PODERIA", "NÃO PODERIA", "RECOMENDÁVEL", "PODE", e "OPCIONAL" neste documento devem ser interpretadas como descritas no [RFC 2119](http://tools.ietf.org/html/rfc2119). Tradução livre [RFC 2119 pt-br](http://rfc.pt.webiwg.org/rfc2119).

## Não faça no Logstash o que pode ser feito nos Beats

É muito comum pegarmos os eventos brutos capturados pelos Beats, como por exemplo pelo Filebeat e direcionar para tratamento por parte do Logstash. Supondo que você está monitorando um arquivo de log onde as linhas que iniciarem com `#` devam ser ignoradas, é um "erro" enviar estes evento para o Logstash realizar o drop (ignorar o registro), essa abordagem irá gerar tráfego de rede e custo computacional desnecessário na instância do Logstash, sendo assim, seria muito mais eficiente efetuar o drop ainda na máquina onde o evento foi capturado, efetuar os tratamentos básicos dentro do possível e enviar um conjunto de dados o mais bem organizado e limpo possível.
