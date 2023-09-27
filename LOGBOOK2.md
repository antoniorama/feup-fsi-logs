# Trabalho realizado nas Semanas #2 e #3

## 	CVE-2023-39147

## Identificação

- Vulnerabilidade no software UVDesk versão 1.1.3 que permitia o upload de ficheiros arbitrários de código e execução na aplicação.
- Relevante em aplicações e websites cujos quais utilizavam o software descrito.

## Catalogação

- Reportada por Daniel Barros no dia 28/07/2023, em nome da empresa Hakai Offensive Security.
- Classificada no dia 01/08/2023 com um nível de gravidade HIGH, a 7.8.
- Não eram necessários privilégios nenhuns para executar o ataque.
- Mais informação: https://nvd.nist.gov/vuln/detail/CVE-2023-2454

## Exploit

- Classifica-se como uma Execução de Código Remota, proveniente de upload de ficheiros.
- Neste caso, o atacante decidiu dar upload de um ficheiro PHP com código malicioso na criação de um ticket. 
- Sendo que só aceitava imagens, o atacante interceptou o pedido ao servidor e mudou o tipo do conteúdo para “image/jpg”.
- O servidor aceitou o ficheiro PHP e o atacante executou o código ao abrir o ficheiro enviado num novo separador.

## Ataques

- Não encontramos relatos de nenhum ataque feito utilizando esta vulnerabilidade.
- No entanto, devido ao tipo de exploit, podemos concluir que esta vulnerabilidade tinha um potencial elevado para causar danos.
- Potencial para instalar malware no servidor, causar um ataque DoS ou exfiltrar informação do website, como credenciais de utilizadores.
- Mais informação: https://en.wikipedia.org/wiki/Arbitrary_code_execution




## Write-up

Começámos por explorar o website para fazer um reconhecimento geral. Percebemos que era uma loja online feita em Wordpress. Encontrámos uma review que dizia que os plugins de Wordpress que este website estava a usar deviam ser utilizados.

A partir daqui fomos tentar encontrar as versões dos plugins que estavam a ser utilizados e encontrámo-las listadas em "Additional information". Há 2 plugins que estão a ser usados : 
- WooCommerce plugin 5.7.1
- Booster for WooCommerce plugin 5.4.3

Com uma pesquisa na internet encontrámos uma vulnerabilidade para o plugin Booster for WooCommerce CVE(2021-34646) de Authentication Bypass, e aqui está a primeira (FLAG).

Ao correr o script conseguimos ter acesso à admin account. A partir daqui navegámos para o post privado que estava mencionado no enunciado e encontrámos a segunda FLAG.
