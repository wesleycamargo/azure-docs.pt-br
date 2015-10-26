<properties
	pageTitle="Introdução a Android no AD do Azure | Microsoft Azure"
	description="Como compilar um aplicativo para Android que se integre ao AD do Azure para entrada e que chame as APIs protegidas do AD do Azure usando o OAuth."
	services="active-directory"
	documentationCenter="android"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="brandwe"/>

# Integrando o AD do Azure em um aplicativo Android

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Se você estiver desenvolvendo um aplicativo de desktop, o AD do Azure torna a autenticação dos usuários com suas contas do Active Directory simples e direta. Ele também habilita seu aplicativo a consumir com segurança qualquer API da Web protegida pelo AD do Azure, como as APIs do Office 365 ou a API do Azure.

Para clientes Android que precisam acessar recursos protegidos, o AD do Azure fornece a biblioteca de autenticação do Active Directory, ou ADAL. A única finalidade da ADAL é tornar mais fácil para seu aplicativo obter tokens de acesso. Para demonstrar como é fácil, vamos compilar aqui um aplicativo de lista de tarefas pendentes para Android que:

-	Obtém tokens de acesso para chamar a API da lista de tarefas pendentes usando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Obtém a lista de tarefas pendentes de um usuário
-	Desconecta usuários.

Para começar, você precisará de um locatário do AD do Azure no qual você possa criar usuários e registrar um aplicativo. Se você ainda não tiver um locatário [saiba como obter um](active-directory-howto-tenant.md).

## Etapa 1: Baixe e execute o servidor de exemplo da API REST TODO do Node. js

Este exemplo é escrito especificamente para funcionar em nosso exemplo existente para criar uma API REST de tarefas de locatário único para o Active Directory do Microsoft Azure. Este é um pré-requisito para o início rápido.

Para obter informações sobre como configurar isso, visite nossos exemplos existentes aqui:

* [Serviço de API REST de exemplo para Node. js do Active Directory do Microsoft Azure](active-directory-devquickstarts-webapi-nodejs.md)

## Etapa 2: Registre sua API da Web com seu locatário do Microsoft Azure AD

**O que estou fazendo?**

*O Microsoft Active Directory dá suporte à adição de dois tipos de aplicativos. APIs da Web que oferecem serviços a usuários e aplicativos (ou na web ou em um aplicativo em execução em um dispositivo) que acessam essas APIs da Web. Nesta etapa, você está registrando a API da Web que está sendo executada localmente para testar este exemplo. Normalmente, essa API Web seria um serviço REST que está oferecendo a funcionalidade que você quer que um aplicativo acesse. O Active Directory do Microsoft Azure pode proteger qualquer ponto de extremidade!*

*Aqui, estamos supondo que você está registrando a API REST TODO mencionada acima, mas isso funciona para qualquer API da Web que você gostaria que o Active Directory do Azure protegesse.*

Etapas para registrar uma API da Web com o AD do Microsoft Azure

