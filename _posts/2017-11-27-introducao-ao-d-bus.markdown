---
layout: post
title: "Introdução ao D-Bus"
author: Rodrigo Laneth (@rlaneth)
date:  2017-11-27 00:00:00 -0300
---

> _Apresentando conceitos e demonstrando na prática as aplicações do sistema de comunicação entre processos_

![](/assets/0-Ruq2GfPiSH8Oe_09.png)  

O [D-Bus](https://www.freedesktop.org/wiki/Software/dbus/) é um sistema de IPC (_Inter-Process Communication_, ou comunicação entre processos) criado e mantido como parte do [freedesktop.org](https://www.freedesktop.org/), projeto dedicado à interoperabilidade e ao desenvolvimento de tecnologias compartilhadas entre os ambientes gráficos para o [X Window System](https://www.x.org/), incluindo os populares [GNOME](https://www.gnome.org/) e [KDE](https://www.kde.org/).

Criado para atender às necessidades desses dois últimos, o D-Bus é, hoje, utilizado por vários outros projetos, incluindo o servidor de áudio [PulseAudio](https://www.freedesktop.org/wiki/Software/PulseAudio/) e a _stack_ Bluetooth [BlueZ](http://www.bluez.org/). Além disso, os _bindings_ existentes para as mais diversas linguagens de programação facilitam bastante a sua adoção.

A proposta deste texto é apresentar os conceitos básicos e demonstrar como um desenvolvedor pode fazer uso do sistema. Portanto, não se trata de uma abordagem minuciosa dos detalhes referentes ao seu funcionamento. Materiais recomendados para um maior aprofundamento no tema podem ser encontrados ao final do texto, nas seções “Indo Além” e “Referências”.

# Conceitos

Muitos dos termos utilizados no âmbito da OOP (_Object-Oriented Programming_, ou programação orientada a objetos), como, por exemplo, “objeto” e “método”, são também aplicados ao D-Bus, muito embora para nomear conceitos distintos. Desta forma, é necessário atenção para não confundir os significados.

## Bus

Podemos descrever um _bus_ como um canal através do qual programas se comunicam. Geralmente, e a depender do ambiente analisado, há, por padrão, um _system bus_, único e destinado à troca de informações entre todo o sistema operacional, e um _session bus_ para cada sessão de usuário ativa, onde os dados trafegados se restrigem a ela.

Cada _bus_ possui um endereço para acesso, que frequentemente é o caminho de um _Unix-domain socket_ (soquete de domínio Unix). No entanto, outros esquemas de comunicação podem ser utilizados. É possível, por exemplo, que um _bus_ esteja associado a uma porta TCP, sendo, assim, acessível por meio do endereço dela.

## Bus Name

Os _bus names_ são nomes atribuídos às conexões dos programas a um _bus_. São divididos em dois tipos: _unique connection names_ (nomes únicos de conexão) e _well-known names_ (nomes conhecidos).

Os _unique connection names_ são únicos e imutáveis, atribuídos a cada conexão no momento em que elas são estabelecidas e nunca reutilizados no mesmo _bus_ ao longo de todo o tempo em que ele existir, mesmo que as conexões a que eles estão associados se encerrem. Se iniciam com dois pontos (“:”), uma característica exclusiva, que não pode ser imitada pelos _well-known names_. Exemplo: **:1.72**.

Os _well-known names_ são nomes atribuídos às conexões por solicitação dos programas. Constituem-se de dois ou mais elementos separados por pontos (“.”). Por convenção, adotam o formato de nomes de domínio revertidos. Exemplo: **com.radialle.app**.

Deve-se ressaltar que o termo “_bus name_” **não** se refere ao “nome de um _bus_”, ainda que possa sugerir esta ideia.

## Objeto

Uma das pontas de qualquer comunicação realizada através do D-Bus é um _objeto_, elemento de um programa que se torna acessível aos demais ao ser registrado em um _bus_.

Cada objeto é identificado por um _object path_ (caminho de objeto) como, por exemplo, **/com/radialle/app/example**. Pelos padrões que seguem, os _object paths_ se assemelham aos caminhos de sistemas de arquivos em estilo Unix. No entanto, ao contrário do que poderia-se pensar, eles não necessariamente indicam relações hierárquicas.

Os objetos existem dentro do contexto de uma conexão. Para acessá-los, é necessário conhecer o _bus name_ ao qual o objeto desejado está associado, bem como o seu _object path_. Assim, um _object path_ precisa ser único dentro de uma mesma conexão, embora possa se repetir no _bus_.

Em todo _bus_, há um objeto que representa o próprio _bus_. Ele é disponibilizado através conexão de _bus name_ **org.freedesktop.DBus** e o seu _object path_ é **/**. Dentre as suas funções, estão a de possibilitar a solicitação de _well-known names_ e a de permitir que os programas conectados descubram as demais conexões ativas ao _bus_.

## Proxy

Um _proxy_ é uma representação de um objeto registrado em um _bus_ que existe somente dentro de um programa, sendo de exclusiva responsabilidade do _binding_ utilizado.

Em muitos _bindings_, o _proxy_ tem a forma de um objeto nativo da linguagem de programação utilizada, permitindo que o desenvolvedor acesse um objeto do D-Bus de maneira muito similar a como faria com um objeto regular.

## Método

Os métodos são associados a objetos, e representam operações de um programa que podem ser chamadas pelos demais processos conectados ao mesmo _bus_. Assim como as tradicionais funções das linguagens de programação, ou mesmo os métodos da OOP, eles podem requerer argumentos (_input parameters_, ou parâmetros de entrada) e retornar dados (_output parameters_, ou parâmetros de saída). Se ocorrer um erro durante a execução da operação, eles podem também retornar uma _exceção_ contendo um nome e uma mensagem de erro.

Muitos detalhes costumam ser simplificados pelos _bindings_. É comum, por exemplo, que a chamada de um método através de um _proxy_ seja efetuada com argumentos de entrada de tipos nativos da linguagem de programação utilizada, com o processo de conversão para os tipos suportados pelo D-Bus ficando sob responsabilidade da biblioteca. Similarmente, os parâmetros de saída e as exceções do D-Bus também podem ser convertidos para as suas contrapartes nativas, possibilitando o tratamento pelo desenvolvedor da maneira convencional.

## Sinal

_Sinais_ (_signals_) são um mecanismo através do qual um objeto pode enviar, a qualquer momento, informações aos programas conectados ao _bus_.

É importante notar, no entanto, que os sinais não são automaticamente recebidos por todos os programas conectados ao _bus_; eles precisam se registrar para recebê-los, especificando os sinais desejados. Há, portanto, a possibilidade de um sinal ser emitido sem que existam destinatários registrados e não ser recebido por qualquer conexão.

## Interface

Uma interface especifica um conjunto de _membros_ — ou seja, métodos e sinais — que pode ser implementado por um objeto. A implementação de uma interface é uma promessa de que o objeto conterá todos os membros definidos por ela.

Diversos objetos podem implementar uma mesma interface, bem como múltiplas interfaces podem ser implementadas por um único objeto. Se membros idênticos forem definidos por duas ou mais interfaces implementadas pelo objeto, um programa que tente acessar qualquer um deles utilizando somente o _object path_ e o nome não terá qualquer garantia sobre qual será efetivamente acessado. Para evitar este problema, é importante que se especifique, também, a interface à qual pertence o membro desejado.

## Propriedade

As _propriedades_ são associadas aos objetos e dedicadas ao armazenamento de informações. Elas podem ser lidas ou modificadas, a depender das permissões que lhe foram atribuídas, pelos programas conectados ao _bus_.

A descoberta e o acesso às propriedades são realizados por meio de membros dos objetos. Uma leitura simples do valor contido em uma delas, por exemplo, seria efetuada através de uma chamada ao método **Get** da interface **org.freedesktop.DBus.Properties**, implementada pelo mesmo objeto ao qual a propriedade estivesse associada.

# Prática

Esclarecidos os conceitos básicos relacionados ao D-Bus, devemos passar à prática para fixar o conhecimento e adquirir uma melhor compreensão das possibilidades que o sistema oferece.

Para os fins deste texto, usaremos exemplos escritos em Python 3.x, que utilizam o _binding_ [pydbus](https://github.com/LEW21/pydbus) e foram testados em uma instalação do Debian 9 “Stretch”. Eles devem, no entanto, funcionar na maioria dos ambientes Linux, desde que sejam observados os requerimentos do pydbus — inclusive a necessidade da GLib 2.46 e do girepository 1.46 ou superiores para registrar objetos em um _bus_ — , do interpretador Python e do próprio D-Bus.

## pydbus

Em sua página do GitHub, o pydbus é definido como uma biblioteca “pythônica” para o D-Bus. Seu [_README_](https://github.com/LEW21/pydbus) é simples e direto, contendo diversos exemplos de uso básico do _binding_. No mesmo repositório há, ainda, um [tutorial](https://github.com/LEW21/pydbus/blob/master/doc/tutorial.rst) com informações mais detalhadas e outros _scripts_ de demonstração. Muito embora essas leituras não sejam obrigatórias para a compreensão do restante deste texto, elas são, sem dúvida, recomendadas.

## MPRIS

Para facilitar a integração de _players_ de mídia com outros programas através do D-Bus, foi criada a [MPRIS](https://specifications.freedesktop.org/mpris-spec/latest/) (_Media Player Remote Interfacing Specification_).

Ao definir convenções para a escolha de _bus names_, determinar os objetos que devem existir, seus _paths_, as interfaces que devem implementar, dentre outros padrões, a especificação simplifica o desenvolvimento de programas que fazem uso das informações e dos serviços oferecidos pelas aplicações dedicadas à reprodução de áudio e vídeo.

Como exemplo, podemos destacar que a MPRIS, desde a sua versão 2.0 até a 2.2 — a mais recente disponível no momento da elaboração deste texto — determina o uso de um _unique connection name_ iniciado por **org.mpris.MediaPlayer2.**, o que torna possível obter uma lista de _players_ compatíveis conectados a um _bus_ através da aquisição e filtragem de uma lista completa de _bus names_.

## pdmtools

O [pdmtools](https://github.com/rapidlight/pdmtools) é um conjunto de duas ferramentas em Python criadas especificamente para acompanhar este texto, servindo como exemplo prático do uso do pydbus para interação com outros programas via D-Bus.

A primeira delas, chamada lsmpris, exibe uma lista contendo os nomes e os _bus names_ dos _players_ compatíveis com a MPRIS 2.x conectados ao _bus_ especificado.

A segunda, nomeada fakeplayer, simula um _player_ compatível ao solicitar um _bus name_ iniciado por **org.mpris.MediaPlayer2.** e registrar um objeto com o _path_ **/org/mpris/MediaPlayer2** contendo a propriedade **Identity**. Obviamente, esta é a implementação de apenas um pequeno fragmento da MPRIS, útil somente para fins de estudo e para _debugging_ da lsmpris.

## Ferramentas do D-Bus

Os _releases_ do D-Bus incluem ferramentas de linha de comando destinadas à interação com o sistema. Dentre elas, encontram-se a [dbus-send](https://dbus.freedesktop.org/doc/dbus-send.1.html), que possibilita o envio de mensagens aos objetos especificados, e a [dbus-monitor](https://dbus.freedesktop.org/doc/dbus-monitor.1.html), para monitoramento das informações trafegadas em um _bus_.

Vemos, a seguir, um exemplo de utilização da dbus-send. Nele, a ferramenta é usada para chamar o método **Get** da interface **org.freedesktop.DBus.Properties**, implementada pelo objeto **/org/mpris/MediaPlayer2** associado à conexão que utiliza o _bus name_ **org.mpris.MediaPlayer2.fakeplayer**. Especificam-se, como parâmetros de entrada, as strings “org.mpris.MediaPlayer2” e “Identity”, que correspondem, respectivamente, à interface e ao nome da propriedade cujo valor deseja-se obter.

```
dbus-send \
--print-reply \
--dest=org.mpris.MediaPlayer2.fakeplayer \
/org/mpris/MediaPlayer2 \
org.freedesktop.DBus.Properties.Get \
string:"org.mpris.MediaPlayer2" string:"Identity"

```

Uma das tarefas que podem ser cumpridas com a dbus-monitor é, conforme demonstrado a seguir, a observação dos sinais emitidos por uma determinada conexão. A ferramenta é chamada com um filtro como parâmetro, de maneira que as únicas mensagens trocadas através do _bus_ a serem exibidas por ela sejam os sinais originados da conexão de _bus name_**org.mpris.MediaPlayer2.vlc**, normalmente associada a uma instância do [VLC media player](https://www.videolan.org/vlc/).

```
dbus-monitor \
"type='signal',sender='org.mpris.MediaPlayer2.vlc'"

```

Nos dois exemplos anteriores, não é especificado um _bus_ ao qual as ferramentas devem se conectar. Para este caso, ambas adotam como padrão o uso do _session bus_.

## D-Feet

Ainda que a possibilidade de interagir com o D-Bus diretamente pela linha de comando seja bastante útil, inclusive para automatização com _shell scripts_, não se trata de uma maneira fácil de explorar e compreender as conexões ativas a um _bus_, os objetos associados a elas, seus métodos, sinais e propriedades. Uma melhor alternativa é o [D-Feet](https://wiki.gnome.org/Apps/DFeet), um _software_ com interface gráfica dedicado às tarefas de _debugging_ relacionadas ao D-Bus e mantido como parte do projeto GNOME.

![](/assets/image-13.png)

Por padrão, o D-Feet exibe, no topo de sua janela principal, as abas “System Bus” e “Session Bus”, que, como os nomes indicam, correspondem, respectivamente, ao _system bus_ e ao _session bus_ da sessão de usuário ativa. No canto superior direito, um botão hambúrguer dá acesso às opções de conectar a ferramenta a um outro _bus_, ou encerrar uma conexão existente.

Ao lado esquerdo da mesma janela, existe uma lista de _bus names_, correspondente à aba selecionada. Clicando sobre um dos itens, surge, do lado direito, uma lista com os objetos associados à conexão e todas as informações referentes a eles.

![](/assets/image-14.png)

No exemplo acima, vemos o D-Feet exibir o único objeto criado pela fakeplayer, de _object path_ **/org/mpris/MediaPlayer2**, que, por sua vez, define a propriedade **Identity**, com permissão somente de leitura. Foi efetuado um duplo clique sobre o seu nome para que o valor associado fosse exibido: “Fake Player”.

Nota-se que a aplicação simplifica o acesso às propriedades, invocando o método e exibindo os resultados de maneira transparente para o usuário. No entanto, como ela também permite chamar métodos, podemos usá-la para acionar o **Get** da interface **org.freedesktop.DBus.Properties**manualmente, se assim desejarmos.

![](/assets/image-15.png)

Uma das funções que não faz parte da ferramenta até a mais recente versão disponível durante a elaboração deste texto (0.3.11) é a de receber os sinais emitidos, muito embora ela permita visualizar quais deles são definidos pelas interfaces que os objetos implementam.

# Indo Além

Há aspectos mais avançados do D-Bus que, apesar de não detalhados neste texto, merecem ser mencionados, uma vez que podem ser bastante úteis para aqueles que pretendem se aprofundar na utilização do sistema.

## Ativação

Um programa com suporte à _ativação_ via D-Bus não precisa permanecer em execução para receber as mensagens a ele direcionadas; o sistema se encarregará de chamá-lo quando for solicitado.

O desenvolvimento de um _software_ compatível com esse recurso envolve a criação de uma _service file_, que fornece ao D-Bus informações como o caminho para o programa a ser executado e o _bus name_ a ser utilizado.

Detalhes podem ser encontrados em documentos como o tutorial “[_Creating and Installing D-Bus Autostart Services_](https://techbase.kde.org/Development/Tutorials/D-Bus/Autostart_Services)”, publicado na [KDE TechBase](https://techbase.kde.org/Welcome_to_KDE_TechBase).

## Segurança

Há diversos recursos para ajudar a garantir a segurança dos programas conectados e das informações trocadas por meio do D-Bus.

O sistema permite que se configure um _bus_ para requerer autenticação, de forma que aplicações não possam se conectar a ele sem apresentar as credenciais adequadas. Também é possível aplicar uma _security policy_ (política de segurança), o que permite a filtragem dos dados trafegados por características como origem, destinatário e conteúdo.

Normalmente, o _system bus_ adota, por padrão, uma política de segurança bastante restritiva, impedindo, por exemplo, a solicitação de _bus names_ e a chamada de qualquer método por processos que não tenham sido explicitamente autorizados.

Mais informações a respeito podem ser encontradas na documentação do [dbus-daemon](https://dbus.freedesktop.org/doc/dbus-daemon.1.html).

# Referências

-  “[_D-Bus Specification_](https://dbus.freedesktop.org/doc/dbus-specification.html)”, especificação do D-Bus
-  “[_Introduction to D-Bus_](https://www.freedesktop.org/wiki/IntroductionToDBus/)”, parte da documentação oficial
-  “[_D-Bus Tutorial_](https://dbus.freedesktop.org/doc/dbus-tutorial.html)”, tutorial oficial
-  “[_D-Bus Overview_](https://pythonhosted.org/txdbus/dbus_overview.html)”, parte do projeto Tx DBus
-  “[_dbus-daemon_](https://dbus.freedesktop.org/doc/dbus-daemon.1.html)”, documentação oficial do dbus-daemon
-  “[_dbus-send_](https://dbus.freedesktop.org/doc/dbus-send.1.html)”, documentação oficial do dbus-send
-  “[_dbus-monitor_](https://dbus.freedesktop.org/doc/dbus-monitor.1.html)”, documentação oficial do dbus-monitor