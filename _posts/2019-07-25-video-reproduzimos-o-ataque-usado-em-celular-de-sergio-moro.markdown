---
layout: post
title:  "VÍDEO: Reproduzimos o ataque usado em celular de Sérgio Moro"
author: Rodrigo Laneth (@rlaneth)
date:   2019-07-25 00:00:00 -0300
---

> _Por meio das informações divulgadas, remontamos o incidente e demonstramos o uso da técnica para ativação do Telegram_


![VÍDEO: Reproduzimos o ataque usado em celular de Sérgio Moro](/assets/0-4x7DmHwB0VuhwSwn.jpg)  
[Foto: Fabio Rodrigues Pozzebom/Agência Brasil](http://agenciabrasil.ebc.com.br/ccj-da-camara-dos-deputados-ouve-o-ministro-sergio-moro-0)

**5 de junho de 2019.** “Um hacker invadiu o celular do ministro Sergio Moro (Justiça) nesta terça-feira (4)”, publica a  [Folha de S. Paulo](http://archive.is/zCxC4). “O celular teria sido hackeado por volta das 18h, segundo o Ministério da Justiça, quando Moro atendeu uma ligação feita pelo próprio número dele”, divulga o  [G1](http://archive.is/B0AIO).

Imediatamente após o ocorrido, profissionais e entusiastas de tecnologia da informação, incluindo desenvolvedores e pesquisadores de segurança, tentam interpretar as informações, muitas vezes distorcidas, que vêm a público. Um deles, Fabio Assolini, da Kaspersky, escreve uma  [_thread_](http://archive.is/EYkua)  no Twitter sobre os possíveis métodos usados para o ataque.

Dentre as possibilidades, Assolini cita  [SIM swap](https://gizmodo.uol.com.br/sim-swap-tecnica-clona-whatsapp/), fraude que consiste em habilitar a linha do alvo em um cartão SIM do atacante por meio de engenharia social ou acesso privilegiado na operadora de telefonia. A mesma teoria foi levantada e defendida por inúmeros outros — e dentre eles, me incluo. Em 6 de junho,  [escrevi](http://archive.is/EUNxY): “_pelo que se sabe atualmente, não teve ‘celular hackeado’, e sim sequestro do número com participação de alguém da operadora_”.  

**23 de julho de 2019.**  A Polícia Federal  [deflagra](http://archive.is/kiLsg)  a operação Spoofing, cumprindo sete mandados de busca e apreensão e quatro de prisão temporária contra suspeitos de envolvimento na “invasão do celular de Moro”. Já no dia 24, a íntegra da  [decisão](https://cdn.oantagonista.net/uploads/2019/07/DECISAO.pdf)  para prisão dos “supostos hackers” divulgada pelo O Antagonista nos fornece acesso às conclusões da investigação da PF.

> _O Telegram permite que o usuário solicite o código de acesso via ligação telefônica com posterior envio de chamada de voz contendo o código para ativação do serviço Web, cuja mensagem fica gravada na caixa postal das vítimas. O invasor então realiza diversas ligações para o número alvo, a fim de que a linha fique ocupada, e a ligação contendo o código de ativação do serviço Telegram Web é direcionada para a caixa postal da vítima._

Embora o documento não esclareça alguns pontos, as informações disponíveis nos permitem remontar com bom nível de confiança o ocorrido.

A esta altura, parece inquestionável que  **não houve invasão ao celular de Moro.**  Nenhum acesso indevido ao dispositivo utilizado pelo ex-magistrado, mas sim a utilização do número de sua linha telefônica para criação de uma conta no Telegram.

E é fato que o Telegram permite o envio do código de verificação para criação de uma nova conta ou login em uma conta já existente através de uma chamada de voz, e que esta pode ser encaminhada para a caixa postal do receptor se a linha encontrar-se ocupada ou se a ligação for manualmente rejeitada.

A peça perdida do quebra-cabeças é o que ocorre em seguida:  **o meio utilizado pelo atacante para acessar a caixa postal do alvo.**  Podemos deduzí-lo através da decisão judicial e do nome da operação Spoofing.

Para isso, deve-se saber que é possível efetuar ligações telefônicas falsificando a informação de origem, identificando a chamada como se houvesse partido de um número telefônico arbitrário. Esta técnica é conhecida como  [_caller ID spoofing_](https://www.fcc.gov/consumers/guides/spoofing-and-caller-id)  e tem aplicações legítimas, mas obviamente também pode ser explorada para fins maliciosos.

De volta à decisão:

> _A edição de números telefônicos pode ser realizada através de serviços de voz sobre IP (VOIP) ou por aplicativos que permitem a modificação do número chamador. (…) O cliente/usuário da BRVOZ utilizando a função ‘identificador de chamadas’ pode realizar ligações telefônicas simulando o número de qualquer terminal telefônico como origem das chamadas._

Segundo o texto, o atacante utilizou o serviço de telefonia VoIP da  [BRVOZ](http://www.brvoz.com.br/). Após a divulgação das notícias, tentei contratar o serviço, mas tive o pagamento estornado e não consegui contato com os canais de atendimento. Presumidamente, a empresa encontra-se sobrecarregada devido à repentina atenção midiática.

No entanto, com uma breve pesquisa no Google, encontrei outra fornecedora que também possibilita o  _caller ID spoofing_, a  [Nvoip](https://www.nvoip.com.br/). Desta vez, consegui criar uma conta.

Utilizei um número da operadora TIM para testes. Constatei que, ao efetuar uma chamada para ele com o “identificador de chamadas” da Nvoip configurado para o mesmo número, quando o celular encontra-se indisponível — seja por estar ocupado com outra ligação, fora de área ou por rejeição manual da chamada recebida — , o acesso à caixa postal torna-se possível.

Tal cenário encaixa-se perfeitamente com o que vem sendo divulgado desde junho, incluindo as ligações que Sérgio Moro relatou haver recebido do próprio número. Com a confiança de que esta é de fato a solução do mistério, reproduzi o ataque para gravar o vídeo de demonstração a seguir.

<iframe width="560" height="315" src="https://www.youtube.com/embed/ILwIaHdueLM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Como se proteger

A utilização da  **autenticação de dois fatores (2FA)**  é sempre recomendada: embora ela não possa impedir a criação de uma nova conta no Telegram, como ocorreu no caso de Moro, ela evitaria o acesso a uma conta já existente.

Fora do Telegram, é importante configurar suas contas para  **utilizar formas mais robustas de 2FA**  do que o recebimento de código por ligação, uma vez que estes estariam vulneráveis ao mesmo método. E, devido a outras possibilidades de ataque, o uso de SMS também não é recomendado. As alternativas incluem o uso de um aplicativo como o  [Google Authenticator](https://support.google.com/accounts/answer/1066447)ou de um dispositivo de autenticação por hardware como o  [YubiKey](https://www.yubico.com/getstarted/meet-the-yubikey/).

No mais, deve-se  **desativar o serviço de caixa postal**  caso ele não seja utilizado. Consulte sua operadora em relação aos procedimentos para desativação.

**Atualização (28/07/2019):**  com a repercussão do caso de Moro, alterações têm sido realizadas para evitar ocorrências similares. Agora, no caso de contas já existentes, o Telegram  [só permite o envio do código via ligação se a autenticação de dois fatores estiver ativada](https://twitter.com/telegram_br/status/1154868292982628353), embora ainda seja possível solicitar a chamada para cadastro de um número de telefone no serviço. Também identificamos que as operadoras estão buscando corrigir a falha que permitia o acesso indevido à caixa postal. No entanto, para maior proteção dos usuários, inclusive contra outros tipos de ataque,  **as recomendações feitas neste texto estão mantidas.**

----------

_Agradecimentos especiais a **Davidson Francis**, **Gustavo Oliveira** e **Shrimp**, integrantes da Radialle, que ajudaram na verificação dos fatos e na preparação deste conteúdo para publicação. As legendas do vídeo de demonstração foram produzidas por Davidson._