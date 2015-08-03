<properties
	pageTitle="Introdução a Cordova no AD do Azure | Microsoft Azure"
	description="Como compilar um aplicativo do Cordova que se integra ao AD do Azure para entrada e que chama APIs protegidas do AD do Azure usando OAuth."
	services="active-directory"
	documentationCenter=""
	authors="vibronet"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="07/17/2015"
	ms.author="vittorib"/>

# Integrar o AD do Azure com um aplicativo Apache Cordova

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

O Apache Cordova possibilita desenvolver aplicativos HTML5/JavaScript que podem ser executados em dispositivos móveis, como aplicativos nativos completos. Com o AD do Azure, você pode adicionar recursos de autenticação de nível empresarial a seus aplicativos Cordova. Graças a um plug-in Cordova que encapsula SDKs nativos do AD do Azure no iOS, Android, Windows Store e Windows Phone, você pode aprimorar seu aplicativo para dar suporte ao logon na contas do AD de seus usuários, obter acesso ao Office 365 e à API do Azure e até mesmo proteger chamadas para sua própria API da Web personalizada.

Neste tutorial, usaremos o plug-in do Apache Cordova a ADAL (Biblioteca de Autenticação do Active Directory) para aprimorar um aplicativo simples com os seguintes recursos:

-	Com apenas algumas linhas de código, autentique um usuário do AD e obtenha um token para chamar a Graph API do AD do Azure.
-	Usar esse token para invocar a Graph API para consultar o diretório e exibir os resultados  
-	Use o cache de token da ADAL para minimizar os prompts de autenticação para o usuário.

Para isso, você precisará:

2. Registrar um aplicativo com o AD do Azure
2. Adicionar código ao seu aplicativo para solicitar tokens
3. Adicionar código para usar o token para consultar a Graph API e exibir os resultados.
4. Criar o projeto de implantação Cordova com todas as plataformas de destino e o plug-in Cordova ADAL e testar a solução em emuladores.

## *0. Pré-requisitos*

Para concluir este tutorial, você precisará de:

- Um locatário do AD do Azure onde você pode ter uma conta com direitos de desenvolvimento de aplicativo
- Um ambiente de desenvolvimento configurado para usar Apache Cordova  

Se você já tiver ambos configurados, vá diretamente para a etapa 1.

Se você não tiver um locatário do AD do Azure, você pode encontrar [instruções sobre como obter um aqui](active-directory-howto-tenant.md).

Se você não tiver o Apache Cordova configurado no seu computador, instale o seguinte:

