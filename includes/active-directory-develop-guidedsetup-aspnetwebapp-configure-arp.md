
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Configurar o aplicativo Web ASP.NET com informações de registro do aplicativo

Nesta etapa, você configurará seu projeto para usar o SSL e, em seguida, usará a URL do SSL para configurar as informações de registro do aplicativo. Depois disso, adicione as informações de registro do aplicativo à sua solução por meio do *web.config*.

1.  No Gerenciador de Soluções, selecione o projeto e examine a janela `Properties` (se uma janela Propriedades não for exibida, pressione F4)
2.  Altere `SSL Enabled` para `True`
3.  Copie o valor de `SSL URL` acima e cole-o no campo `Redirect URL` na parte superior desta página e, depois, clique em *Atualizar*:<br/><br/>![Propriedades do projeto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4.  Adicione o seguinte ao arquivo `web.config` localizado na pasta da raiz, na seção `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
