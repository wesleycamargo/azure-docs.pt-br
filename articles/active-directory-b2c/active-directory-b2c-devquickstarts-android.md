---
title: 'Azure Active Directory B2C: Chamar uma API da Web de um aplicativo Android | Microsoft Docs'
description: "Este artigo mostra como criar um aplicativo de &quot;lista de tarefas pendentes&quot; do Android que chama uma API Web do Node.js usando tokens de portador OAuth 2.0. Tanto o aplicativo do Android quanto a API Web usam o Azure Active Directory B2C para gerenciar identidades de usuário e autenticar usuários."
services: active-directory-b2c
documentationcenter: android
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 07/22/2016
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 47300333a7c41ad6f23b20ae0698bee839efd3bb


---
# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C: chamar uma API Web de um aplicativo do Android
> [!WARNING]
> Este tutorial requer algumas atualizações importantes, especificamente para remover o uso do ADAL Android para B2C.  Vamos publicar novas instruções para o uso do Azure AD B2C em aplicativos Android na próxima semana, e é recomendável aguardar até lá.  Porém, se você quiser experimentar, fique à vontade para continuar a ler o artigo a seguir.
>
>

Usando o Azure Active Directory B2C (Azure AD), você poderá adicionar recursos poderosos de gerenciamento de identidades de autoatendimento a seus aplicativos do Android e APIs Web em poucas etapas. Este artigo mostra como criar um aplicativo de "lista de tarefas pendentes" do Android que chama uma API Web do Node.js usando tokens de portador OAuth 2.0. Tanto o aplicativo Android quanto a API Web usam o AD B2C do Azure para gerenciar identidades de usuário e para autenticar usuários.

Este guia de início rápido requer que você tenha uma API Web protegida pelo Azure AD com B2C para funcionar totalmente. Criamos uma para .NET e para Node.js que você pode usar. Este passo a passo presume que o exemplo de API Web do Node.js esteja configurado. Para saber mais, confira o [tutorial da API Web do Azure AD B2C para Node.js](active-directory-b2c-devquickstarts-api-node.md).

Para os clientes Android que precisam acessar recursos protegidos, o Azure AD fornece a ADAL (Biblioteca de Autenticação do Active Directory). A única finalidade da ADAL é tornar mais fácil para seu aplicativo obter tokens de acesso. Para demonstrar como é fácil, vamos compilar neste guia um aplicativo de lista de tarefas pendentes para Android que:

* Obtém tokens de acesso para chamar a API da lista de tarefas pendentes usando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Obtém as listas de tarefas pendentes dos usuários.
* Desconecta usuários.

