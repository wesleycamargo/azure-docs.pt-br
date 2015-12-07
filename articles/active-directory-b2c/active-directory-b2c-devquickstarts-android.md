<properties pageTitle="Visualização do B2C do AD do Azure: chamando uma API Web de um aplicativo do Android | Microsoft Azure" description="Este artigo mostrará como criar um aplicativo de "Lista de Tarefas Pendentes" do Android que chama uma API Web do node.js usando tokens de portador OAuth 2.0. O aplicativo do Android e a api Web usam o B2C do AD do Azure para gerenciar identidades de usuário e autenticar usuários." services="active-directory-b2c" documentationCenter="android" authors="brandwe" manager="msmbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="11/19/2015"
	ms.author="brandwe"/>

# Visualização do B2C do AD do Azure: chamando uma API Web de um aplicativo do Android

Com o AD B2C do Azure, você pode adicionar recursos poderosos de gerenciamento de identidades de autoatendimento para seus aplicativos do Android e APIs Web em poucas etapas. Este artigo mostra como criar um aplicativo de "Lista de Tarefas Pendentes" do Android que chama uma API Web do node.js usando tokens de portador OAuth 2.0. Tanto o aplicativo do Android quanto a API Web usam o AD B2C do Azure para gerenciar identidades de usuário e autenticar usuários.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

 	
> [AZURE.NOTE]Este guia de início rápido tem o pré-requisito de que você deve ter uma API Web protegida pelo AD do Azure com B2C para funcionar totalmente. Criamos uma para .Net e node.js que você pode usar. Este passo a passo pressupõe que o exemplo de API Web do node.js está configurado. Consulte o [Tutorial da API Web do B2C do AD do Azure para Node.js](active-directory-b2c-devquickstarts-api-node.md).

 
> [AZURE.NOTE]Este artigo não aborda como implementar conexão, registro e gerenciamento de perfil com o Azure AD B2C. Ele se concentra na chamada a APIs Web depois que o usuário já está autenticado. Se não estiver, você deve começar com o [tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para saber mais sobre os conceitos básicos do Azure AD B2C.


Para clientes Android que precisam acessar recursos protegidos, o AD do Azure fornece a biblioteca de autenticação do Active Directory, ou ADAL. A única finalidade da ADAL é tornar mais fácil para seu aplicativo obter tokens de acesso. Para demonstrar como é fácil, vamos compilar aqui um aplicativo de lista de tarefas pendentes para Android que:

-	Obtém tokens de acesso para chamar a API da lista de tarefas pendentes usando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Obtém a lista de tarefas pendentes de um usuário
-	Faz logout dos usuários.



### Etapa 1: obter um diretório AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e assim por diante. Se você não tiver um, acesse [criar um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir.

### Etapa 2: criar um aplicativo

Agora você precisa criar um aplicativo no diretório B2C, que dá ao AD do Azure algumas informações que ele precisa para se comunicar de forma segura com seu aplicativo. O aplicativo e a API Web serão representados por uma única **ID de aplicativo** nesse caso, uma vez que incluem um aplicativo lógico. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). Certifique-se de

- Incluir um **aplicativo Web/api Web** no aplicativo
- Digitar `urn:ietf:wg:oauth:2.0:oob` como uma **URL de Resposta** - é a URL padrão para este exemplo de código.
- Criar um **Segredo do Aplicativo** para seu aplicativo e copiá-lo. Você precisará dele em breve.
- Copiar a **ID do Aplicativo** atribuída ao aplicativo. Você também precisará dela em breve.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

### Etapa 3: criar suas políticas

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

No AD B2C do Azure, cada experiência do usuário é definida por uma [**política**](active-directory-b2c-reference-policies.md). Este aplicativo contém três experiências de identidade: inscrição, entrada e entrada com o Facebook. Você precisará criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Ao criar suas três políticas, não se esqueça de:

- Escolha o **Nome de exibição** e alguns outros atributos de inscrição em sua política de inscrição.
- Escolha as declarações de aplicativo **Nome de exibição** e **ID de objeto** em cada política. Você pode escolher outras declarações também.
- Copie o **nome** de cada política após criá-lo. Ele deve ter o prefixo `b2c_1_`. Em breve, você precisará esses nomes de política. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar suas três políticas com êxito, você está pronto para criar o aplicativo.

