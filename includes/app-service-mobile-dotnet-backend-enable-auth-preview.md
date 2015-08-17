A autenticação do projeto do servidor baixado do portal já está habilitada.

Em seu projeto do ASP.NET, você verá o seguinte:

* O pacote NuGet `Microsoft.Azure.Mobile.Server.Authentication` está instalado.

* Em WebApiConfig.cs, o método `UseDefaultConfiguration()` é chamado no objeto MobileAppConfiguration. Isso, por sua vez, chama o método de extensão `AddAppServiceAuthentication()` fornecido pelo pacote NuGet acima. Ele também registra um middleware OWIN necessário para autenticação chamando `app.UseAppServiceAuthentication()` durante a inicialização do OWIN.

<!---HONumber=August15_HO6-->