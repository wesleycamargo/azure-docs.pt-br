
É sempre uma boa prática validar o comprimento dos dados enviados pelos usuários. Nesta seção, você adicionará um código ao serviço móvel que valida o tamanho dos dados da cadeia de caracteres enviados para o serviço móvel e rejeita as cadeias de caracteres que são muito longas, neste caso mais de 10 caracteres.

1. Inicie o Visual Studio com a opção **Executar como administrador** e abra a solução contendo o projeto de serviço móvel com que você trabalhou no tutorial [Introdução] ou [Introdução aos dados](../articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md).

2. Na janela do Gerenciador de Soluções, expanda o projeto de serviço da lista de tarefas e expanda **Controladores**. Abra o arquivo TodoItemController.cs que faz parte do projeto de serviço móvel.

   	![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-open-todoitemcontroller.png)

3. Substitua o método `PostTodoItem` pelo método a seguir, que validará se a cadeia de texto não tem mais que 10 caracteres. Para itens que tenham um tamanho de texto maior que 10 caracteres, o método retorna um código de status HTTP Solicitação Inválida 400 com uma mensagem descritiva incluída como conteúdo.


        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            if (item.Text.Length > 10)
            {
                return BadRequest("The Item's Text length must not be greater than 10.");
            }
            else
            {
                TodoItem current = await InsertAsync(item);
                return CreatedAtRoute("Tables", new { id = current.Id }, current);
            } 
        }



4. Clique com o botão direito do mouse no projeto de serviço e clique em **Compilar** para compilar o projeto de serviço móvel. Verifique se não ocorreu nenhum erro.

   	![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-build-dotnet-service.png)

5. Clique com o botão direito do mouse no projeto do serviço e clique em **Publicar**. Publique o serviço móvel na conta do Microsoft Azure usando as configurações de publicação utilizadas anteriormente no tutorial [Introdução] ou [Introdução aos dados](../articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md).
 
     >[AZURE.NOTE]Também é possível testar usando o serviço hospedado localmente no IIS Express. Para saber mais, consulte o tutorial [Introdução aos dados](../articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md).

    ![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-publish-dotnet-service.png)





<!-- URLs. -->
[Introdução]: ../articles/mobile-services/mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

<!---HONumber=July15_HO5-->