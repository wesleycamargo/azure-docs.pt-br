
## <a name="create-an-application-express"></a>Criar um aplicativo (Expresso)
Agora você precisa registrar seu aplicativo no *Portal de Registro de Aplicativos da Microsoft*:
1. Registre o aplicativo por meio do [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)
2.  Insira um nome para o aplicativo e seu email
3.  Verifique se a opção Instalação Guiada está marcada
4.  Siga as instruções para adicionar uma URL de Redirecionamento ao aplicativo

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Adicionar as informações de registro do aplicativo à sua solução (Avançado)
Agora você precisa registrar seu aplicativo no *Portal de Registro de Aplicativos da Microsoft*:
1. Acesse o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar um aplicativo
2. Insira um nome para o aplicativo e seu email 
3.  Verifique se a opção Instalação Guiada está desmarcada
4.  Clique em `Add Platform` e selecione `Web`
5.  Volte ao Visual Studio e, no Gerenciador de Soluções, selecione o projeto e examine a janela Propriedades (se uma janela Propriedades não for exibida, pressione F4)
6.  Altere SSL habilitado para `True`
7.  Copie a URL do SSL e adicione essa URL à lista de URLs de Redirecionamento na lista de URLs de Redirecionamento do Portal de Registro:<br/><br/>![Propriedades do projeto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
8.  Adicione o seguinte em `web.config` localizado na pasta raiz, na seção `configuration\appSettings`:

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
<!-- Workaround for Docs conversion bug -->
<ol start="9">
<li>
Substitua `ClientId` pela ID do Aplicativo que você acabou de registrar
</li>
<li>
Substitua `redirectUri` pela URL do SSL do projeto
</li>
</ol>
<!-- End Docs -->