Observe que este artigo não aborda como usar as políticas que você acabou de criar. Se quiser aprender sobre como as políticas funcionam no AD B2C do Azure, comece com o [tutorial de introdução ao aplicativo Web da .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

### Etapa 4: baixar o código

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Para compilar o exemplo à medida que avança, você pode [baixar um projeto de esqueleto como um. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip) ou clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE]**É necessário baixar o esqueleto para concluir este tutorial.** Devido à complexidade da implementação de um aplicativo totalmente funcional no Android, o **esqueleto** tem código de experiência do usuário que será executado quando você concluiu o tutorial abaixo. Esta é uma medida de economia de tempo para o desenvolvedor. O código de experiência do usuário não é relevante para o tópico da adição do B2C a um aplicativo do Android.

O aplicativo completo também está [disponível como. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) ou na `complete` ramificação do mesmo repositório.


Para compilar com Maven, você pode usar o pom.xml de nível superior


  * Siga as etapas na seção [Pré-requisitos para configurar o seu maven para android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)
  * Configure o emulador com o SDK 21
  * Vá para a pasta raiz onde você clonou o repositório
  * Execute o comando: mvn clean install
  * Altere o diretório para o exemplo de início rápido: cd samples\\hello
  * Execute o comando: mvn android:deploy android:run
  * Você deve ver a inicialização do aplicativo
  * Insira as credenciais de usuário de teste para testar!

Pacotes de JAR também serão enviados juntamente com o pacote aar.

### Etapa 5: baixar a ADAL para Android e adicioná-la ao seu espaço de trabalho do Android Studio

Facilitamos para você ter várias opções para usar esta biblioteca em seu projeto Android:

* Você pode usar o código-fonte para importar essa biblioteca no Eclipse e vinculá-la ao seu aplicativo.
* Se estiver usando o Android Studio, você poderá usar o formato de pacote *aar* e fazer referência aos binários.



####Opção 1: binários via Gradle (recomendado)

Você pode obter os binários do repositório central do Maven. O pacote AAR pode ser incluído como a seguir em seu projeto no AndroidStudio (exemplo: em `build.gradle`):

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
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

####Opção 2: aar via Maven

Se estiver usando o plug-in m2e no Eclipse, você pode especificar a dependência no seu `pom.xml` arquivo:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

####Opção 3: origem via Git (último recurso)

Para obter o código-fonte do SDK via git, basta digitar:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src
    
    use the branch "convergence"


### Etapa 6: definir o arquivo de configuração

Usaremos a configuração que você definiu no portal B2C acima para configurar o projeto do Android.

Abra `helpes/Constants.java` e preencha os valores para os seguintes itens:

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
**ESCOPOS** - é o escopo que passamos para o servidor que desejamos solicitar do servidor para o logon do usuário. Para a Visualização do B2C, passamos o client\_id. No entanto, isso será alterado para ler escopos no futuro. Em seguida, esse documento será atualizado. **ADDITIONAL\_SCOPES** -esses são os escopos adicionais que você talvez queira usar para seu aplicativo. Isso será usado no futuro **CLIENT\_ID** -ID do aplicativo que você obteve no portal de **REDIRECT\_URL** -o redirecionamento no qual podemos esperar que o token seja postado. **EXTRA\_QP** - qualquer extra que você deseja passar para o servidor no formato codificado da URL. **FB\_POLICY** -a política que você está chamando. A parte posterior importante deste passo a passo. **EMAIL\_SIGNIN\_POLICY** -a política que você está chamando. A parte posterior importante deste passo a passo. **EMAIL\_SIGNUP\_POLICY** -a política que você está chamando. A parte posterior importante deste passo a passo.

### Etapa 7: adicionar referências à ADAL do Android em seu projeto


> [AZURE.NOTE]O ADAL para Android usa um modelo com base em tentativa para invocar a Autenticação. As tentativas "modelam" o aplicativo para realizar o trabalho. Este exemplo inteiro e, de fato, todos os que usam o ADAL para Android, gerenciam tentativas e passam informações entre elas.


A primeira coisa que precisamos fazer é informar o Android do layout de nosso aplicativo, incluindo as Intents() que desejamos usar. Vou explicar essas tentativas em detalhes mais tarde.

Atualize o arquivo AndroidManifest.xml de seu projeto para incluir todas as nossas tentativas:

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

Como você pode ver, definimos cinco atividades que utilizaremos.

**AuthenticationActivity** - proveniente de ADAL é o que fornece o logon no webview

**LoginActivity** -é o que exibe nossas políticas de entrada e os botões para cada política.

**SettingsActivity** -permite alterar configurações do aplicativo em tempo de execução.

**AddTaskActivity** -permite adicionar tarefas à nossa API REST protegidas pelo AD do Azure