> [!NOTE]
> Este artigo não aborda como implementar a entrada, a inscrição e o gerenciamento de perfil com o AD B2C do Azure. Ele se concentra em como chamar APIs Web depois que o usuário é autenticado. Você deve começar com o [tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para saber mais sobre os conceitos básicos do Azure AD B2C, caso ainda não tenha feito isso.
>
>

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório AD B2C do Azure
Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos etc. Se você ainda não tiver um, [crie um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir neste guia.

## <a name="create-an-application"></a>Criar um aplicativo
Em seguida, você precisa criar um aplicativo em seu diretório B2C. Isso fornece ao Azure AD as informações de que ele precisa para se comunicar de forma segura com seu aplicativo. O aplicativo e a API Web são representados por uma única **ID do Aplicativo** nesse caso, pois abrangem um aplicativo lógico. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). É necessário que você:

* Incluir um **aplicativo Web**/**api Web** no aplicativo.
* Digite `urn:ietf:wg:oauth:2.0:oob` como uma **URL de Resposta**. É a URL padrão deste exemplo de código.
* Crie um **Segredo de aplicativo** para seu aplicativo e copie-o. Você precisará dela mais tarde. Observe que esse valor precisa ser [seguido por caracteres de escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) antes de ser usado.
* Copie a **ID do aplicativo** atribuída ao seu aplicativo. Você também precisará dela mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar suas políticas
[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

No Azure AD B2C, toda experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Esse aplicativo contém três experiências de identidade: inscrição, entrada e entrada usando o Facebook.  Você precisa criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Ao criar as três políticas, não deixe de:

* Escolher o **Nome de exibição** e outros atributos de inscrição em sua política de inscrição.
* Escolha as declarações de aplicativo **Nome de exibição** e **ID do Objeto** em todas as políticas. Você pode escolher outras declarações também.
* Copie o **Nome** de cada política depois de criá-la. Ele deve ter o prefixo `b2c_1_`.  Você precisará esses nomes de política mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as três políticas, você estará pronto para compilar o aplicativo.

Observe que este artigo não aborda como usar as políticas que você acabou de criar. Para saber mais sobre o funcionamento das políticas no Azure AD B2C, comece com o [tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Baixar o código
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Para compilar o exemplo à medida que avança, [baixe um projeto de esqueleto como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip). Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [!NOTE]
> **Você tem que baixar o esqueleto para concluir este tutorial.**  Devido à complexidade da implementação de um aplicativo totalmente funcional do Android, o esqueleto tem código de experiência do usuário que será executado depois que você concluir este tutorial. Essa é uma medida de economia de tempo para os desenvolvedores. O código de experiência do usuário não é relevante para o tópico sobre como adicionar o B2C a um aplicativo do Android.
>
>

O aplicativo completo também está [disponível como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) ou na ramificação `complete` do mesmo repositório.

Para compilar com o Maven, você pode usar `pom.xml` no nível superior.

1. Siga as etapas na [seção de pré-requisitos para configurar o Maven para Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
2. Configure um emulador com o SDK 21.
3. Vá para a pasta raiz onde você clonou o repositório.
4. Execute o comando `mvn clean install`.
5. Altere o diretório para a amostra do Guia de Início Rápido `cd samples\hello`.
6. Execute o comando `mvn android:deploy android:run`.

Você deve ver a inicialização do aplicativo. Insira as credenciais de usuário de teste para testá-lo.

Os pacotes JAR também serão enviados com o Pacote AAR do Android.

## <a name="download-the-android-adal-and-add-it-to-your-android-studio-workspace"></a>Baixar a ADAL para Android e adicioná-la ao seu espaço de trabalho do Android Studio
Você tem opções para usar essa biblioteca em seu projeto Android:

* Você pode usar o código-fonte para importar essa biblioteca no Eclipse e vinculá-la ao seu aplicativo.
* Se estiver usando o Android Studio, você poderá usar o formato de pacote AAR e fazer referência aos binários.

### <a name="option-1-binaries-via-gradle-recommended"></a>Opção 1: binários via Gradle (recomendado)
Você pode obter os binários do repositório central do Maven. O pacote AAR pode ser incluído em seu projeto no Android Studio (por exemplo, em `build.gradle`) deste modo:

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

### <a name="option-2-aar-via-maven"></a>Opção 2: AAR via Maven
Se usar o plug-in `m2e` no Eclipse, você poderá especificar a dependência em seu arquivo `pom.xml`:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### <a name="option-3-source-via-git-last-resort"></a>Opção 3: origem via Git (último recurso)
Para obter o código-fonte do SDK via Git, digite:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

Use a **convergência**

## <a name="set-up-your-configuration-file"></a>Definir o arquivo de configuração
Use a configuração que você definiu anteriormente no portal B2C para configurar o projeto do Android.

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
* `SCOPES`: os escopos que você passa ao servidor e que deseja solicitar de volta do servidor quando um usuário entra. Para a visualização B2C, você passa `client_id`. No entanto, espera-se que isso seja alterado para `read scopes` no futuro. Este documento será atualizado quando isso ocorrer.
* `ADDITIONAL_SCOPES`: os escopos adicionais que você pode querer usar em seu aplicativo. Eles devem ser usados no futuro.
* `CLIENT_ID`: a ID do aplicativo que você obteve no portal.
* `REDIRECT_URL`: o redirecionamento em que você espera que o token seja retornado.
* `EXTRA_QP`: qualquer extra que você deseje passar para o servidor no formato codificado da URL.
* `FB_POLICY`: a política que você está invocando. Essa é a parte mais importante deste passo a passo.
* `EMAIL_SIGNIN_POLICY`: a política que você está invocando. Essa é a parte mais importante deste passo a passo.
* `EMAIL_SIGNUP_POLICY`: a política que você está invocando. Essa é a parte mais importante deste passo a passo.

## <a name="add-references-to-android-adal-to-your-project"></a>Adicionar referências à ADAL do Android ao seu projeto
> [!NOTE]
> A ADAL do Android usa um modelo com base em tentativa para invocar a autenticação. As tentativas "modelam" o aplicativo para realizar o trabalho. Este exemplo inteiro e todas as ADAL do Android se concentram em como gerenciar as tentativas e em como transmitir informações entre elas.
>
>

Primeiro, informe o Android sobre o layout de seu aplicativo, inclusive as tentativas que deseja usar. Essas tentativas serão explicadas em detalhes posteriormente neste tutorial.

Atualize o arquivo `AndroidManifest.xml` de seu projeto para incluir todas as suas tentativas:

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

Como pode ver, você define cinco atividades. Você usará todos elas.

* `AuthenticationActivity`: isso provém da ADAL e fornece a exibição da Web de entrada.
* `LoginActivity`: exibe suas políticas de entrada e os botões para cada política.
* `SettingsActivity`: você pode usar isso para alterar as configurações do aplicativo no tempo de execução.
* `AddTaskActivity`: você pode usar isso para adicionar tarefas protegidas pelo Azure AD à sua API REST.
* `ToDoActivity`: essa é a atividade principal que exibe tarefas.

## <a name="create-the-sign-in-activity"></a>Criar a atividade de entrada
Crie uma atividade principal e dê o nome de `LoginActivity`.

Crie um arquivo chamado `LoginActivity.java`.

Você precisa inicializar a atividade e adicionar alguns botões que controlarão a sua interface do usuário. Você deve conhecer isso se já escreveu código Android antes.

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
Agora você criou botões que chamam a tentativa `ToDoActivity` (que chama a ADAL quando você precisa de um token). Eles fazem isso usando sua atividade como referência e parâmetro adicional. Este parâmetro é passado pelo `intent.putExtra()` método. Você define a `"thePolicy"` usando o que especificou no `Constants.java`. Isso informa à tentativa qual política invocar durante a autenticação.

## <a name="create-the-settings-activity"></a>Criar a atividade de configurações
Essa é uma atividade que popula nossa interface do usuário de configurações.

Crie um arquivo chamado `SettingsActivity.java` para operações CRUD (criar, ler, atualizar e excluir) simples.

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
//        checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//        checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

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

## <a name="create-the-add-task-activity"></a>Criar a atividade adicionar tarefa
Você pode usar essa atividade para adicionar uma tarefa para o ponto de extremidade da API REST.

Crie um arquivo chamado `AddTaskActivity.java` e escreva o descrito a seguir.

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

## <a name="create-the-to-do-list-activity"></a>Criar a atividade de lista de tarefas pendentes
Essa é a atividade mais importante. Você pode usá-la para obter um token do Azure AD para uma política e usar esse token para chamar o servidor da API REST de tarefa.

Crie um arquivo chamado `ToDoActivity.java` e escreva o descrito a seguir. (As chamadas serão explicadas posteriormente).

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


 Você pode ter notado que isso depende de métodos que ainda não foram escritos. Eles incluem `updateLoggedInUser()`, `clearSessionCookie()` e `getTasks()`. Você vai escrevê-los mais adiante neste guia. Você pode ignorar com segurança os erros no Android Studio por enquanto.

Explicação dos parâmetros:

* `SCOPES`: é necessário e são os escopos aos quais você está tentando solicitar acesso. Para a visualização B2C, isso é o mesmo que `client_id`, mas deve mudar no futuro.
* `POLICY`: a política para quando você deseja autenticar o usuário.
* `CLIENT_ID`: obrigatória, vem do portal do Azure AD.
* `redirectUri`: pode ser configurado como o nome do pacote. Não é necessário fornecê-lo para a chamada `acquireToken` .
* `getUserInfo()`: a maneira de pesquisar se um usuário já está em cache. O parâmetro também descreve como solicitar um usuário se ele não for encontrado ou se o token de acesso do usuário for inválido. Esse método será escrito mais adiante neste guia.
* `PromptBehavior.always`: ajuda a solicitar credenciais para ignorar o cache e o cookie.
* `Callback`: será chamado depois que o código de autorização for trocado por um token. Ele terá um objeto `AuthenticationResult`, que contém o token de acesso, a data de expiração e as informações de token da ID.

> [!NOTE]
> O aplicativo do portal da empresa do Microsoft Intune fornece o componente de agente e pode ser instalado no dispositivo do usuário. O aplicativo fornece SSO (logon único) para todos os aplicativos no dispositivo. Os desenvolvedores devem estar preparados para permitir o Intune. A ADAL do Android usará a conta do agente se houver uma conta de usuário criada no autenticador. Para usar o agente, o desenvolvedor precisa registrar um `redirectUri` especial para uso pelo agente. `redirectUri` está no formato de msauth://packagename/Base64UrlencodedSignature. Você pode obter o `redirectUri` para seu aplicativo usando o script `brokerRedirectPrint.ps1` ou usando a chamada à API `mContext.getBrokerRedirectUri()`. A assinatura está relacionada aos seus certificados de autenticação do repositório da Google Play.
>
>

 Você pode ignorar o usuário do agente usando:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [!NOTE]
> Para reduzir a complexidade desse Guia de Início Rápido do B2C, optamos por ignorar o agente em nosso exemplo.
>
>

Em seguida, crie métodos auxiliares que obtêm o token somente durante as chamadas de autenticação para de API de tarefa.

No mesmo arquivo `ToDoActivity.java` , escreva o seguinte.

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

Adicione também métodos que usarão "set" e "get" no `AuthenticationResult` (que contém o token) para o `Constants` global. Embora `ToDoActivity.java` use `sResult` em seus fluxos, você precisa adicionar esses métodos. Caso contrário, suas outras atividades não terão acesso ao token para fazer o trabalho (como a adição de uma tarefa em `AddTaskActivity.java`).

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## <a name="create-a-method-to-return-a-user-identifier"></a>Criar um método para retornar um identificador de usuário
A ADAL do Android representa o usuário na forma de um objeto `UserIdentifier` . Isso gerencia o usuário. Você pode usar o objeto para identificar se o mesmo usuário está sendo usado em suas chamadas. Usando essas informações, você pode confiar no cache em vez de fazer uma nova chamada para o servidor. Para facilitar essa tarefa, criamos o método `getUserInfo()` que retorna `UserIdentifier`. Você pode usá-lo com `acquireToken()`. Além disso, criamos um método `getUniqueId()` que retorna a ID de `UserIdentifier` no cache.

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

### <a name="write-helper-methods"></a>Escrever métodos auxiliares
Em seguida, escreva alguns métodos auxiliares para ajudá-lo a limpar os cookies e fornecer `AuthenticationCallback`. Esses métodos são usados exclusivamente pelo exemplo para garantir que você está em um estado inicial ao chamar a atividade `ToDo` .

No mesmo arquivo denominado `ToDoActivity.java`, escreva o seguinte.

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

## <a name="call-the-task-api"></a>Chamar a API de tarefa
Depois de ter sua atividade pronta para obter tokens, você poderá escrever sua API para acessar o servidor de tarefa.

`getTasks` fornece uma matriz que representa as tarefas no seu servidor.

Inicie com `getTasks`.

No mesmo arquivo denominado `ToDoActivity.java`, escreva o seguinte.

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

Escreva também um método que inicializará nossas tabelas na primeira execução:

No mesmo arquivo denominado `ToDoActivity.java`, escreva o seguinte.

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

Você pode ver que esse código requer alguns métodos adicionais para fazer o seu trabalho. Escreva-os a seguir.

### <a name="create-an-endpoint-url-generator"></a>Criar um gerador de URL de ponto de extremidade
Você precisa gerar a URL de ponto de extremidade à qual se conectará. Faça isso no mesmo arquivo de classe.

**No mesmo arquivo** denominado `ToDoActivity.java`,escreva o que se segue.

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


Observe que você adiciona o token de acesso à solicitação no código discutido na próxima seção.

## <a name="write-some-ux-methods"></a>Escrever alguns métodos UX
O Android exige que você manipule alguns retornos de chamada para operar o aplicativo. Esses são `createAndShowDialog` e `onResume()`. Você deve conhecer isso se já escreveu código Android antes.

No mesmo arquivo denominado `ToDoActivity.java`, escreva o seguinte.

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

Em seguida, gerencie seus retornos de chamada de diálogo.

No mesmo arquivo denominado `ToDoActivity.java`, escreva o seguinte.

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

Agora você deve ter um arquivo `ToDoActivity.java` que compila. Todo o projeto deve ser compilado agora também.

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo
Por fim, compile e execute o aplicativo no Android Studio ou no Eclipse. Inscreva-se ou entre no aplicativo. Crie tarefas para o usuário conectado. Saia e entre novamente como um usuário diferente e crie tarefas para esse usuário.

Observe que as tarefas são armazenados por usuário na API, pois a API extrai a identidade do usuário do token de acesso que recebe.

Para referência, o exemplo completo [é fornecido como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). Você também pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```

## <a name="important-information"></a>Informações importantes
### <a name="encryption"></a>Criptografia
A ADAL criptografa os tokens e os armazena em `SharedPreferences` por padrão. Você pode examinar a classe `StorageHelper` para ver os detalhes. O Android introduziu o armazenamento de chaves privadas **AndroidKeyStore para 4.3(API18)** . A ADAL o utiliza para API18 e superior. Se você quiser usar a ADAL para versões anteriores do SDK, precisará fornecer uma chave secreta em `AuthenticationSettings.INSTANCE.setSecretKey`.

### <a name="session-cookies-in-web-view"></a>Cookies de sessão no modo de exibição da Web
O modo de exibição da Web do Android não limpa os cookies de sessão depois que o aplicativo é fechado. Você pode fazer isso com o código de exemplo a seguir.

```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[Saiba mais sobre cookies](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).



<!--HONumber=Dec16_HO5-->


