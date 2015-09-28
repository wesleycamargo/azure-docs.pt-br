<properties pageTitle="Visualização do B2C do AD do Azure: chamando uma API Web de um aplicativo do Android | Microsoft Azure" description="Este artigo mostrará como criar um aplicativo de "Lista de Tarefas Pendentes" do Android que chama uma API Web do node.js usando tokens de portador OAuth 2.0. O aplicativo do Android e a api Web usam o B2C do AD do Azure para gerenciar identidades de usuário e autenticar usuários." services="active-directory-b2c" documentationCenter="android" authors="brandwe" manager="msmbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="brandwe"/>

# Visualização do B2C do AD do Azure: chamando uma API Web de um aplicativo do Android

Com o AD B2C do Azure, você pode adicionar recursos poderosos de gerenciamento de identidades de autoatendimento para seus aplicativos do Android e apis Web em poucas etapas. Este artigo mostra como criar um aplicativo de "Lista de Tarefas Pendentes" do Android que chama uma API Web do node.js usando tokens de portador OAuth 2.0. Tanto o aplicativo do Android quanto a api Web usam o AD B2C do Azure para gerenciar identidades de usuário e autenticar usuários.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
> [AZURE.NOTE]
	Este guia de início rápido tem o pré-requisito de que você deve ter uma API Web protegida pelo AD do Azure com B2C para funcionar totalmente. Criamos uma para .Net e node.js que você pode usar. 
	Este passo a passo pressupõe que o exemplo de API Web do node.js está configurado. Consulte o [Tutorial da API Web do B2C do AD do Azure para Node.js](active-directory-b2c-devquickstarts-api-node.md).

> [AZURE.NOTE]
	Este artigo não aborda como implementar conexão, registro e gerenciamento de perfil com o Azure AD B2C. Ele se concentra na chamada a APIs Web depois que o usuário já está autenticado. 
    Se não estiver, você deve começar com o [tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para saber mais sobre os conceitos básicos do Azure AD B2C.

Para clientes Android que precisam acessar recursos protegidos, o AD do Azure fornece a biblioteca de autenticação do Active Directory, ou ADAL. A única finalidade da ADAL é tornar mais fácil para seu aplicativo obter tokens de acesso. Para demonstrar como é fácil, vamos compilar aqui um aplicativo de lista de tarefas pendentes para Android que:

-	Obtém tokens de acesso para chamar a API da lista de tarefas pendentes usando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Obtém a lista de tarefas pendentes de um usuário
-	Faz logout dos usuários.



### Etapa 1: obter um diretório AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e assim por diante. Se você não tiver um, acesse [criar um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir.

### Etapa 2: criar um aplicativo

Agora você precisa criar um aplicativo no diretório B2C, que dá ao AD do Azure algumas informações que ele precisa para se comunicar de forma segura com seu aplicativo. O aplicativo e a API Web serão representados por uma única **ID de aplicativo** nesse caso, uma vez que incluem um aplicativo lógico. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). Certifique-se de

- Incluir um **aplicativo Web/api Web** no aplicativo
- Digitar `http://localhost:3000/auth/openid/return` como uma **URL de Resposta** - é a URL padrão para este exemplo de código.
- Criar um **Segredo do Aplicativo** para seu aplicativo e copiá-lo. Você precisará dele em breve.
- Copiar a **ID do Aplicativo** atribuída ao aplicativo. Você também precisará dela em breve.

### Etapa 3: criar suas políticas

No B2C do AD do Azure, cada experiência do usuário é definida por uma [**política**](active-directory-b2c-reference-policies.md). Este aplicativo contém três experiências de identidade: inscrição, entrada e entrada com o Facebook. Você precisará criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Ao criar suas três políticas, não se esqueça de:

- Escolher o **Nome de Exibição** e alguns outros atributos de inscrição em sua política de inscrição.
- Escolher as declarações de aplicativo **Nome de Exibição** e **ID do Objeto** em cada política. Você pode escolher outras declarações também.
- Copie o **Nome** de cada política após criá-lo. Ele deve ter o prefixo `b2c_1_`. Em breve, você precisará esses nomes de política. 

Depois de criar suas três políticas com êxito, você está pronto para criar o aplicativo.

Observe que este artigo não aborda como usar as políticas que você acabou de criar. Para saber mais sobre como as políticas funcionam AD B2C do Azure, você deve começar com o [Tutorial de introdução do aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

### Etapa 4: baixar o código

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Para compilar o exemplo conforme você avança, você pode [baixar um projeto de esqueleto como .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip) ou clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE]**É necessário baixar o esqueleto para concluir este tutorial.** Devido à complexidade da implementação de um aplicativo totalmente funcional no Android, o **esqueleto** tem código de experiência do usuário que será executado quando você concluir o tutorial abaixo. Esta é uma medida de economia de tempo para o desenvolvedor. O código de experiência do usuário não é relevante para o tópico da adição do B2C a um aplicativo do Android.

