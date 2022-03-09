---
layout: post
title: "Slack como sistema de notificações"
author: Rodrigo Laneth (@rlaneth)
date:  2018-10-19 00:00:00 -0300
---

> _Popular ferramenta de colaboração é útil para gerenciar avisos_


![Slack como sistema de notificações](/assets/1-fJJwanZUQS644-LV54J1pw.png)  
[Imagem: Scott Web/Unsplash](https://unsplash.com/photos/bmmcfZqSjBU)

Há alguns dias, precisei de uma solução para notificações que atendesse a alguns requisitos específicos: as mensagens poderiam ser originadas de um ou mais sistemas, devendo ser separadas em categorias e entregues aos aparelhos Android e iOS dos usuários autorizados.

Como nem todos os usuários teriam autorização para receber todas as notificações, também era extremamente importante poder restringir a quem elas seriam entregues com base na categoria em que se enquadrassem.

Considerei diversas possibilidades, incluindo a de desenvolver um sistema próprio, mas os custos seriam elevados: a implementação de  _push notifications_  em um app para iOS, por exemplo, requer participação no  [Apple Developer Program](https://developer.apple.com/programs/), a uma taxa de US$99/ano — preço pouco atrativo quando o objetivo é simplesmente permitir que algumas pessoas recebam notificações.

Por fim, optei por utilizar o  [Slack](https://slack.com/), uma conhecida ferramenta de comunicação para equipes. Embora possa não parecer uma escolha óbvia a princípio, notei que era a maneira mais eficiente de resolver a questão.

### Slack

Para compreender como a plataforma atende aos requisitos apresentados, é preciso conhecer o básico sobre o seu funcionamento, especialmente no que diz respeito aos  [_workspaces_](https://get.slack.help/hc/en-us/articles/212675257-Join-a-Slack-workspace)  e suas subdivisões, os canais.

Cada  _workspace_  é um espaço onde um grupo de pessoas — que podem ser, por exemplo, os funcionários de uma empresa, os estudantes de uma universidade ou os membros de um  _makerspace_  — se comunica, e o seu acesso é restrito de acordo com as configurações e convites enviados pelos administradores.

Além disso, cada canal em um  _workspace_  pode ser aberto a todos os membros do espaço, ou apenas para convidados.

O Slack conta com  [apps](https://slack.com/download)  para as mais diversas plataformas, incluindo  [Android](https://slack.com/downloads/android)  e  [iOS](https://slack.com/downloads/ios). Por meio deles, os usuários podem ser notificados quando mensagens são enviadas aos canais dos quais eles participam.

Também é possível alterar configurações de notificação de acordo com o canal — de maneira que um usuário pode, por exemplo, silenciar as notificações de mensagens do canal #a, mas continuar recebendo as do #b.

#### API

A API do Slack permite a publicação nos diversos canais de um  _workspace_, de forma que as notificações de um sistema A podem ser enviadas ao canal #a, e as de um sistema B, ao canal #b. Desta forma, satisfazem-se as necessidades de separação em categorias e controle de acesso.

O recurso que permite a publicação de mensagens por meio da API é chamado de  [_Incoming Webhooks_](https://api.slack.com/incoming-webhooks), e os passos para utilizá-lo são, em resumo, criar um  [Slack app](https://api.slack.com/docs/slack-button#register_your_slack_app),  [ativar a função](https://api.slack.com/apps)  e, em seguida, as URLs através das quais as publicações serão feitas.

![](/assets/image-18.png)

Autorizando a publicação via Incoming Webhooks no canal  **#general**

O uso dos  _Incoming Webhooks_  é extremamente simples, bastando o envio de uma solicitação POST às URLs geradas. Assim, é possível inclusive efetuar publicações através da ferramenta de linha de comando  [curl](https://curl.haxx.se/), conforme exemplificado a seguir.

```
curl -X POST -H 'Content-type: application/json' --data '{"text":"Hello, World!"}' <Webhook URL criada pelo Slack aqui>

```

#### Custos

O Slack  [oferece um plano gratuito](https://slack.com/pricing)  que, embora com limitações — como a restrição de visualização além das últimas 10 mil mensagens enviadas ao espaço — , provou atender perfeitamente ao meu caso de uso. Além disso, não há necessidade de investir na manutenção de servidores da aplicação, uma vez que se trata de solução hospedada.

### Alternativa: Mattermost

Quando comentei em um pequeno canal privado do Telegram sobre o uso do Slack para notificações, fui lembrado da existência do  [Mattermost](https://mattermost.com/). Menciono-o aqui pois pode ser uma opção mais adequada para casos de uso distintos, ainda que similares ao descrito.

O Mattermost é uma alternativa ao Slack que pode ser obtida e instalada pelo usuário em servidor próprio. Funciona, portanto, como um produto, e não como um serviço hospedado. Há uma versão gratuita e open source, licenciada sob os termos da licença MIT.

Tal como o Slack, o Mattermost suporta  [_Incoming Webhooks_](https://docs.mattermost.com/developer/webhooks-incoming.html), sendo assim uma outra opção viável para uso como sistema de notificações.

### Conclusão

O Slack, embora apresentado como ferramenta de comunicação para equipes, funciona também como uma excelente plataforma para entrega e gestão de notificações, oferecida a um custo bastante reduzido — e muitas vezes gratuita.

Conhecer esta possibilidade de utilização do serviço pode ser particularmente interessante para pessoas e pequenos grupos que se identificam com a  [cultura  _maker_](https://epocanegocios.globo.com/colunas/noticia/2018/04/um-guia-para-se-relacionar-com-cultura-maker.html)  e desenvolvem projetos envolvendo o envio de notificações para aparelhos como smartphones e tablets.

Há ainda o Mattermost, alternativa ao Slack, que, apesar de requerer hospedagem e manutenção, a depender do caso de uso, pode representar uma melhor opção.