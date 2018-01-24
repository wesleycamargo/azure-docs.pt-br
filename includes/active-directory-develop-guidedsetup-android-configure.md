
## <a name="register-your-application"></a>Registre seu aplicativo
Você pode registrar seu aplicativo de duas maneiras, conforme descrito nas duas seções a seguir.

### <a name="option-1-express-mode"></a>Opção 1: modo Expresso
Registre rapidamente seu aplicativo fazendo o seguinte:
1. Vá até o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2.  No campo **Nome do Aplicativo**, insira um nome para o seu aplicativo.

3. Marque a caixa de seleção **Instalação Interativa** e, depois, selecione **Criar**.

4. Siga as instruções para obter a ID do aplicativo e cole-a em seu código.

### <a name="option-2-advanced-mode"></a>Opção 2: modo Avançado
Para registrar seu aplicativo e adicionar suas informações de registro de aplicativo à sua solução, faça o seguinte:
1. Caso ainda não tenha registrado seu aplicativo, acesse o [Portal de Registro de Aplicativo da Microsoft](https://apps.dev.microsoft.com/portal/register-app).
2. No campo **Nome do Aplicativo**, insira um nome para o seu aplicativo. 

3. Desmarque a caixa de seleção **Instalação Interativa** e, depois, selecione **Criar**.

4. Selecione **Adicionar Plataforma**, selecione **Aplicativo Nativo** e, em seguida, selecione **Salvar**.

5. Em **aplicativo** > **java** > **{host}.{namespace}**, abra `MainActivity`. 

6.  Substitua *[Inserir a ID do aplicativo aqui]* na linha seguinte pela ID do aplicativo que você acabou de registrar:

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. Em **aplicativo** > **manifestos**, abra o arquivo *AndroidManifest.xml*.

8. No nó `manifest\application`, adicione a atividade a seguir. Ao fazer isso você registra uma atividade `BrowserTabActivity` que permite que o sistema operacional retome o seu aplicativo depois de concluir a autenticação:

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
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
9. No nó `BrowserTabActivity`, substitua `[Enter the application Id here]` pela ID do aplicativo.
