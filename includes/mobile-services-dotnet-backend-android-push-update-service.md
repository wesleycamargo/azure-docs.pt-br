
1. No Gerenciador de Soluções do Visual Studio, expanda a pasta **Controladores** no projeto do serviço móvel. Abra o TodoItemController.cs. Na parte superior do arquivo, adicione as seguintes instruções `using`:

		using System;
		using System.Collections.Generic;

2. Atualize a definição do método `PostTodoItem` com o seguinte código:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

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

    Esse código irá enviar uma notificação por push (com o texto do item inserido) depois de inserir um item de tarefa pendente. Se ocorrer um erro, o código adicionará uma entrada no log de erros que pode ser visualizada na guia **Logs** do serviço móvel no Portal de Gerenciamento.

3. Republique o projeto de serviço móvel no Azure.

<!---HONumber=July15_HO2-->