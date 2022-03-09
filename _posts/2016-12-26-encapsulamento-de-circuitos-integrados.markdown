---
layout: post
title: "Encapsulamento de circuitos integrados"
author: Rodrigo Laneth (@rlaneth)
date:  2016-12-26 00:00:00 -0300
---

> _Entenda e descubra o tipo mais adequado para placas a serem montadas manualmente_

 
![Encapsulamento de Circuitos Integrados](/assets/1-ZO0hn0pdXfMNzKeqrhG4cw.jpg)  
[Foto: Stanmar/Wikimedia Commons](https://commons.wikimedia.org/wiki/File:Integrated_circuit_on_microchip.jpg)

Escrevendo sobre a transição  [do Arduino ao AVR](https://radialle.com/do-arduino-ao-avr-e24a303e5b5e), percebi um tema que merecia ser abordado separadamente: a explicação sobre encapsulamento (em inglês,  _package_) de circuitos integrados.

Vamos relembrar. Um  **circuito integrado**  (CI) ou  _integrated circuit_  (IC) é o que se conhece popularmente como chip. Um conjunto de componentes eletrônicos interligados em uma única peça.

Boa parte deles se parece com o da foto que ilustra este texto: uma pequena caixa preta com “perninhas”. Essa proteção externa do circuito, junto com esses conectores, são o que chamamos de  **encapsulamento**.

Existem muitos tipos de encapsulamento, padronizados e proprietários. Mas se você faz a montagem das suas placas à mão, na grande maioria dos casos, sua preferência será pelo DIP (_Dual In-line Package_).

Ele é desenhado para  **encaixe em furos**  (_through-hole_), e é conveniente não só por facilitar a soldagem manual, mas também por possibilitar que o componente seja colocado diretamente em uma  _protoboard_.

Como exemplo, usemos o microcontrolador ATtiny85. Na sua  [página oficial](http://www.atmel.com/devices/attiny85.aspx), há uma lista com os modelos do produto e seus tipos de encapsulamento.

Quando comprei alguns, optei pelo modelo  **20PU**, cujo encapsulamento é listado como  **PDIP 8P3 8**.

O “P” antes do “DIP” vem de “_Plastic_”, indicando que a parte externa do componente é de plástico. Do resto do nome, as outras informações que podemos extrair é que o chip tem oito pinos e 0,3" (polegadas) de largura.

Em qualquer caso, é DIP.

![](/assets/image-11.png)  
_ATtiny85–20PU_

Mas também não é difícil encontrar o mesmo microcontrolador no modelo 20SU, de encapsulamento SOIC (_Small Outline Integrated Circuit_). Este é feito para a montagem  **sobre a superfície**  de uma placa. Os pinos e o espaçamento entre eles são menores.

![](/assets/image-12.png)  
_ATtiny85–20SU (via  [RS Semiconductors](http://int.rsdelivers.com/product/atmel/attiny85-20su/atmel-attiny85-20su-8bit-avr-microcontroller-20mhz-8-kb-512-b-flash-8-pin-soic/6962639))_

Não se pode dizer que é absolutamente impossível soldar essa versão à mão, mas é bastante complicado. Para encaixar na  _protoboard_, a solução é usar um  [adaptador](https://www.sparkfun.com/products/13655)  ou fazer uma  [gambiarra](http://www.instructables.com/id/Deadbug-an-SMD-ATTiny85/).

No mais, se você quiser conhecer outros tipos de encapsulamento existentes,  [essa lista](https://en.wikipedia.org/wiki/List_of_integrated_circuit_packaging_types)  na Wikipédia em inglês é um bom começo.