
## <a name="create-an-application-express"></a>Criar um aplicativo (Expresso)
Agora você precisa registrar seu aplicativo no *Portal de Registro de Aplicativos da Microsoft*:
1. Registre o aplicativo por meio do [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure)
2.  Insira um nome para o aplicativo e seu email
3.  Verifique se a opção Instalação Guiada está marcada
4.  Siga as instruções para obter a ID do aplicativo e colá-lo no código

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Adicionar as informações de registro do aplicativo à sua solução (Avançado)
Agora você precisa registrar seu aplicativo no *Portal de Registro de Aplicativos da Microsoft*:
1. Acesse o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar um aplicativo
2. Insira um nome para o aplicativo e seu email 
3. Verifique se a opção Instalação Guiada está desmarcada
4. Clique em `Add Platform` e, em seguida, selecione `Native Application` e clique em Salvar
5. Copie o GUID da ID do Aplicativo, volte ao Visual Studio, abra `App.xaml.cs` e substitua `your_client_id_here` pela ID do Aplicativo que você acabou de registrar:

```csharp
private static string ClientId = "your_application_id_here";
```