**ToDoActivity** -a atividade principal que exibe tarefas.



### Etapa 8: criar a atividade de logon

Vamos criar uma atividade principal e chamá-la `LoginActivity`.

Crie um arquivo chamado `LoginActivity.java`

É necessário inicializar a atividade e adicionar alguns botões que controlarão nossa interface do usuário. Novamente, isso é muito simples e bastante familiar se você já escreveu código Android antes:

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

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

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
O que fizemos foi criar botões que chamam nossa tentativa ToDoActivity (que chamará ADAL quando precisarmos de um token) com nossa própria atividade como uma referência e um parâmetro extra. Este parâmetro é passado pelo `intent.putExtra()` método. Você pode ver aqui que definimos "thePolicy" como especificado no `Constants.java`. Isso permite que a tentativa saiba qual política invocar durante a autenticação.

### Etapa 9: criar a atividade de configurações

Essa é apenas uma atividade que popula nossa interface do usuário de configurações.

Crie um arquivo chamado `SettingsActivity.java`

CRUD muito simples aqui:

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

	//private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_settings);

        loadSettings();
//		checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//		checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


	}

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

	private void saveSettings(String key, boolean value) {
		SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
				Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
		Editor prefsEditor = prefs.edit();
		prefsEditor.putBoolean(key, value);
		prefsEditor.commit();
	}
}
```

### Etapa 10: criar a atividade de AddTask

Isso nos permitirá adicionar uma tarefa para o ponto de extremidade da API REST. Novamente, é bastante simples.

Crie um arquivo chamado `AddTaskActivity.java`

E escreva:

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

### Etapa 11: criar a atividade de lista de tarefas

Agora, temos a atividade mais importante, que nos permite obter um token do AD do Azure para a política e, em seguida, usar esse token para chamar o servidor da API REST de tarefa.

Crie um arquivo chamado `ToDoActivity.java`

Escreva o seguinte (explicarei as chamadas posteriormente):

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

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
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
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

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}
   
```

        
 Você pode perceber que isso se baseia nos métodos que ainda não escrevemos, como `updateLoggedInUser()`, `clearSessionCookie()` e `getTasks()`. Vamos escrevê-los abaixo. Você pode ignorar com segurança os erros no Android Studio por enquanto.

Explicação dos parâmetros:

  * ***ESCOPOS*** é necessário e são os escopos aos quais você está tentando solicitar acesso. Para a visualização B2C, isso é o mesmo que o Clientid, mas será alterado no futuro.
  * ***POLÍTICA*** é a política para quando você deseja autenticar o usuário. 
  * ***CLIENT\_ID*** é necessário e é proveniente do Portal AzureAD.
  * Você pode configurar redirectUri como seu nome de pacote. Não é necessário ser fornecido para a chamada acquireToken.
  * ***getUserInfo()*** é a maneira como pesquisaremos se o usuário já estiver no cache e o avisa se os parâmetros não foram encontrados ou se o token de acesso é inválido. Escrevemos esse método abaixo.
  * ***PromptBehavior.always*** ajuda a solicitar que as credenciais ignorem o cache e o cookie.
  * ***Retorno de chamada*** será chamado depois que o código de autorização é trocado por um token.

  O retorno de chamada terá um objeto AuthenticationResult que tem informações de token de acesso, data de vencimento e token de ID.

> [AZURE.NOTE]O aplicativo do portal da Empresa do Microsoft Intune fornece o componente de agente e pode ser instalado no dispositivo do usuário. Os desenvolvedores devem estar preparados para permitir seu uso, pois ele fornece o SSO em todos os aplicativos no dispositivo. O ADAL para Android usará a conta do agente se houver uma conta de usuário criada no Autenticador. Para usar o agente, o desenvolvedor precisa registrar um redirectUri especial para uso do agente. RedirectUri está no formato msauth://packagename/Base64UrlencodedSignature. Você pode obter o redirecturi para seu aplicativo usando o script `brokerRedirectPrint.ps1` ou use a chamada à API `mContext.getBrokerRedirectUri()`. A assinatura está relacionada aos seus certificados de autenticação do repositório da Google Play.

 Um desenvolvedor pode ignorar o usuário do agente com:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE]Para reduzir a complexidade desse guia de início rápido do B2C, optamos por ignorar o agente em nosso exemplo.

Em seguida, vamos criar alguns métodos auxiliares que obterão o token sozinho durante nossas chamadas de autenticação para a API de tarefa.

**No mesmo arquivo** chamado `ToDoActivity.java`

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