- [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [Cordova CLI](https://cordova.apache.org/) (pode ser facilmente instalado por meio do gerenciador de pacotes NPM: `npm install -g cordova`)

Observe que eles funcionam no PC e no Mac.

Cada plataforma de destino tem pré-requisitos diferentes.

- Para compilar e executar a versão do aplicativo para Tablet/PC Windows ou para celular
	- [Visual Studio 2013 para Windows com atualização 2 ou posterior](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Versão Express ou outra).
- Para compilar e executar para iOS
	-   Xcode 5. x ou superior. Baixe-o em http://developer.apple.com/downloads ou na [App Store da Mac](http://itunes.apple.com/us/app/xcode/id497799835?mt=12)
	-   [ios-sim](https://www.npmjs.org/package/ios-sim) – permite que você inicie aplicativos iOS no Simulador iOS por meio da linha de comando (pode ser facilmente instalado por meio do terminal: `npm install -g ios-sim`)

- Para compilar e executar o aplicativo para Android
	- Instale [Java Development Kit (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou superior. Certifique-se de que `JAVA_HOME` (variável de ambiente) está configurado corretamente de acordo com o caminho de instalação do JDK (por exemplo C:\\Program Files\\Java\\jdk1.7.0_75).
	- Instale o [SDK do Android](http://developer.android.com/sdk/installing/index.html?pkg=tools) e adicione o local `<android-sdk-location>\tools` (por exemplo, C:\\tools\\Android\\android-sdk\\tools) em sua variável de ambiente `PATH`.
	- Abra o Gerenciador de SDK do Android (por exemplo, por meio do terminal: `android`) e instale
    - *Android 5.0.1 (API 21)* SDK de plataforma
    - *Android SDK Build-tools* versão 19.1.0 ou superior
    - *Repositório de suporte do Android* (Extras)

  O SDK do Android não fornece qualquer instância do emulador padrão. Crie uma nova executando `android avd` no terminal e, em seguida, selecionando *Criar...* se você quiser executar o aplicativo Android no emulador. O *nível de API* recomendado é 19 ou superior, consulte [Gerenciador AVD](http://developer.android.com/tools/help/avd-manager.html) para obter mais informações sobre o emulador do Android e as opções de criação.


## *1. Registrar um aplicativo com o AD do Azure*

Observação: Esta etapa é opcional. O tutorial forneceu valores previamente provisionados que permitirão que você veja o exemplo em ação sem fazer nenhum provisionamento em seu próprio locatário. No entanto, é recomendável que você execute esta etapa e se familiarize com o processo, pois ele será necessário quando você criar seus próprios aplicativos.

O AD do Azure só emitirá tokens para aplicativos conhecidos. Antes de poder usar o AD do Azure do seu aplicativo, você precisa criar uma entrada para ele no seu locatário. Para registrar um novo aplicativo em seu locatário:

- Entre no Portal de Gerenciamento do Azure
- Clique em Active Directory no painel de navegação à esquerda.
- Selecione o locatário em que você deseja registrar o aplicativo
- Clique na guia Aplicativos e clique em Adicionar na lista de botões.
- Siga os prompts e crie um novo “Aplicativo cliente nativo”
    - O nome do aplicativo descreverá seu aplicativo para os usuários finais
    -	O "URI de redirecionamento" é o URI usado para retornar os tokens para seu aplicativo. Digite `http://MyDirectorySearcherApp`.

Depois de concluir o registro, o AAD atribuirá a seu aplicativo um identificador de cliente único. Você precisará desse valor nas próximas seções: você pode encontrá-lo na guia Configurar do aplicativo recém-criado.

## *2. Clone os repositórios necessários para o tutorial*

No shell ou na linha de comando, digite o seguinte comando:

    git clone https://github.com/AzureAD/azure-activedirectory-library-for-cordova.git
    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## *3. Crie o aplicativo Cordova*

Há várias maneiras de criar aplicativos Cordova. Neste tutorial, usaremos a CLI (Interface de Linha de Comando) do Cordova. No shell ou na linha de comando, digite o seguinte comando:


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

Isso criará a estrutura de pastas e scaffolding do projeto Cordova, copiando o conteúdo do projeto inicial na subpasta www. Siga para a nova pasta DirSearchClient.

    cd .\DirSearchClient

Adicione o plug-in de lista de permissões, necessário para invocar a Graph API.

     cordova plugin add https://github.com/apache/cordova-plugin-whitelist.git

Em seguida, adicione todas as plataformas às quais deseja dar suporte. Para obter um exemplo em funcionamento, você precisará executar pelo menos um dos comandos a seguir. Observe que você não poderá emular iOS no Windows ou Windows/Windows Phone em um Mac.

    cordova platform add android@97718a0a25ec50fedf7b023ae63bfcffbcfafb4b
    cordova platform add ios
    cordova platform add windows

Por fim, você pode adicionar a ADAL para o plug-in Cordova ao seu projeto.

    cordova plugin add ../azure-activedirectory-library-for-cordova

## *3. Adicionar código para autenticar usuários e obter tokens do AAD*

O aplicativo que você está desenvolvendo neste tutorial fornecerá um recurso de pesquisa de diretório de funções básicas, em que o usuário final pode digitar o alias de qualquer usuário no diretório e visualizar alguns atributos básicos. O projeto inicial contém a definição da interface do usuário básico do aplicativo (em www/index.html) e o scaffolding que conecta os ciclos de eventos do aplicativo básico, ligações de interface do usuário e a lógica de exibição dos resultados (em www/js/index.js). A única coisa que falta para você fazer é adicionar a lógica, implementando as tarefas de identidade.

A primeira coisa que você precisa fazer é introduzir em seu código os valores de protocolo usados pelo AAD para identificar seu aplicativo e os recursos de destino. Esses valores serão usados para construir as solicitações de token posteriormente. Inserir o trecho a seguir na parte superior do arquivo index.js.

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

Os valores `redirectUri` e `clientId` devem corresponder aos valores que descrevem seu aplicativo no AAD. Você pode encontrá-los na guia Configurar no portal do Azure, conforme descrito na etapa 1, mostrada anteriormente neste tutorial. Observação: se você tiver optado por não registrar um novo aplicativo em seu próprio locatário, você pode simplesmente colar os valores previamente configurados acima como estão. Isso permitirá que você consulte o exemplo em execução, embora deva sempre criar sua própria entrada para seus aplicativos destinados a produção.

Em seguida, precisamos adicionar o código real da solicitação de token. Inserir o trecho a seguir entre as definições `search ` e `renderdata `.

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user cridentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Vamos examinar essa função, separando-a em suas duas partes principais. Esse exemplo foi criado para funcionar com qualquer locatário, em vez de estar vinculado a um locatário determinado. Ele usa o ponto de extremidade "/common", que permite que o usuário insira qualquer conta no momento da autenticação e direciona a solicitação para o locatário a que ele pertence. Essa primeira parte do método inspeciona o cache da ADAL para ver se já existe um token armazenado e, se houver, ela usa os locatários originários para reinicializar a ADAL. Isso é necessário para evitar prompts extras, uma vez que o uso de "/common" sempre resulta em solicitar que o usuário insira uma nova conta. ```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
``` A segunda parte do método executa a solicitação tokewn adequada. O método `acquireTokenSilentAsync` solicita que a ADAL retorne um token para o recurso especificado sem mostrar qualquer experiência do usuário. Isso pode acontecer se o cache já tiver um token de acesso adequado armazenado, ou se houver um token de atualização que pode ser usado para obter um novo token de acesso sem mostrar nenhum prompt. Se essa tentativa falhar, voltamos para `acquireTokenAsync`, que solicitará visivelmente que o usuário seja autenticado. ```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user cridentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
``` Agora que temos o token, podemos finalmente invocar a Graph API e executar a consulta de pesquisa que queremos. Insira o trecho a seguir logo abaixo da definição `authenticate`.

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Os arquivos de ponto de partida forneceram uma experiência do usuário de funções básicas para digitar o alias de um usuário em uma caixa de texto. Esse método usa esse valor para construir uma consulta, combiná-la ao token de acesso, enviá-la à Graph e analisar os resultados. O método renderData, já está presente no arquivo de ponto de partida, se encarrega de visualizar os resultados.

## *4. Execute*
Seu aplicativo está finalmente pronto para execução! Operá-lo é muito simples: quando o aplicativo for iniciado, digite na caixa de texto o alias do usuário que você deseja pesquisar e clique no botão. Será solicitada a sua autenticação. Após a autenticação e pesquisa bem-sucedidas, os atributos do usuário pesquisado serão exibidos. Execuções subsequentes executarão a pesquisa sem mostrar qualquer prompt, devido à presença em cache do token adquirido anteriormente. As etapas concretas para execução do aplicativo variam de acordo com a plataforma.


##### Para compilar e executar a versão do aplicativo para Tablet/PC Windows

   `cordova run windows`

   __Observação__: durante a primeira execução, você poderá ser solicitado a inscrever-se para uma licença de desenvolvedor. Consulte [licença de desenvolvedor](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) para obter mais detalhes.


##### Para compilar e executar o aplicativo no Windows Phone 8.1

   Para executar no dispositivo conectado: `cordova run windows --device -- --phone`

   Para executar no emulador padrão: `cordova emulate windows -- --phone`

   Use `cordova run windows --list -- --phone` para ver todos os destinos disponíveis e `cordova run windows --target=<target_name> -- --phone` para executar o aplicativo em um emulador ou dispositivo específico (por exemplo, `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).
##### Para compilar e executar no dispositivo Android

   Para executar no dispositivo conectado: `cordova run android --device`

   Para executar no emulador padrão: `cordova emulate android`

   __Observação__: Verifique se você criou a instância do emulador usando o *Gerenciador AVD* conforme mostrado na seção *Pré-requisitos*.

   Use `cordova run android --list` para ver todos os destinos disponíveis e `cordova run android --target=<target_name>` para executar o aplicativo em um emulador ou dispositivo específico (por exemplo, `cordova run android --target="Nexus4_emulator"`).

##### Para compilar e executar no dispositivo iOS

   Para executar no dispositivo conectado: `cordova run ios --device`

   Para executar no emulador padrão: `cordova emulate ios`

   __Observação__: Verifique se você tem o pacote `ios-sim` instalado para ser executado no emulador. Confira a seção *Pré-requisitos* para saber mais.

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

Use `cordova run --help` para ver a compilação adicional e opções de execução.

Para referência, o exemplo concluído (sem seus valores de configuração) é fornecido aqui. Agora você pode passar para cenários mais avançados (tudo bem, e mais interessantes também). Você pode desejar experimentar:

[Proteger uma API da Web Node.js com o AD do Azure >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
 

<!---HONumber=July15_HO4-->