O aplicativo completo também está [disponível como. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) ou na ramificação `complete` do mesmo repositório.


Para compilar com Maven, você pode usar o pom.xml de nível superior


  * Siga as etapas em [Seção de pré-requisitos para configurar o Maven para Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)
  * Configure o emulador com o SDK 21
  * Vá para a pasta raiz onde você clonou o repositório
  * Execute o comando: mvn clean install
  * Altere o diretório para o exemplo de início rápido: cd samples\\hello
  * Execute o comando: mvn android:deploy android:run
  * Você deve ver a inicialização do aplicativo
  * Insira as credenciais de usuário de teste para testar!

Pacotes de JAR também serão enviados juntamente com o pacote aar.

### Etapa 5: baixar a ADAL para Android e adicioná-la ao seu espaço de trabalho do Eclipse

Facilitamos para você ter várias opções para usar esta biblioteca em seu projeto Android:

* Você pode usar o código-fonte para importar essa biblioteca no Eclipse e vinculá-la ao seu aplicativo.
* Se estiver usando o Android Studio, você poderá usar o formato de pacote *aar* e fazer referência aos binários.

####Opção 1: origem via Git

Para obter o código-fonte do SDK via git, basta digitar:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src
    
    use the branch "convergence"

####Opção 2: binários via Gradle

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
    compile('com.microsoft.aad:adal:2.0-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0-alpha
}
```

####Opção 3: AAR via Maven

Se você estiver usando o plug-in m2e no Eclipse, você pode especificar a dependência em seu arquivo POM. XML:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0-alpha</version>
    <type>aar</type>
</dependency>
```


####Opção 5: pacote jar dentro da pasta de bibliotecas
Você pode obter o arquivo jar do Maven, o repositório, e colocá-lo na pasta *libs* em seu projeto. Você precisa copiar os recursos necessários para seu projeto também, uma vez que os pacotes jar não os incluem.


### Etapa 6: adicionar referências à ADAL do Android em seu projeto


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

### Etapa 7: adicionar código para chamar o ADAL para Android

Vamos criar uma atividade principal e chamá-la de `ToDoActivity`.

É necessário inicializar a atividade e adicionar alguns botões que controlarão nossa interface do usuário. Novamente, isso é muito simples e bastante familiar se você já escreveu código Android antes:

```
public class ToDoActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();
```

Em seguida, na mesma classe (não feche o colchete ainda), adicione nossos botões da interface do usuário. Você verá que adicionamos botões para Entrada do Facebook e Entrada de Email. Isso usará nossas políticas definidas no arquivo `constants.java`:

```
        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

    ```
Create an instance of AuthenticationContext at your main Activity:

    ```Java
 		 mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false, InMemoryCacheStore.getInstance());
            mAuthContext.getCache().removeAll();

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
    ```
  * NOTE: mContext is a field in your activity
 
 Let's also define our Result method `onActivityResult` that will be called when we have a result from the callback we'll write later:
 
 ```
 @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }
 ```

Next, let's define our callback:

    ```Java
    mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, Constants.POLICY, Constants.CLIENT_ID, Constants.REDIRECT_URL, Constants.USER_HINT, Constants.PROMPT,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getAccessToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
        }
        Toast.makeText(getApplicationContext(), TAG + "done", Toast.LENGTH_SHORT).show();
    }
        ```
        
 You may notice that this relies on methods we haven't written yet, such as `updateLoggedInUser()` and `getTasks()`. We'll write those below.	You can safely ignore the errors in Android Studio for now.

Explanation of the parameters:

  * ***SCOPES*** is required and is the scopes you are trying to reqeust access for. For the B2C preview this is the same as the Clientid but will change in the future.
  * ***POLICY*** is the policy for while you wish to authenticate the user. 
  * ***CLIENT_ID*** is required and comes from the AzureAD Portal.
  * You can setup redirectUri as your packagename. It is not required to be provided for the acquireToken call.
  * ***USER_HINT*** is the way we look up if the user is already in the cache and prompt the user if they are not found or the access token is invalid.
  * ***PROMPT*** helps to ask for credentials to skip cache and cookie.
  * ***Callback*** will be called after authorization code is exchanged for a token.

  The Callback will have an object of AuthenticationResult which has accesstoken, date expired, and idtoken info.

