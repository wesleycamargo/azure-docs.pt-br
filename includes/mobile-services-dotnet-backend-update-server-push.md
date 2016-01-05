
1. No Gerenciador de Soluções do Visual Studio, expanda a pasta **Controladores** no projeto do serviço móvel. Abra TodoItemController.cs e atualize a definição do método `PostTodoItem` com o seguinte código:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // Create a WNS native toast.
            WindowsPushMessage message = new WindowsPushMessage();

            // Define the XML paylod for a WNS native toast notification 
			// that contains the text of the inserted item.
            message.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                                 @"<toast><visual><binding template=""ToastText01"">" +
                                 @"<text id=""1"">" + item.Text + @"</text>" +
                                 @"</binding></visual></toast>";
            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Esse código envia uma notificação por push (com o texto do item inserido) depois de inserir um item de tarefa pendente. Se ocorrer um erro, o código adicionará uma entrada de log de erros que pode ser exibida na guia **Logs** do serviço móvel no [portal clássico do Azure](https://manage.windowsazure.com/).

	>[AZURE.NOTE]Você pode usar notificações de modelo para enviar uma única notificação por push para os clientes em várias múltiplas. Para obter mais informações, consulte [Suporte a plataformas de múltiplos dispositivos por meio de um único serviço móvel](../articles/mobile-services-how-to-use-multiple-clients-single-service.md#push).

2. Republique o projeto de serviço móvel no Azure.

<!---HONumber=AcomDC_1203_2015-->