Também vamos adicionar alguns métodos que executam "set" e "get" para nosso AuthenticationResult (que tem o token) para as CONSTANTES globais. Isso é necessário porque, embora `ToDoActivity.java` usa **sResult**, em seu fluxo nossas outras atividades não teriam acesso ao token para trabalhar (como adicionar uma tarefa em nosso `AddTaskActivity.java`)

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }

    
```
### Etapa 12: criar um método para retornar um UserIdentifier

O ADAL para Android representa o usuário na forma de um objeto **UserIdentifier**. Isso gerencia o usuário e nos permite saber se o mesmo usuário está sendo usado em nossas chamadas para que possamos usar o cache em vez de fazer uma nova chamada ao servidor. Para tornar isso mais fácil, criamos um `getUserInfo()` que retornará um UserIdentifier que podemos usar para `acquireToken()`. Também criamos um método getUniqueId() que usará rapidamente para nós a ID do UserIdentifier no cache.

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }
    
```
 
### Etapa 13: escrever alguns métodos auxiliares

Precisamos escrever alguns métodos auxiliares que nos ajudam a limpar os cookies e fornecem um AuthenticationCallback. Eles são usados apenas para o exemplo a fim de garantir que estamos em um estado limpo ao chamar nossa atividade ToDo.

**No mesmo arquivo** chamado `ToDoActivity.java`

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
``` 

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }
    
```   

### Etapa 14: chamar a API de tarefa

Agora que temos nossa atividade configurada e pronta para fazer o trabalho pesado de capturar tokens, vamos escrever nossa API para acessar o servidor de tarefa.

Nosso `getTasks` fornece uma matriz que representa as tarefas em nosso servidor

Vamos escrever nosso `getTask` primeiro:

**No mesmo arquivo** chamado `ToDoActivity.java`

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
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
 
 **No mesmo arquivo** chamado `ToDoActivity.java`
 
```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```
 
 Você verá que esse código requer alguns métodos adicionais para fazer o seu trabalho. Vamos escrever isso agora.
 
### Criar um gerador de URL de ponto de extremidade
 
 É necessário gerar a URL de ponto de extremidade à qual nos conectaremos. Vamos fazer isso no mesmo arquivo de classe:
 
 **No mesmo arquivo** chamado `ToDoActivity.java`
 
 ``` private URL getEndpointUrl() { URL endpoint = null; try { endpoint = new URL(Constants.SERVICE\_URL); } catch (MalformedURLException e) { e.printStackTrace(); } return endpoint; }

 ```


Observe que adicionamos o token de acesso à solicitação no código a seguir:

### Etapa 15: vamos escrever alguns métodos de UX

O Android exige manipular alguns retornos de chamada para operar o aplicativo. Esses são `createAndShowDialog` e `onResume()`. Novamente, isso é muito simples e bastante familiar se você já escreveu código do Android antes:

Vamos escrever isso agora:

**No mesmo arquivo** chamado `ToDoActivity.java`

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

E agora gerenciar nossos retornos de chamada da caixa de diálogo:

**No mesmo arquivo** chamado `ToDoActivity.java`


```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

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

É isso! Você deve ter uma `ToDoActivity.java` arquivo compilado. Todo o projeto deve ser compilado agora também.
    


### Etapa 16: executar o aplicativo de exemplo

Por fim, compile e execute o aplicativo no Android Studio ou Eclipse. Inscreva-se ou entre no aplicativo e crie tarefas para o usuário conectado. Saia e entre novamente como um usuário diferente, criando tarefas para esse usuário.

Observe como as tarefas são armazenados por usuário na API, pois a API extrai a identidade do usuário do token de acesso que recebe.

Para referência, o exemplo concluído [é fornecido como um .zip aqui](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


### Informações importantes


#### Criptografia

A ADAL criptografa os tokens e os armazena em SharedPreferences por padrão. Você pode examinar a classe StorageHelper para ver os detalhes. O Android introduziu o armazenamento de chaves privadas AndroidKeyStore 4.3(API18). A ADAL o utiliza para API18 e superior. Se você quiser usar a ADAL para versões anteriores do SDK, você precisa fornecer a chave secreta em AuthenticationSettings.INSTANCE.setSecretKey

#### Cookies de sessão no Webview

O Webview para Android não limpa os cookies de sessão depois que o aplicativo é fechado. Você pode lidar com isso com o código de exemplo abaixo: ```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
``` Mais sobre cookies: http://developer.android.com/reference/android/webkit/CookieSyncManager.html
 

<!---HONumber=AcomDC_1125_2015-->