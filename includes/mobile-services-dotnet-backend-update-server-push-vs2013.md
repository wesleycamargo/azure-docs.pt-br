Estas etapas criam um novo [ApiController](http://go.microsoft.com/fwlink/p/?LinkId=512673) personalizado, que envia notificações por push ao aplicativo. Você pode implementar o mesmo código em um [TableController](http://msdn.microsoft.com/library/azure/dn643359.aspx) ou em qualquer lugar nos seus serviços de back-end.

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse na pasta Controladores do projeto de serviço móvel, expanda **Adicionar** e clique em **Novo Item Scaffold**.

	Isso exibe a caixa de diálogo Adicionar Scaffold.

2. Expanda **Serviços Móveis do Azure** e clique em **Controlador Personalizado de Serviços Móveis do Azure**, em **Adicionar**, forneça um **Nome do controlador** de `NotifyAllUsersController` e clique em **Adicionar** novamente.

	![Caixa de diálogo API da Web adiciona Scaffold.](./media/mobile-services-dotnet-backend-update-server-push-vs2013/add-custom-api-controller.png)

	Isso cria uma nova classe de controlador vazia chamada **NotifyAllUsersController**.

3. No novo arquivo de projeto NotifyAllUsersController.cs, adicione as seguintes instruções **usando**:

        using Newtonsoft.Json.Linq;
        using System.Threading.Tasks;

4. Adicione o seguinte código que implementa o método POST no controlador:

        public async Task<bool> Post(JObject data)
        {
            try
            {
                // Define the XML paylod for a WNS native toast notification 
				// that contains the value supplied in the POST request.
                string wnsToast = 
                    string.Format("<?xml version="1.0" encoding="utf-8"?>" +
                    "<toast><visual><binding template="ToastText01">" + 
                    "<text id="1">{0}</text></binding></visual></toast>", 
                    data.GetValue("toast").Value<string>());

                // Define the WNS native toast with the payload string.
                WindowsPushMessage message = new WindowsPushMessage();
                message.XmlPayload = wnsToast;

                // Send the toast notification.
                await Services.Push.SendAsync(message);
                return true;
            }
            catch (Exception e)
            {
                Services.Log.Error(e.ToString());
            }
            return false;
        }

	>[AZURE.NOTE]Este método POST pode ser chamado por qualquer cliente que tenha a chave do aplicativo, que não é segura. Para proteger o ponto de extremidade, aplique o atributo `[AuthorizeLevel(AuthorizationLevel.User)]` ao método ou classe para exigir a autenticação.

<!---HONumber=Oct15_HO3-->