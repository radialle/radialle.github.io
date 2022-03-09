---
layout: post
title:  "Os mobiles já estão mais rápidos que os PCs?"
author: Davidson Francis (@theldus)
date:   2019-04-07 00:00:00 -0300
---

> _A evolução do poder de processamento dos dispositivos móveis cresce a cada dia, mas será que eles já ultrapassam os computadores domésticos?_

![](/assets/1-kl8Vpj5-QUrzAVbzvSih3Q.jpg)  

Atualmente, a demanda por dispositivos de alto desempenho está cada vez maior, e, aliado a isso, há também um grande foco em reduzir cada vez
mais o consumo de energia e temperatura. Com isso, tornou-se possível o desenvolvimento de pequenos ‘monstrinhos’, como o Parallella e o Jetson
Nano, que, embora de baixo custo, conseguem competir facilmente com Desktops tradicionais.

![image](/assets/image-20.png)
_Parallella (99$) à esquerda e Jetson Nano (também a 99$) à direita, ambos consomem apenas 5 W._

Com os smartphones e tablets não é diferente, dificilmente alguém em 2005 afirmaria que em 2019 teríamos dispositivos com 4 GB de RAM, 64 GB de
memória interna e 8 núcleos de CPU, e aqui estamos.

Mas a pergunta inicial continua: um celular atual consegue se equiparar a uma CPU atual? Vamos aos testes.

### Metodologia

Foi decidido utilizar o algoritmo clássico de multiplicação de matrizes, uma vez que é um algoritmo sem dependência de dados e que escala bem
à medida que se aumenta o número núcleos.

O presente artigo não tem a intenção de descrever como foi preparado o ambiente no hardware utilizado, nem de descrever os algoritmos, uma vez
que estes são facilmente encontrados na internet.

#### Hardware utilizado:

**Mobile**: LG K11+:
- MediaTek MT6750 com: 1 cluster ARM-Cortex A53 @ 1.5GHz + 1 cluster Cortex-A53 @ 1.0GHz
- Mali T860 MP2: 2 clusters a 700 MHz c/ 16 shader cores.

**Desktop**: Intel Core i7 2600 @ 3.4GHz, 4 cores com Hyper-Threading.

#### Software utilizado:

**Mobile**: clang 7.0.1 e NDK r19c.

**Desktop**: gcc 5.3.0

*Importante observar que nenhuma flag de otimização foi utilizada durante a compilação dos testes.

### Resultados

Os testes a seguir foram todos executados com matrizes de tamanho 512x512, com o MediaTek MT6750 a utilizar todos os cores disponíveis e o Core
i7 com 1 e 4 cores. Para os testes de CPU, foi utilizado o OpenMP e de GPU o OpenCL 1.2.

Os tempos foram aferidos utilizando a rotina omp_get_wtime() (código para CPU, em OpenMP) e o profilling que o próprio OpenCL fornece (para as
versões em GPU). Estão em segundos e para cada teste foi executado 10 vezes e obtido a média.

![image](/assets/image-21.png)  
_Figura 1a: MT6750 vs i7 2600_

![image](/assets/image-22.png)  
_Figura 1b: MT6750 vs i7 2600, Single vs Multicore_

Nas Figura 1a e 1b, fica claro a diferença entre as CPUs, mesmo que contra apenas um único core do i7 2600. Na Figura 2, é apresentado o Speedup,
onde pode-se perceber facilmente que o Core i7 foi cerca de 18x mais rápido, quando utilizado apenas 1 core, e cerca de 48x mais rápido quando
utilizado todos os seus 4 cores.

![image](/assets/image-23.png)  
_Figura 2: Speedup MT6750 e i7 2600_

Isso significa que os celulares estão muito longe dos desktops? não exatamente, ainda nos resta a GPU.

É de conhecimento geral que uma GPU facilmente supera uma CPU para certos tipos de aplicação, portanto, antes de prosseguir com os testes, vamos
ver o quanto a Mali T860MP2 consegue ser superior que o MediaTek MT6750:

![image](/assets/image-24.png)  
_Figura 3: Speedup MT6750 e Mali T860 MP2_

Na Figura 3 a Mali chega a ser 117x superior ao MediaTek em Single Core!, e cerca de 40x no Multi Core, portanto, os testes a seguir irão utilizar
a GPU como referência.

![image](/assets/image-25.png)  
_Figura 4: GPU vs CPU_

Pode-se observar uma grata surpresa na figura acima: para um único core do i7 2600, a GPU conseguiu um tempo de execução menor que o da CPU, e,
para todos os 4 cores do i7, o Desktop se saiu levemente na frente comparado a GPU.

O que nos leva aos seguintes resultados finais:

![image](/assets/image-26.png)  
_Figura 5: Speedup GPU e CPU_

A Mali foi cerca de 113% superior ao i7 quando utilizado apenas um único core! e cerca de 21% mais lento quando o Desktop utilizou todos os cores.

### Conclusão

É importante ressaltar que apenas um único teste utilizando um kernelsimples como esse não pode ser encarado como a palavra final para o assunto em
questão. Existe a possibilidade de otimizar o benchmark para beneficiar de todos os recursos que tanto a GPU quanto a CPU fornecem, bem como testar
outros tipos de cargas de trabalho, com outros tamanhos e etc.

Contudo, é válido notar que, embora uma CPU mid/low-end como o MediaTek MT6750 tenha ficado muito atrás do desktop (no teste CPU vs CPU), a GPU do
mesmo SoC conseguiu se aproximar bastante e pode-se dizer que possui desempenho equiparável para certos tipos de aplicação, como no processamento
de imagens por exemplo.

Também é válido acrescentar que muitos apps não utilizam de todos os cores e muito menos da GPU que o dispositivo possui, restringindo a apenas um
único core, o que passa muito longe do desempenho de um desktop (vide Figuras 1a e 1b).

De toda forma, é esperado que os resultados sejam bem diferentes ao comparar dispositivos high-end como o Snapdragon 855/Adreno 640. Será que o
futuro da computação será dominado por dispositivos ARM? talvez RISC-V? ou a Intel/AMD pensará em um jeito de reverter a situação?

Cenas dos próximos capítulos...