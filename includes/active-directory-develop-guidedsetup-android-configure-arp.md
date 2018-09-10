## <a name="add-the-applications-registration-information-to-your-app"></a>Adicionar as informações de registro do aplicativo ao aplicativo

Nesta etapa, você precisa adicionar a ID do Cliente ao projeto.

1.  Abra `MainActivity` (em `app` > `java` > *`{host}.{namespace}`*)
2.  Substitua a linha que começa com `final static String CLIENT_ID` por:
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Abra: `app` > `manifests` > `AndroidManifest.xml`
4. Adicione a atividade a seguir ao nó `manifest\application`. Isso registra um `BrowserTabActivity` para permitir que o sistema operacional continue executando o aplicativo depois de concluir a autenticação:

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

