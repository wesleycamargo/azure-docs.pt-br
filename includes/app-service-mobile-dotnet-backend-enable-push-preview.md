O projeto do servidor baixado do portal já tem uma notificação por push habilitada.

Em seu projeto ASP.NET, você verá o seguinte:

* O pacote NuGet `Microsoft.Azure.Mobile.Server.Notifications` está instalado.

* Em WebApiConfig.cs, o método `UseDefaultConfiguration()` é chamado no objeto MobileAppConfiguration. Isso, por sua vez, chama o método de extensão `AddPushNotifications()` fornecido pelo pacote NuGet acima.

<!---HONumber=August15_HO8-->