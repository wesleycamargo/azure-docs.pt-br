1. No **Gerenciador de Soluções** do Visual Studio, expanda a pasta **Controladores** no projeto do back-end móvel. Abra **TodoItemController.cs**. Na parte superior do arquivo, adicione as seguintes instruções `using`:

        using Microsoft.Azure.Mobile.Server.Notifications;


2. Substitua o método `PostTodoItem` pelo seguinte código:
        
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            HttpConfiguration config = this.Configuration;

            TemplatePushMessage message = new TemplatePushMessage();
            message.Add("message", item.Text);

            try
            {
                var client = new PushClient(config);
                var result = await client.SendAsync(message);

                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                config.Services.GetTraceWriter().Error(ex.Message, null, "Push.SendAsync Error");
            }

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

<!---HONumber=August15_HO8-->