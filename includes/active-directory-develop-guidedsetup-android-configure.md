
## <a name="create-an-application-express"></a>Criar um aplicativo (Expresso)
Agora você precisa registrar seu aplicativo no *Portal de Registro de Aplicativos da Microsoft*:
1. Registre o aplicativo por meio do [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure)
2.  Insira um nome para o aplicativo e seu email
3.  Verifique se a opção Instalação Guiada está marcada
4.  Siga as instruções para obter a ID do aplicativo e colá-lo no código

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Adicionar as informações de registro do aplicativo à sua solução (Avançado)
Agora você precisa registrar seu aplicativo no *Portal de Registro de Aplicativos da Microsoft*:
1. Acesse o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar um aplicativo
2. Insira um nome para o aplicativo e seu email 
3. Verifique se a opção Instalação Guiada está desmarcada
4. Clique em `Add Platform` e, em seguida, selecione `Native Application` e clique em Salvar
5.  Abra `MainActivity` (em `app` > `java` > *`{host}.{namespace}`*)
6.  Substitua *[Inserir a ID do aplicativo aqui]* na linha que começa com `final static String CLIENT_ID` pela ID do aplicativo que você acabou de registrar:

```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Abra `AndroidManifest.xml` (em `app` > `manifests`) Adicione a atividade a seguir ao nó `manifest\application`. Isso registra um `BrowserTabActivity` para permitir que o sistema operacional continue executando o aplicativo depois de concluir a autenticação:
</li>
</ol>

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        
        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```
<!-- Workaround for Docs conversion bug -->
<ol start="8">
<li>
Em `BrowserTabActivity`, substitua `[Enter the application Id here]` pela ID do aplicativo.
</li>
</ol>