1. Entre no [portal de gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique em Active Directory no NAV à esquerda.
3. Clique no locatário do diretório onde você deseja registrar o aplicativo de exemplo.
4. Clique na guia Aplicativos.
5. Na gaveta, clique em Adicionar.
6. Clique em “Adicionar um aplicativo que minha organização está desenvolvendo”.
7. Insira um nome amigável para o aplicativo, por exemplo, "TodoListService", selecione “Aplicativo Web e/ou API da Web” e clique em Avançar.
8. Para a URL de logon, insira a URL base para o exemplo, que é, por padrão, `https://localhost:8080`.
9. Para o URI de ID do aplicativo, insira`https://<your_tenant_name>/TodoListService`, substituindo `<your_tenant_name>` pelo nome do seu locatário do AD do Azure. Clique em OK para concluir o registro.
10. Ainda no portal do Azure, clique na guia Configurar de seu aplicativo.
11. **Encontre o valor da ID do cliente, copie-o e guarde-o**; você precisará dele mais tarde ao configurar seu aplicativo.

## Etapa 3: Registre o aplicativo cliente nativo para Android de exemplo

Registrar seu aplicativo Web é a primeira etapa. Em seguida, você precisará informar o Active Directory do Azure sobre seu aplicativo. Isso permite que o aplicativo se comunique com a API da Web recém-registrada

**O que estou fazendo?**

*Como mencionado acima, o Active Directory do Microsoft Azure dá suporte à adição de dois tipos de aplicativos. APIs da Web que oferecem serviços a usuários e aplicativos (ou na web ou em um aplicativo em execução em um dispositivo) que acessam essas APIs da Web. Nesta etapa, você está registrando o aplicativo neste exemplo. Você deve fazer isso para que este aplicativo seja capaz de solicitar acesso à API da Web que você acabou de registrar. O Active Directory do Azure não vai permitir que o aplicativo solicite a entrada a menos que ele esteja registrado! Isso faz parte da segurança do modelo.*

*Aqui, estamos supondo que você está registrando este aplicativo de exemplo mencionado acima, mas isso funciona para qualquer aplicativo que você está desenvolvendo.*

**Por que eu estou colocando um aplicativo e uma API da Web em um locatário?**

*Como você deve ter adivinhado, você pode compilar um aplicativo que acessa uma API externa que é registrada no Active Directory do Azure de outro locatário. Se você fizer isso, os clientes precisarão concordar com o uso da API no aplicativo. A parte boa é que a biblioteca de autenticação do Active Directory para iOS se encarrega desse consentimento por você! Conforme chegamos aos recursos mais avançados, você verá que essa é uma parte importante do trabalho necessário para acessar o conjunto de APIs da Microsoft no Azure e Office, bem como em qualquer outro provedor de serviço. Por enquanto, como você registrou sua API da Web e seu aplicativo sob o mesmo locatário, você não verá solicitações de permissão. Isso normalmente acontece se você estiver desenvolvendo um aplicativo para uso somente por sua própria empresa.*

1. Entre no [portal de gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique em Active Directory no NAV à esquerda.
3. Clique no locatário do diretório onde você deseja registrar o aplicativo de exemplo.
4. Clique na guia Aplicativos.
5. Na gaveta, clique em Adicionar.
6. Clique em “Adicionar um aplicativo que minha organização está desenvolvendo”.
7. Insira um nome amigável para o aplicativo, por exemplo, "TodoListClient-Android", selecione “Aplicativo cliente nativo” e clique em Avançar.
8. Para o URI de redirecionamento, digite `http://TodoListClient`. Clique em Concluir.
9. Clique na guia Configurar do aplicativo.
10. Encontre o valor da ID do cliente, copie-o e guarde-o; você precisará dele mais tarde ao configurar seu aplicativo.
11. Em “Permissões para outros aplicativos”, clique em “Adicionar aplicativo.” Selecione “Outros” na lista suspensa “Mostrar” e clique na marca de seleção superior. Localize e clique em TodoListService e clique na marca de seleção da parte inferior para adicionar o aplicativo. Selecione “Acessar TodoListService” na lista suspensa “Permissões delegadas” e salve a configuração.



Para compilar com Maven, você pode usar o pom.xml de nível superior

  * Clone esse repositório em um diretório de sua escolha:

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`

  * Siga as etapas em [Seção de pré-requisitos para configurar o Maven para Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)
  * Configure o emulador com o SDK 19
  * Vá para a pasta raiz onde você clonou o repositório
  * Execute o comando: mvn clean install
  * Altere o diretório para o exemplo de início rápido: cd samples\\hello
  * Execute o comando: mvn android:deploy android:run
  * Você deve ver a inicialização do aplicativo
  * Insira as credenciais de usuário de teste para testar!

Pacotes de JAR também serão enviados juntamente com o pacote aar.

### Etapa 4: Baixe a ADAL para Android e adicione-a ao seu espaço de trabalho do Eclipse

Facilitamos para você ter várias opções para usar esta biblioteca em seu projeto Android:

* Você pode usar o código-fonte para importar essa biblioteca no Eclipse e vinculá-la ao seu aplicativo.
* Se estiver usando o Android Studio, você poderá usar o formato de pacote *aar* e fazer referência aos binários.

####Opção 1: zip de origem

Para baixar uma cópia do código-fonte, clique em "Baixar ZIP" no lado direito da página ou [aqui](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

####Opção 2: Origem via Git

Para obter o código-fonte do SDK via git, basta digitar:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####Opção 3: Binários via Gradle

Você pode obter os binários do repositório central do Maven. O pacote AAR pode ser incluído da seguinte forma em seu projeto no AndroidStudio:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

####Opção 4: AAR via Maven

Se você estiver usando o plug-in m2e no Eclipse, você pode especificar a dependência em seu arquivo POM. XML:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####Opção 5: pacote jar dentro da pasta de bibliotecas
Você pode obter o arquivo jar do Maven, o repositório, e colocá-lo na pasta *libs* em seu projeto. Você precisa copiar os recursos necessários para seu projeto também, uma vez que os pacotes jar não os incluem.


### Etapa 5: Adicione referências à ADAL do Android em seu projeto


2. Adicione uma referência ao seu projeto e especifique-a como uma biblioteca Android. Se não souber como fazer isso, [clique aqui para obter mais informações](http://developer.android.com/tools/projects/projects-eclipse.html)

3. Adicione a dependência do projeto para depuração em suas configurações de projeto

4. Atualize o arquivo AndroidManifest.xml do seu projeto para incluir:

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

7. Crie uma instância de AuthenticationContext na atividade principal. Os detalhes dessa chamada estão além do escopo deste arquivo Leia-me, mas você pode obter uma boa introdução consultando o [exemplo de cliente nativo para Android](https://github.com/AzureADSamples/NativeClient-Android). Veja um exemplo:

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * OBSERVAÇÃO: mContext é um campo na sua atividade

8. Copie esse bloco de código para manipular o final de AuthenticationActivity depois que o usuário insere as credenciais e recebe o código de autorização:

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. Para solicitar um token, defina um retorno de chamada

    ```Java
    private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };
    ```
10. Por fim, solicite um token usando esse retorno de chamada:

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

Explicação dos parâmetros:

  * O recurso é necessário e é o recurso que você está tentando acessar.
  * ClientID é obrigatório e é proveniente do Portal do AzureAD.
  * Você pode configurar redirectUri como seu nome de pacote. Não é necessário ser fornecido para a chamada acquireToken.
  * PromptBehavior ajuda a solicitar credenciais para ignorar o cache e o cookie.
  * O retorno de chamada será chamado depois que o código de autorização for trocado por um token.

  O retorno de chamada terá um objeto AuthenticationResult que tem informações de token de acesso, data de vencimento e token de ID.

Opcional: **acquireTokenSilent**

Você pode chamar **acquireTokenSilent** para manipular o armazenamento em cache e a atualização de token. Ele fornece também a versão de sincronização. Ele aceita userid como parâmetro.

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **Agente**: o aplicativo do portal da empresa do Microsoft Intune fornecerá o componente do agente. A ADAL usará a conta de agente, se houver uma conta de usuário criada nesse autenticador e o desenvolvedor escolha não ignorá-la. O desenvolvedor pode ignorar o usuário do agente com:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 O desenvolvedor precisa registrar redirectUri especial para uso do agente. RedirectUri está no formato msauth://packagename/Base64UrlencodedSignature. Você pode obter o redirecturi para seu aplicativo usando o script "brokerRedirectPrint.ps1" ou usar a chamada à API mContext.getBrokerRedirectUri. Assinatura está relacionada aos seus certificados de autenticação.

 O modelo atual do agente é para um usuário. AuthenticationContext fornece um método de API para obter o usuário do agente.

 ```java
 String brokerAccount =  mContext.getBrokerUser();
 ``` O usuário do agente será retornado se a conta for válida.

 O manifesto do seu aplicativo deve ter permissões para usar contas do AccountManager: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET\_ACCOUNTS
 * USE\_CREDENTIALS
 * MANAGE\_ACCOUNTS


Usando este passo a passo, você deve ter o que precisa para se integrar com êxito com o Active Directory do Azure. Para obter mais exemplos de como isso funciona, visite o repositório AzureADSamples / no GitHub.

## Informações importantes

### Personalização

Recursos de projeto da biblioteca podem ser substituídos pelos recursos do seu aplicativo. Isso acontece quando seu aplicativo está sendo criado. Por esse motivo, você pode personalizar o layout de atividade de autenticação da maneira desejada. Você precisa certificar-se de manter a ID dos controles que a ADAL usa (Webview).

### Agente

O componente de agente será entregue com o aplicativo do portal corporativo do Microsoft Intune. A conta será criada no gerenciador de contas. O tipo de conta é "com.microsoft.workaccount". Permite apenas contas únicas do SSO. Ele criará o cookie do SSO para o usuário depois de concluir o desafio de dispositivo para um dos aplicativos.

### URL e ADFS de autoridade

O ADFS não é reconhecido como STS de produção, por isso, você precisa desativar a descoberta de instância e inserir false no construtor de AuthenticationContext.

A URL de autoridade precisa da instância STS e do nome do locatário: https://login.windows.net/yourtenant.onmicrosoft.com

### Consultar itens do cache

A ADAL fornece cache padrão em SharedPrefrecens com algumas funções de consulta simples de cache. Você pode obter o cache atual de AuthenticationContext com: ```Java
 ITokenCacheStore cache = mContext.getCache();
``` Você também pode fornecer sua implementação de cache, se desejar personalizá-la. ```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### PromptBehavior

A ADAL fornece a opção de especificar o comportamento de solicitação. PromptBehavior.Auto exibirá a interface do usuário se o token de atualização for inválido e as credenciais de usuário forem necessárias. PromptBehavior.Always ignorará o uso de cache e sempre mostrará a interface do usuário.

### Solicitação de token silenciosa do cache e atualização

Esse método não usa a exibição da interface do usuário e não exige uma atividade. Ele retorna o token do cache, se disponível. Se o token tiver expirado, ele tenta atualizá-lo. Se o token de atualização tiver expirado ou com falha, ele retornará AuthenticationException.

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

Você também pode fazer uma chamada de sincronização com esse método. Você pode definir null para retorno de chamada ou usar acquireTokenSilentSync.

### Diagnostics

A seguir estão as fontes principais de informações para diagnosticar problemas:

+ Exceções
+ Logs
+ Rastreamentos de rede

Além disso, observe que as IDs de correlação são essenciais para o diagnóstico na biblioteca. Você pode definir suas IDs de correlação com base em cada solicitação se quiser correlacionar uma solicitação da ADAL com outras operações em seu código. Se você não definir uma ID de correlações, a ADAL vai gerar uma aleatório e todas as mensagens de log e chamadas de rede conterão a ID de correlação. A ID gerada automaticamente muda em cada solicitação.

#### Exceções

Obviamente, esse é o primeiro diagnóstico. Tentamos fornecer mensagens de erro úteis. Se você encontrar uma que não é útil, registre um problema e nos informe. Forneça informações do dispositivo, como modelo e SDK#.

#### Logs

Você pode configurar a biblioteca para gerar mensagens de log que você pode usar para ajudar a diagnosticar problemas. Configure o registro de log fazendo a chamada a seguir para configurar um retorno de chamada que a ADAL usará para lidar com cada mensagem de log conforme elas são geradas.


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
 ``` As mensagens podem ser gravadas em um arquivo de log personalizado, conforme mostrado abaixo. Infelizmente, não há um modo padrão de obter os logs de um dispositivo. Há alguns serviços que podem ajudá-lo. Você pode também criar seus próprios métodos, como enviar o arquivo para um servidor.

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### Níveis de registro de log

+ Error(Exceptions)
+ Warn(Warning)
+ Info(Information purposes)
+ Verbose(More details)

Defina o nível de log da seguinte maneira: ```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 Todas as mensagens de log são enviadas para logcat, além de qualquer retorno de chamada de log personalizado. Você pode obter o log para um logcat de formato de arquivo como mostrado abaixo:

 ```
  adb logcat > "C:\logmsg\logfile.txt"
 ``` Mais exemplos de adb cmds: https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat

#### Rastreamentos de rede

Você pode usar várias ferramentas para capturar o tráfego HTTP que a ADAL gera. Isso é mais útil se você estiver familiarizado com o protocolo OAuth ou se você precisar fornecer informações de diagnóstico para a Microsoft ou outros canais de suporte.

Fiddler é a ferramenta de rastreamento de HTTP mais fácil. Use os links a seguir para configurá-la até o tráfego de rede da ADAL de registro correto. Para ser útil, é necessário configurar o Fiddler, ou qualquer outra ferramenta, como Charles, para registrar o tráfego SSL não criptografado. OBSERVAÇÃO: Rastreamentos gerados desta forma podem conter informações altamente privilegiadas como tokens de acesso, nomes de usuário e senhas. Se você estiver usando contas de produção, não compartilhe esses rastreamentos com terceiros. Se precisar fornecer um rastreamento a alguém para obter suporte, reproduza o problema com uma conta temporária com nomes de usuário e senhas que você não se importa de compartilhar.

+ [Configurando o Fiddler para Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [Configurar regras do Fiddler para ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### Modo de caixa de diálogo
O método acquireToken sem atividade dá suporte ao prompt da caixa de diálogo.

### Criptografia

A ADAL criptografa os tokens e os armazena em SharedPreferences por padrão. Você pode examinar a classe StorageHelper para ver os detalhes. O Android introduziu o armazenamento de chaves privadas AndroidKeyStore 4.3(API18). A ADAL o utiliza para API18 e superior. Se você quiser usar a ADAL para versões anteriores do SDK, você precisa fornecer a chave secreta em AuthenticationSettings.INSTANCE.setSecretKey

### Desafio de portador de Oauth2

A classe AuthenticationParameters fornece funcionalidade para obter o authorization\_uri do desafio de portador de Oauth2.

### Cookies de sessão no Webview

O Webview para Android não limpa os cookies de sessão depois que o aplicativo é fechado. Você pode lidar com isso com o código de exemplo abaixo: ```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
``` Mais sobre cookies: http://developer.android.com/reference/android/webkit/CookieSyncManager.html

### Substituições de recurso

A biblioteca ADAL inclui sequências de caracteres em inglês para duas mensagens ProgressDialog a seguir.

Seu aplicativo deve substituí-las se desejar cadeias de caracteres localizadas.

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### Caixa de diálogo NTLM
A ADAL versão 1.1.0 dá suporte à caixa de diálogo NTLM que é processada por meio do evento onReceivedHttpAuthRequest do WebViewClient. O layout da caixa de diálogo e as sequências de caracteres podem ser personalizadas.### Etapa 5: Baixe o código de exemplo do cliente nativo do iOS

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
 

<!---HONumber=Oct15_HO3-->