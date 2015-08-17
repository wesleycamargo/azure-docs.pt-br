O projeto do servidor baixado do portal já está com o push habilitado.

Em seu projeto do ASP.NET, você verá o seguinte:

* O pacote NuGet `Microsoft.Azure.Mobile.Server.Notifications` está instalado.

* Em WebApiConfig.cs, o método `UseDefaultConfiguration()` é chamado no objeto MobileAppConfiguration. Isso, por sua vez, chama o método de extensão `AddPushNotifications()` fornecido pelo pacote NuGet acima.

<!---HONumber=August15_HO6-->