> [AZURE.NOTE]	Microsoft Intune's Company portal app provides the broker component and may be installed on the user's device. Developers should be prepared to allow for it's use as it provides SSO across all applications on the device. ADAL for Android will use the broker account if there is one user account created in the Authenticator. To use the broker, the developer needs to register a special redirectUri for broker usage. RedirectUri is in the format of msauth://packagename/Base64UrlencodedSignature. You can get your redirecturi for your app using the script `brokerRedirectPrint.ps1` or use API call `mContext.getBrokerRedirectUri()`. The signature is related to your signing certificates from the Google Play store.

 A Developer can skip the broker user with:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.WARNING] In order to reduce the complexity of this B2C Quickstart, we have opted in our sample to skip the broker. However, this is not recommended.

Next, let's create some helper methods that will get the token alone during our authentication calls to the Task API:

```
private void getToken(final AuthenticationCallback callback) {

        // one of the acquireToken overloads
        mAuthContext.acquireToken(onnstants.SCOPES, Constants.ADDITIONAL_SCOPES, Constants.POLICY,
                Constants.CLIENT_ID, Constants.REDIRECT_URL, Constants.USER_HINT, Constants.PROMPT,
                "nux=1&" + Constants.EXTRA_QP, callback);
    }

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {
        Constants.CURRENT_RESULT = newToken;
    }
    
```

Finally, Your app manifest should have permissions to use AccountManager accounts: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


### Step 8. Call the Task API

Now that we have our Activity wired up and ready to go to do the heavy lifting of grabbing tokens, let's write our API to access the task server.

Our `getTasks` provides an array that represents the tasks in our server 
Our `addTask` and `deleteTask` do the subsequent action and return TRUE or FALSE if it was successful.

Let's write our `getTask` first:

```
private void getTasks() { if (Constants.CURRENT\_RESULT == null || Constants.CURRENT\_RESULT.getAccessToken().isEmpty()) return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(Constants.CURRENT_RESULT.getAccessToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }
 ```
 
 Também vamos escrever um método que inicializará nossas tabelas na primeira execução:
 
 ```
     private void initAppTables() {
        try {
            // Get the Mobile Service Table instance to use
//            mToDoTable = mClient.getTable(WorkItem.class);
//            mToDoTable.TABvLES_URL = "/api/";
            //mTextNewToDo = (EditText)findViewById(R.id.listViewToDo);
            // Create an adapter to bind the items with the view
            //mAdapter = new WorkItemAdapter(ToDoActivity.this, R.layout.listViewToDo);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```
 
 You'll see that this code requires some additional methods to do it's work. Let's write those now.
 
 ### Create endpoint URL generator
 
 We need to generate the endpoint URL that we'll be connecting to. Let's do that in the same class file:
 
 
 ```
     private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

    ```


Note that we add the access token to the request in the following code:

### Step 9: Let's write some UX methods

Android requires us to handle some callbacks in order to operate the app. These are `createAndShowDialog` and `onResume()`. This is pretty simple and very familiar if you've written Android code before. 

Let's write those now:

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }
    
```

And now manage our dialog callbacks:


```
/ * * * /** * Cria uma caixa de diálogo e a mostra * * @param exception A exceção a ser mostrada na caixa de diálogo */ private void createAndShowDialog(Exception exception, String title) { createAndShowDialog(exception.toString(), title); }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }
    
 ```
    


### Etapa 10: executar o aplicativo de exemplo

Por fim, compile e execute o aplicativo no Android Studio ou Eclipse. Inscreva-se ou entre no aplicativo e crie tarefas para o usuário conectado. Saia e entre novamente como um usuário diferente, criando tarefas para esse usuário.

Observe como as tarefas são armazenados por usuário na API, pois a API extrai a identidade do usuário do token de acesso que recebe.

Para referência, o exemplo concluído [é fornecido como. zip aqui](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


<!--

### Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a node.js Web API from a node.js Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

### Important Information


#### Encryption

ADAL encrypts the tokens and store in SharedPreferences by default. You can look at the StorageHelper class to see the details. Android introduced AndroidKeyStore for 4.3(API18) secure storage of private keys. ADAL uses that for API18 and above. If you want to use ADAL for lower SDK versions, you need to provide secret key at AuthenticationSettings.INSTANCE.setSecretKey

#### Session cookies in Webview

Android webview does not clear session cookies after app is closed. You can handle this with sample code below:
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
Mais informações sobre cookies: http://developer.android.com/reference/android/webkit/CookieSyncManager.html
 

<!---HONumber=Sept15_HO3-->