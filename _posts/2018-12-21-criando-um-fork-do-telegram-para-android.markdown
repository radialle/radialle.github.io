---
layout: post
title: "Criando um fork do Telegram para Android"
author: Rodrigo Laneth (@rlaneth)
date:  2018-12-21 00:00:00 -0300
---

> _Da clonagem do repositório à compilação_

![Criando um fork do Telegram para Android](/assets/1-FS_fV4cMzpm2NxjL0GnF5g.jpg)  
[Foto: NeONBRAND/Unsplash](https://unsplash.com/photos/2c9ffiPlD9Q)

O  **Telegram**, um dos serviços de mensagens instantâneas mais populares da atualidade, se destaca pela  [abertura do código-fonte](https://telegram.org/apps), de  [suas APIs](https://core.telegram.org/api)  e pela existência de uma  [biblioteca oficial para a construção de clientes alternativos](https://telegram.org/blog/tdlib), características que atraem os usuários com conhecimento avançado e o tornam um dos comunicadores mais  _developer-friendly_ disponíveis no mercado.

É fato que ele possui defeitos. Condenações ao  [uso de criptografia ponta-a-ponta somente em  _secret chats_](https://news.ycombinator.com/item?id=16746148),  [questionamentos em relação à segurança do MTProto](https://crypto.stackexchange.com/questions/55108/has-telegram-security-been-significantly-improved-with-mtproto-2-0)  e a  [não divulgação do código-fonte dos servidores](https://www.reddit.com/r/Telegram/comments/4b45ie/when_if_ever_will_telegrams_server_side_source/)  são algumas das polêmicas que ele enfrentou ao longo de sua existência. Umas das críticas mais relevantes é a do renomado especialista em segurança  [The Grugq](https://twitter.com/thegrugq), que, em seu texto “[_Operational Telegram_](https://medium.com/@thegrugq/operational-telegram-cbbaadb9013a)”, de novembro de 2015, conclui:

> _O Telegram é suscetível a erros, utiliza criptografia questionável, vaza um grande volume de informações, rouba os contatos do dispositivo, e agora é conhecido como ponto de encontro para terroristas._

Mas apesar das controvérsias, o Telegram permanece popular e, por isso, é um interessante objeto de estudo. Então, decidi recentemente iniciar um  _fork_  para aprender com o código-fonte dos clientes oficiais.

### Iniciando

Antes de decidir qual aplicação do Telegram usaria como base para o desenvolvimento, a minha única preferência era pelas versões  _mobile_. Mesmo assim, tive algumas frustrações devido à inconsistência na manutenção e na divulgação do código-fonte dos clientes.

Em janeiro de 2018, o Telegram  [anunciou um novo cliente oficial](https://telegram.org/blog/telegram-x)  para Android e iOS, o  **Telegram X**, que seria mantido paralelamente à alternativa clássica nas lojas de aplicativos das respectivas plataformas. Meses depois, em setembro, anunciou-se a  [substituição da versão clássica pela X no iOS](https://techcrunch.com/2018/09/19/telegram-to-replace-ios-messaging-app-with-telegram-x-swift-rebuild/)  — muito embora, no momento da publicação deste texto, o app chamado  [Telegram X permaneça disponível na App Store](https://itunes.apple.com/us/app/telegram-x/id898228810)  ao lado do  [Telegram Messenger](https://itunes.apple.com/us/app/telegram-messenger/id686449807).

Há pouco tempo atrás, o código-fonte referenciado no site oficial  [estava bastante defasado](https://github.com/peter-iakovlev/Telegram). Hoje, ele aponta para um  [novo repositório no GitHub](https://github.com/peter-iakovlev/Telegram-iOS), aparentemente contendo uma versão mais atual — que  **pode**  ser baseado no Telegram X. Já o código-fonte do Telegram X para Android aparentemente nunca foi publicado.

No caso do  [Telegram clássico para Android](https://play.google.com/store/apps/details?id=org.telegram.messenger), a situação é menos confusa, com o código-fonte  [encontrado no GitHub](https://github.com/DrKLO/Telegram)  aparentando equivaler ao aplicativo na Play Store. Enfim, optei por utilizá-lo para o desenvolvimento do  _fork_.

### Configuração do ambiente

As instruções a seguir descrevem a configuração de um ambiente baseado no  **Fedora 29**  para compilação do Telegram para Android conforme presente em seu repositório do GitHub durante a elaboração deste texto (no momento em que escrevo, o  _commit_  mais recente é o  [e222fde](https://github.com/DrKLO/Telegram/commit/e222fded6cca5ace3649be6f18b55f526311bc79), de 7 de novembro de 2018).

Embora baseadas no Fedora, as instruções podem ser interpretadas sem grandes dificuldades para aplicação em outras distribuições Linux ou mesmo em sistemas operacionais completamente distintos.

Como a intenção desta seção é orientar sobre a compilação do aplicativo em sua forma original, evitando alterações ao projeto publicado, precisei basear-me em versões antigas de algumas ferramentas requeridas. São elas:

-   **Android NDK**  (atual: r18b; utilizada: r16b);
-   **Gradle**  (atual: 4.6; utilizada: 4.4);
-   **Plugin Android Gradle**  (atual: 3.2.1; utilizada: 3.1.4)

Os problemas encontrados em relação às versões mais recentes de cada um dos itens listados estão descritos na próxima seção deste texto.

1.  **Instalação do Android Studio**  
    Basta efetuar o  [download](https://developer.android.com/studio/install#linux)  e prosseguir com a instalação de acordo com as  [instruções oficiais](https://developer.android.com/studio/install#linux), que se resumem a extrair o arquivo  `.zip`  para o local desejado e executar o script  `studio.sh`  na pasta  `android-studio/bin`.  

2.  **Instalação do Android NDK r16b**  
    O  [NDK (_Native Development Kit_)](https://developer.android.com/ndk/)  é um conjunto de ferramentas para implementação de partes de um aplicativo Android em código nativo, por meio de linguagens como C e C++. É necessário para compilar bibliotecas incluídas no projeto do Telegram.  

3.  **Clonagem do repositório do Telegram para Android**  
    Exemplo para linha de comando:  `git clone --recurse-submodules  [https://github.com/DrKLO/Telegram.git](https://github.com/DrKLO/Telegram.git)`  . Destaque para o parâmetro  `--recurse-submodules`, necessário para que o download do código da biblioteca  [libgtvoip](https://github.com/DrKLO/Telegram/blob/6a1cf64f6f45ba0d0e7ff898e8a6c677ddc3ef33/.gitmodules), incluída no repositório como  [submódulo](https://blog.github.com/2016-02-01-working-with-submodules/), seja também realizado no processo.

4.  **Configuração do Android Studio**  
    Após abrir o projeto do Telegram para Android, é preciso configurar o caminho para o Android NDK através da janela  _Project Structure_, que pode ser acessada por meio do menu  _File_.  

5.  **Modificação do** [**BuildVars.java**](https://github.com/DrKLO/Telegram/blob/e222fded6cca5ace3649be6f18b55f526311bc79/TMessagesProj/src/main/java/org/telegram/messenger/BuildVars.java)  
    É necessário gerar e adicionar ao arquivo as suas próprias chaves de API para o  [Telegram](https://core.telegram.org/api/obtaining_api_id)  e  [HockeyApp](https://www.hockeyapp.net/), conforme  [avisado no README](https://github.com/DrKLO/Telegram/blob/d073b80063c568f31d81cc88c927b47c01a1dbf4/README.md)  do repositório. Também é interessante observar as outras variáveis presentes e modificá-las conforme desejado.  

6.  **Adição do** [**google-services.json**](https://developers.google.com/android/guides/google-services-plugin)  
    O  `google-services.json`  é um arquivo de configuração necessário para habilitar o uso de APIs da Google utilizadas pelo Telegram. O processo de geração e adição do arquivo a um projeto do Android Studio é  [descrito na página de suporte](https://support.google.com/firebase/answer/7015592)  da Google.  

7.  **Gerar chave para assinatura**  
    Por fim, é necessário gerar chaves para assinar o aplicativo. Uma maneira de fazê-lo de dentro do próprio Android Studio é através da opção  _Build > Generate Signed Bundle / APK… > APK_ (selecionar)  _> Next > Create new…_. Alternativamente, a ferramenta  [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)  do Java pode ser usada. A  _keystore_  deve ser salva no diretório  `TMessagesProj/config`  com o nome  _release.keystore_, e o  _alias_  da chave gerada, bem como a senha da  _keystore_  e a senha da chave, devem ser adicionadas nas propriedades do projeto (arquivo  _gradle.properties_).

### Problemas com versões recentes das ferramentas utilizadas

No momento em que escrevo, existem duas versões estáveis mais recentes do Android NDK disponíveis: a r17c e a r18b.

O projeto do Telegram para Android tem como requerimento mínimo a API nível 16, associada ao Android 4.1 Jelly Bean. No entanto, bibliotecas usadas por ele utilizam a função  `posix_memalign`, que, a partir do NDK r17c, passou a estar disponível somente para os níveis de API superiores à 17, do Android 4.2 Jelly Bean MR1.

A justificativa para a mudança, de acordo com  [este issue no repositório do NDK no GitHub](https://github.com/android-ndk/ndk/issues/647), é que a  `posix_memalign`  não havia sido implementada em alguns dispositivos com o Android 4.1 Jelly Bean.

Por sua vez, o NDK r18b abandonou o suporte ao compilador GCC e agora força o uso do Clang, que não compila as bibliotecas do projeto.

É possível utilizar o NDK r17c alterando o requerimento mínimo do projeto para a API nível 17. O uso do NDK r18, no entanto, exigiria maiores alterações. Há um  [pull request aberto](https://github.com/DrKLO/Telegram/pull/1483), não avaliado nem testado por mim, que promete aplicar as mudanças apropriadas.

Em relação ao Gradle, a versão 4.6 provoca conflito com a opção depreciada  `android.enableD8`, que desativa o  [D8](https://android-developers.googleblog.com/2017/08/next-generation-dex-compiler-now-in.html)  e está  [presente no arquivo  _gradle.properties_](https://github.com/DrKLO/Telegram/blob/e222fded6cca5ace3649be6f18b55f526311bc79/gradle.properties#L20), e o  [_desugaring_](https://android-developers.googleblog.com/2017/04/java-8-language-features-support-update.html), uma etapa extra de otimização do bytecode que tornou-se padrão. Ocorrem problemas também com o valor da  `buildToolsVersion`  [declarado no arquivo  _build.gradle_  do TMessagesProj](https://github.com/DrKLO/Telegram/blob/e222fded6cca5ace3649be6f18b55f526311bc79/TMessagesProj/build.gradle#L36).

Possíveis soluções são desativar o  _desugaring_  adicionando  `android.enableD8.desugaring=false`  ao  _build.gradle_  e elevar o valor da  `buildToolsVersion`  de  `28.0.2`  para  `28.0.3`.

No mais, cabe mencionar que o  _plugin_  Android Gradle não pode ser atualizado para a versão 3.1.4 sem que o Gradle seja também atualizado para a 4.6.

### Conclusão

Uma vez que o aplicativo tenha sido corretamente compilado, começa-se a pensar na aplicação das modificações desejadas. Particularmente, penso que atualizar o projeto para funcionar com versões mais recentes do Android NDK e do Gradle seriam excelentes primeiros passos.

![](/assets/image-19.png)

Telegram com  **AlertDialog**  adicionado à  [**IntroActivity**](https://github.com/DrKLO/Telegram/blob/e222fded6cca5ace3649be6f18b55f526311bc79/TMessagesProj/src/main/java/org/telegram/ui/IntroActivity.java)

Por fim, destaco o  [Telegram FOSS](https://github.com/Telegram-FOSS-Team/Telegram-FOSS). Trata-se de um  _fork_  não oficial do Telegram para Android cuja principal promessa é substituir ou remover elementos “não-[FOSS](https://en.wikipedia.org/wiki/Free_and_open-source_software), pouco confiáveis ou suspeitos” do mensageiro. Dentre as modificações efetuadas, estão a troca do Google Cloud Messaging pelo serviço de  _push_  do Telegram e a remoção do HockeyApp.

Não somente esta versão pode ser um melhor ponto de partida para a criação de um  _fork_  do Telegram — a depender, é claro, de suas preferências e objetivos — , o trabalho já realizado pelos seus desenvolvedores podem servir de auxílio mesmo para quem preferir basear-se na versão oficial.