<properties urlDisplayName="Validate and Modify Data" pageTitle="Usar o back-end do .NET para validar e modificar dados (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Windows Store app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Validar e modificar dados nos Serviços Móveis usando o back-end do .NET

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

Este tópico mostra como usar código em seu back-end do .NET dos Serviços Móveis do Azure para validar e modificar dados. O serviço de back-end do .Net é um serviço HTTP criado com a estrutura da API Web e o Entity Framework. Se você estiver familiarizado com a classe `ApiController` definida com a estrutura da API Web, a classe `TableController` fornecida pelos Serviços Móveis será muito intuitiva. A `TableController` é derivada da classe `ApiController` e fornece funcionalidade adicional para fazer a interface com uma tabela do banco de dados. Pode ser usada para executar operações nos dados que estão sendo inseridos e atualizados, incluindo a validação e a modificação de dados demonstradas neste tutorial. 

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Adicionar validação de comprimento de cadeia de caracteres]
2. [Atualizar o cliente para oferecer suporte à validação]
3. [Validação da duração do teste]
4. [Adicionar um campo de carimbo de data/hora para CompleteDate]
5. [Atualizar o cliente para exibir a CompleteDate]

Este tutorial se baseia nas etapas e no código de exemplo do tutorial anterior [Introdução] ou [Introdução aos dados]. Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução] ou [Introdução aos dados].  

## <a name="string-length-validation"></a>Add validation

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]


## <a name="update-client-validation"></a>Update the client

Agora que o serviço móvel está configurado para validar os dados e enviar respostas para um comprimento de texto inválido, você precisa atualizar seu aplicativo para que possa tratar respostas de erros na validação. O erro será captado como uma "MobileServiceInvalidOperationException" na chamada do aplicativo cliente para "IMobileServiceTable<TodoItem].InsertAsync()".

1. Na janela do Gerenciador de Soluções do Visual Studio, navegue para o projeto cliente e abra o arquivo MainPage.xaml.cs. Adicione as seguintes instruções **using** nesse arquivo:

        using Windows.UI.Popups;
        using Newtonsoft.Json;
        using Newtonsoft.Json.Linq;

2. No MainPage.xaml.cs, substitua o método **InsertTodoItem** pelo código a seguir:

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an Id, the item is added to the CollectionView
            MobileServiceInvalidOperationException invalidOpException = null;
            try
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
            catch(MobileServiceInvalidOperationException e)
            {
                invalidOpException = e;
            }
            if (invalidOpException != null)
            {
                string strJsonContent = await invalidOpException.Response.Content.ReadAsStringAsync();
                var responseContent = JObject.Parse(strJsonContent);
                MessageDialog errormsg = new MessageDialog(string.Format("{0} (HTTP {1})", 
                    (string)responseContent["message"],(int)invalidOpException.Response.StatusCode), 
                    invalidOpException.Message);
                var ignoreAsyncOpResult = errormsg.ShowAsync();
            }
        }

   	Essa versão do método inclui tratamento de erro para a **MobileServiceInvalidOperationException**, que exibe a mensagem de erro desserializada do conteúdo da resposta em uma caixa de diálogo de mensagem.

## <a name="test-length-validation"></a>Validação da duração do teste

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto do aplicativo cliente e clique em **Configurar como Projeto de Inicialização**. Em seguida, pressione a tecla **F5** para iniciar o aplicativo que hospeda o serviço localmente no IIS Express.

2. Digite o texto para um novo item de tarefa pendente com um tamanho maior que 10 caracteres e, em seguida, clique em **Salvar**.

    ![][1]

3. Será exibida uma caixa de diálogo de mensagem semelhante à seguinte em resposta ao texto inválido.

    ![][2]

## <a name="add-timestamp"></a>Add a timestamp field for CompleteDate

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]




## <a name="update-client-timestamp"></a>Update the client to display the CompleteDate

A etapa final é atualizar o cliente para exibir os novos dados de **CompleteDate**. 


1. No Gerenciador de Soluções do Visual Studio, no projeto cliente todolist, abra o arquivo MainPage.xaml e substitua o elemento **CheckBoxComplete** pelas definições abaixo. Em seguida, salve o arquivo. Isso altera o manipulador de eventos em **CheckBoxComplete** para que possamos manipular o evento de "clique". Também podemos adicionar um bloco de texto ao lado da caixa de seleção e vinculá-lo ao carimbo de data/hora da data de conclusão.
	      
        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
          Click="CheckBoxComplete_Clicked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
        <TextBlock Name="textCompleteDate" Text="{Binding CompleteDate}" VerticalAlignment="Center"/>


