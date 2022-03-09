---
layout: post
title:  "Do Arduino ao AVR"
author: Rodrigo Laneth (@rlaneth)
date:   2016-12-26 00:00:00 -0300
---

> _Migrando da plataforma de prototipagem aos microcontroladores_

![Encapsulamento de Circuitos Integrados](/assets/arduino-leonardo-attiny85-cover.jpg) 

É difícil encontrar, nos dias de hoje, alguém que se interesse por tecnologia e não conheça o Arduino. Ele ficou famoso ao longo dos últimos anos e protagoniza inúmeros projetos, incluindo  [sistemas de monitoramento para plantas](https://www.hackster.io/ryanjgill2/plant-monitoring-system-88ed2b)  e  [LEDs controlados via Minecraft](http://hackaday.com/2016/06/04/control-the-real-world-with-an-arduino-enabled-minecraft-mod/).

Seu sucesso é merecido. A plataforma ajuda e incentiva milhares de pessoas a ingressarem e se desenvolverem no ramo da eletrônica. Basta o conhecimento básico sobre programação para começar a utilizá-la na criação de protótipos e dispositivos eletrônicos.

Mas temos que admitir: nem toda ideia pode sair da  _protoboard_ dependendo do Arduino. Toda a conveniência que ele oferece tem um custo, e dificilmente encontramos uma situação onde embutir a placa no produto final não faz com que ele fique mais caro, maior, e pareça menos profissional do que poderia ser.

Se o plano é comercializar o produto em questão, desvantagens como essas não seriam aceitáveis. Para projetos pessoais e trabalhos acadêmicos, depende do que se pretende fazer, mas mesmo quando eles podem ser tolerados, os pontos negativos nunca são desejáveis.

Foi trabalhando em um experimento que tinha como objetivo a criação de um dispositivo  _wearable_, no qual nem os menores Arduinos e clones poderiam ser encaixados, que eu vi que era a hora de começar a usar microcontroladores avulsos.

## Microcontroladores

Eles são componentes programáveis, quase como mini-computadores, que podem ser adaptados da forma que melhor se encaixar no circuito.

Não é coincidência que essa descrição seja parecida com a dos Arduinos. Eles  **não são**  microcontroladores, mas  **contém**  um, onde os programas são de fato gravados e executados. O diferencial das placas são os elementos para facilitar o uso, permitindo, por exemplo, a programação diretamente via USB.

Vejamos o Arduino Uno. Este modelo usa o microcontrolador  [ATmega328P](http://www.atmel.com/pt/br/devices/ATMEGA328P.aspx), presente na placa no local destacado na imagem a seguir.

![](/assets/image.png)  
_Arduino Uno (foto da placa via  [Arduino Blog](https://blog.arduino.cc/2013/10/23/open-source-matters-in-hardware-too-interview/), licença  [CC BY-SA 3.0](https://www.arduino.cc/en/Main/CopyrightNotice))_

Uma característica que diferencia o Uno dos outros modelos é que o microcontrolador não é soldado nele, mas sim encaixado sobre um soquete, do qual pode ser removido com relativa facilidade.

Assim, é possível usar a placa apenas para gravar e testar o programa, e, em seguida, utilizar o ATmega328P de forma independente, como demonstra o vídeo a seguir.

**Mas atenção:**  se você pretende repetir o feito, tenha em mente que é importante tomar precauções anti-estática antes de manusear o chip, para evitar danificá-lo. Além disso, saiba que ele precisa ser ligado a dois capacitores.

Um deles deve ser conectado entre o pino 9 e GND (_ground_), e o outro entre o 10 e GND, conforme indicação do autor do vídeo. Por mais que o próprio afirme que “costuma funcionar sem” e faça a demonstração dessa forma, não significa que isso seja recomendável.

Lembre-se de que é importante ter um bom conhecimento em inglês e consultar sempre o  _datasheet_  do componente que você vai utilizar.

## Apresentando os AVRs

O ATmega328P é parte da família AVR, da Atmel, que inclui as linhas ATtiny e ATxmega, além da própria ATmega. São microcontroladores 8-bits com base na  [arquitetura Harvard modificada](https://pt.wikipedia.org/wiki/Arquitetura_Harvard#Arquitetura_de_Harvard_modificada).

É importante destacar que a Atmel apresentou, mais recentemente, chips de 32-bits que também levam o nome “AVR”, apesar de não serem tão parecidos com os originais. Para evitar confusão, pode-se usar o termo “AVR” **apenas para os de 8-bits**, como faço aqui, e “AVR32” para os novos, de 32-bits.

Para escolher e começar a utilizar um AVR, deve-se conhecer os tipos de armazenamento usados por eles. Seguem.

-   **Memória Flash**  
    Persistente, usada para o armazenamento do programa a ser executado pelo microcontrolador.  

-   **SRAM (_Static Random Access Memory_)**  
    Provê armazenamento temporário para as informações utilizadas pelo programa em execução, similar à memória RAM de um computador comum.  

-   **EEPROM (_Electrically-Erasable Programmable Read-Only Memory_)**  
    Pode ser usada pelo programa para armazenamento persistente de dados. As informações nela escritas permanecem mesmo após o microcontrolador ser desligado.

Talvez você já tenha percebido que tudo isso também existe no Arduino. Os programas escritos para a placa são, afinal, armazenados e executados pelo AVR presente nela.

Um dos momentos em que isto pode ser facilmente notado é logo após a compilação de um  _sketch_  pela Arduino IDE, quando é possível visualizar mensagens referentes à memória Flash e SRAM.

![](/assets/image-3.png)

Além disso, existe uma  [biblioteca](https://www.arduino.cc/en/Reference/EEPROM)  para manipulação da EEPROM, distribuída com a Arduino IDE.

Outro aspecto dos AVRs que deve ser mencionado é a utilização de  **fusíveis**para a configuração do chip. As opções variam de acordo com o modelo, mas um exemplo é o controle da velocidade do microcontrolador, e se a fonte de  _clock_  a ser utilizada será a interna ou uma externa.

E como se configura e programa um AVR, se eles não podem ser ligados diretamente à porta USB do computador? A forma mais comum é através do ISP (_In-System Programming_). Para isso, é necessário utilizar um aparelho também chamado de ISP (_In-System_ **_Programmer_**, neste contexto).

Dentre os ISPs existentes no mercado, está o  [AVRISP](http://www.atmel.com/pt/br/tools/AVRISPMKII.aspx), da própria Atmel. Mas se você tiver um Arduino, provavelmente não vai precisar disso, já que eles geralmente podem servir ao mesmo propósito.

Quanto ao software, a ferramenta oferecida pela Atmel para a programação dos microcontroladores é a  [Atmel Studio](http://www.atmel.com/microsite/atmel-studio/)  (antiga AVR Studio), que permite o desenvolvimento em Assembly, C ou C++, e auxilia no processo de gravação do chip.

Mas como aqui estamos tratando da transição do Arduino ao AVR, falaremos do uso da própria Arduino IDE no lugar da Atmel Studio, o que torna a mudança muito mais confortável para quem já se acostumou com a plataforma.

Para prosseguir com demonstrações, precisamos determinar um modelo específico de Arduino e de AVR para utilizar. O processo para transformar a placa em um ISP e realizar a gravação do microcontrolador é similar para todos os modelos, mas as variações existem.

Portanto, este é o momento para deixar de falar em termos gerais e começar a abordar o Arduino Leonardo e o ATtiny85. O primeiro é o Arduino que tenho em mãos, e o segundo, o microcontrolador que escolhi para o experimento do  _wearable_.

## Conhecendo o ATtiny85

É um microcontrolador barato, que custa, no Brasil, aproximadamente R$10, e de tamanho bastante reduzido. As  [especificações](http://www.atmel.com/pt/br/Images/Atmel-2586-AVR-8-bit-Microcontroller-ATtiny25-ATtiny45-ATtiny85_Datasheet-Summary.pdf)  do modelo  **20PU**, pelo qual optei devido ao  [encapsulamento](https://radialle.com/encapsulamento-de-circuitos-integrados-5d1ab80ec5f), indicam aproximadamente 8 milímetros de largura por 10 de comprimento e 8,6 de altura.

Ele oferece 8kB de memória Flash, 512 bytes de EEPROM, 512 bytes de SRAM e cinco pinos de E/S (entrada e saída) — seis, se contar com o RESET.

Sim, pode-se usar o RESET, que normalmente serve para que o ISP possa reiniciar o microcontrolador e gravar nele um novo programa, como um pino de E/S, por meio da configuração do fusível RSTDISBL.

Porém, há uma dificuldade: fazer isso impossibilita a programação do AVR com um ISP. A partir do momento em que a alteração do fusível é feita, o chip só pode voltar a ser programado com um HVSP (_High-Voltage Serial Programmer_), mais caro e difícil de encontrar ou fazer.

Assim, o melhor é ignorar a possibilidade, a não ser que você realmente precise da função e tenha acesso a, ou esteja disposto a comprar ou montar, um HVSP. Antes, talvez você queira considerar os outros modelos de AVR maiores e com mais pinos, que existem inclusive na linha ATtiny.

Quanto aos cinco pinos de E/S restantes, dois suportam PWM (_Pulse-Width Modulation_) e os outros três podem funcionar como entrada analógica, conforme indicado pela imagem.

![](/assets/image-4.png)  
_Pinos do ATtiny85 (simplificado)_

No esquema acima, há siglas referentes ao ISP: MISO (_Master In Slave Out_), que indica o pino usado para envio de dados do ATtiny85 ao programador; MOSI (_Master Out Slave In_), para os dados do programador ao ATtiny85; e SCK (_Serial Clock_), para sincronização da comunicação.

Como se descobre a posição correta do chip para identificar os pinos? Bom, normalmente os circuitos integrados — não só os microcontroladores — vêm marcados com um chanfro, da maneira como foi ilustrado acima, ou com uma bolinha ao lado do primeiro pino, ou ambos.

![](/assets/image-5.png)  
_Marcação no ATtiny85–20PU_

## Programando o ATtiny85

Há, no Arduino Leonardo, um cabeçalho denominado ICSP (_In-Circuit Serial Programming_). Os seis pinos nele existentes correspondem a 5v, GND, RESET, MISO, MOSI e SCK, conforme abaixo.

![](/assets/image-6.png)  
_Cabeçalho ICSP do Arduino Leonardo (foto da placa via  [Arduino](https://www.arduino.cc/en/Main/CopyrightNotice), licença  [CC BY-SA 3.0](https://www.arduino.cc/en/Main/CopyrightNotice))_

Para programar o chip com a placa, o que precisamos fazer é conectar os pinos do Arduino aos do ATtiny85, observando a função de cada um. O MISO deve ser ligado ao MISO, o MOSI ao MOSI, e assim por diante.

**_Plot twist_: o RESET é exceção.**  O do cabeçalho ICSP serve para reiniciar o microcontrolador do Arduino, e não do outro dispositivo. Por esta razão, usaremos o pino 10 para a tarefa.

Mas antes de prosseguir com as conexões, falemos das preparações necessárias para programar o chip usando a placa e a Arduino IDE. Em primeiro lugar, transformamos o Arduino Leonardo em um ISP seguindo estes passos:

1.  **Abrir o  _sketch_  ArduinoISP na Arduino IDE**  
    Ele pode ser encontrado em  `Arquivo  >  Exemplos  >  ArduinoISP`.  

2.  **Verificar se o pino 10 será usado como RESET**  
    A definição deverá estar correta no  _sketch_:  `#define RESET 10`.  

3.  **Conectar LEDs ao Arduino Leonardo** (opcional)  
    Por padrão, o  _sketch_  usa o pino 9 para um LED de  _heartbeat_, que mostra que o programador está rodando, o 8 para um indicador de erro, e o 7 para um indicador de comunicação com o microcontrolador. Você pode ligá-los ou modificar os pinos se quiser, mas não é estritamente necessário.  

4.  **Carregar o  _sketch_  para a placa**  
    Ligar o Arduino Leonardo ao computador, se certificar de que o modelo da placa e a porta estão configurados corretamente na Arduino IDE, e clicar em “Carregar”. Procedimento idêntico ao de qualquer outro  _sketch_.

Em seguida, dizemos à Arduino IDE como ela deve se comunicar com o ATtiny85, utilizando o Arduino Leonardo para configurar o chip e instalar programas nele, da seguinte maneira:

1.  **Localizar a pasta de instalação da Arduino IDE**  
    Se você usa Windows, tente  `C:\Arquivos de Programas (x86)\Arduino`. No macOS, dê um clique secundário no Arduino.app e escolha “Mostrar Conteúdo do Pacote”.  

2.  **Encontrar o arquivo  _programmers.txt_**  
    Dentro da pasta de instalação da Arduino IDE, deverá estar em  `hardware\arduino\avr`.  
    Ex.:  `C:\Arquivos de Programas (x86)\Arduino\hardware\arduino\avr\programmers.txt`  (Windows);  `/Applications/Arduino.app/Contents/Java/hardware/arduino/avr/programmers.txt`(macOS).  

3.  **Adicionar a configuração para usar o Arduino Leonardo como ISP**  
    Colocar as linhas a seguir no final do arquivo.

```
leonardoisp.name=Leonardo as ISP
leonardoisp.communication=serial
leonardoisp.protocol=arduino
leonardoisp.speed=19200
leonardoisp.program.protocol=arduino
leonardoisp.program.speed=19200
leonardoisp.program.tool=avrdude
leonardoisp.program.extra_params=-P{serial.port} -b{program.speed}

```

E para finalizar, instalamos o pacote  **attiny**, do desenvolvedor  [David Mellis](https://github.com/damellis), na Arduino IDE:

1.  **Adicionar a URL ao Gerenciador de Placas**  
    Em  `Arquivo  >  Preferências`  (`Arduino  >  Preferências`  no macOS), colar o endereço a seguir no campo “URLs Adicionais de Gerenciadores de Placas”.  
    `[https://raw.githubusercontent.com/damellis/attiny/ide-1.6.x-boards-manager/package_damellis_attiny_index.json](https://raw.githubusercontent.com/damellis/attiny/ide-1.6.x-boards-manager/package_damellis_attiny_index.json)`  

2.  **Abrir o Gerenciador de Placas da Arduino IDE**  
    Ele se encontra em  `Ferramentas  >  Placa  >  Gerenciador de Placas`.  

3.  **Pesquisar por “attiny”**  
    Basta digitar o nome na caixa de busca na parte superior da janela do gerenciador.  

4.  **Instalar a última versão do pacote  _attiny by David A. Mellis_**  
    Clicar sobre o elemento, certificar-se de que a versão mais recente está selecionada na caixa de seleção e clicar em “Instalar”.

![](/assets/image-7.png)  
_Instalação do pacote attiny pelo Gerenciador de Placas da Arduino IDE_

Agora estamos prontos para fazer as ligações e começar a interagir com o ATtiny85. Segue o esquema.

![](/assets/image-8.png)  
_Esquema de ligação do Arduino Leonardo com o ATtiny85_

Com os fios ligados e o Arduino Leonardo conectado à porta USB do computador, abrimos a Arduino IDE, e, no menu “Ferramentas”, selecionamos as opções corretas de placa, processador, porta,  _clock_  e programador.

Para rodar o código compilado pela Arduino IDE, o chip precisa do  _bootloader_ adequado. Felizmente, para gravá-lo, basta clicar em “Gravar Bootloader”, ainda no menu “Ferramentas”. Isto também deve ser feito sempre que mudarmos o  _clock_, para registrar no ATtiny85 o novo parâmetro.

![](/assets/image-9.png)  
_Menu “Ferramentas” da Arduino IDE_

Sem uma fonte de  _clock_  externa, o ATtiny85 pode ser configurado para rodar a 1 MHz, 8 MHz ou 16 MHz (as opções que começam com “_Internal_” no menu da Arduino IDE). Com ela (“_External_”), pode-se escolher entre 8 MHz, 16 MHz ou 20 MHz.

**Muita atenção aqui.**  Se configurarmos o microcontrolador para usar uma fonte externa, ele não funcionará e não poderá ser reprogramado sem que ela esteja, de fato, presente. Portanto, cuidado.

Se houver algum erro ao gravar o  _bootloader_, verifique a ligação entre a placa e o microcontrolador — não é difícil se enganar com os fios — e as opções escolhidas. Também é válido tentar desconectar e reconectar o Arduino Leonardo à porta USB, e até reinciar a Arduino IDE.

Agora, se a mensagem “gravação do bootloader concluída” for exibida, o ATtiny85 está pronto para receber o seu programa.

## Pisca-pisca

Lembra do Blink, aquele  _sketch_  que vem pré-carregado e é basicamente o “Hello World” da programação de Arduinos?

Ele pode ser aberto na Arduino IDE, em  `Arquivo  >  Exemplos  >  Basics  >  Blink`, e, conforme sua própria descrição, o que ele faz é ligar um LED por um segundo, depois desligá-lo por mais um, e repetir indefinidamente.

A maioria dos Arduinos tem um LED embutido que pode ser usado com este  _sketch_. O ATtiny85 não.

Portanto, se queremos usar o Blink para comprovar que o microcontrolador já está completamente preparado, vamos precisar de um LED e um resistor de pelo menos 220 Ohm.

Ligamos conforme o esquema e carregamos no ATtiny85 o código a seguir, tudo como você provavelmente já está esperando.

![](/assets/image-10.png)

Sinta-se livre para trocar o Arduino por outra fonte de alimentação

```
/*
  Blink
  Turns on an LED on for one second, then off for one second,
  repeatedly.
  This example code is in the public domain.

  modified 23 Dec 2016
  by Rodrigo Menezes (for Radialle)

  modified 8 May 2014
  by Scott Fitzgerald
  
  modified 2 Sep 2016
  by Arturo Guadalupi
*/

void setup() {
  pinMode(4, OUTPUT);
}
void loop() {
  digitalWrite(4, HIGH);
  delay(1000);
  digitalWrite(4, LOW);
  delay(1000);
}
```

Você poderia mudar o pino, claro. Bastaria ligar o resistor em outro, e alterar o  _sketch_  de acordo. Apenas lembre-se de que a numeração dos pinos no código é diferente da física: o pino ao lado do RESET, por exemplo, é o segundo, e no programa deve ser referido como 3.

Na dúvida, consulte o esquema da seção “Conhecendo o ATtiny85”, ou o  [_datasheet_](http://www.atmel.com/images/atmel-2586-avr-8-bit-microcontroller-attiny25-attiny45-attiny85_datasheet.pdf).

## Conclusão

O Arduino certamente continuará como a sua plataforma de prototipagem favorita, mas agora você já conhece uma alternativa para deixar os seus projetos muito melhores do que antes.

Há muito a ganhar explorando o universo dos microcontroladores, e espero que este texto tenha lhe servido bem como uma introdução a essa jornada.