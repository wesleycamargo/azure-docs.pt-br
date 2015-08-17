O projeto do servidor baixado do portal já possui recursos de dados habilitados.

Em seu projeto do ASP.NET, você verá o seguinte:

* Os pacotes NuGet `Microsoft.Azure.Mobile.Server.Tables` e `Microsoft.Azure.Mobile.Server.Entity` estão instalados.

* Em WebApiConfig.cs, o método `UseDefaultConfiguration()` é chamado no objeto MobileAppConfiguration. Isso, por sua vez, chama o método de extensão `AddTablesWithEntityFramework()` fornecido pelo pacote NuGet acima.

<!---HONumber=August15_HO6-->