2. No Gerenciador de Soluções do Visual Studio, no projeto cliente todolist, abra o arquivo MainPage.xaml.cs e substitua o manipulador de eventos `CheckBoxComplete_Checked` pelo manipulador de eventos `CheckBoxComplete_Clicked` a seguir. Isso é para que possamos ver a data de conclusão do item.

        private void CheckBoxComplete_Clicked(object sender, RoutedEventArgs e)
        {
            CheckBox cb = (CheckBox)sender;
            TodoItem item = cb.DataContext as TodoItem;
            UpdateCheckedTodoItem(item);
        }


2. No arquivo MainPage.xaml.cs, substitua a classe **TodoItem** existente pela seguinte definição, que inclui a nova propriedade **CompleteDate** como um tipo que permite valor nulo.

        public class TodoItem
        {
            public string Id { get; set; }
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }        
            [JsonProperty(PropertyName = "CompleteDate")]
            public DateTime? CompleteDate { get; set; }
        }
	
    >[WACOM.NOTE] O atributo <code>DataMemberAttribute</code> informa ao cliente para mapear a nova propriedade <code>CompleteDate</code> no aplicativo para a coluna <code>CompleteDate</code> definida na tabela TodoItem. Usando esse atributo, seu aplicativo pode ter nomes de propriedades em objetos que são diferentes dos nomes das colunas no Banco de Dados SQL.
    

	


4. No MainPage.xaml.cs, remova ou comente a função da cláusula `.Where` no método **RefreshTodoItems** existente, para que os todoitems concluídos sejam incluídos nos resultados.

            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               //.Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();


5. No MainPage.xaml.cs, atualize o método **UpdateCheckedTodoItem** da seguinte maneira para que os itens sejam atualizados depois de uma atualização e os itens concluídos não sejam removidos da lista. Em seguida, salve o arquivo.	

        private async void UpdateCheckedTodoItem(TodoItem item)
        {
            // This code takes a freshly completed TodoItem and updates the database.
            await todoTable.UpdateAsync(item);
            //items.Remove(item);
            RefreshTodoItems();
        }


6. Nas janela do Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse na **Solução** e clique em **Recompilar Solução** para recompilar o cliente e o serviço de back-end do .NET. Verifique se os dois projetos são compilados sem erros.

    ![][3]
	
7. Pressione a tecla **F5** para executar o aplicativo cliente e o serviço localmente. Adicione alguns novos itens e clique para marcar alguns itens como concluídos, para ver o carimbo de data/hora de **CompleteDate** sendo atualizado.

    ![][4]

8. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto de serviço todolist e clique em **Publicar**. Publique o serviço de back-end do .NET no Microsoft Azure usando o arquivo de configuração da publicação que você baixou no portal do Azure.

9. Atualize o arquivo App.xaml.cs do projeto cliente removendo o comentário da conexão ao endereço do serviço móvel. Teste o aplicativo com o back-end do .NET hospedado em sua conta do Azure.




## <a name="next-steps"> </a>Próximas etapas

Agora que você concluiu este tutorial, considere continuar com o tutorial final da série de dados: [Refinar consultas com paginação].

Scripts de servidor também são usados ao autorizar usuários e para enviar notificações por push. Para obter mais informações, consulte os tutoriais a seguir:

* [Autorização de usuários do lado do serviço]
  <br/>Saiba como filtrar dados com base na ID de um usuário autenticado.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

* [Referência conceitual do tutorial do .NET de Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com .NET.

<!-- Anchors. -->
[Adicionar validação de comprimento de cadeia de caracteres]: #string-length-validation
[Atualizar o cliente para oferecer suporte à validação]: #update-client-validation
[Validação da duração do teste]: #test-length-validation
[Adicionar um campo de carimbo de data/hora para CompleteDate]: #add-timestamp
[Atualizar o cliente para exibir a CompleteDate]: #update-client-timestamp
[Próximas etapas]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-rebuild-solution.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-final-local-app-run.png



<!-- URLs. -->
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Autorização de usuários do lado do serviço]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-dotnet
[Introdução]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library
