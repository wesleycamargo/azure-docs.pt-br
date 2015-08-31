<properties 
	pageTitle="Tratar conflitos de gravação do banco de dados com simultaneidade otimista (Windows Store) | Microsoft Azure" 
	description="Saiba como tratar conflitos de gravação do banco de dados no servidor e em seu aplicativo da Windows Store." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="wesmc"/>

# Tratando conflitos de gravação do banco de dados



##Visão geral

Este tutorial tem o objetivo de ajudá-lo a compreender melhor como tratar dos conflitos que ocorrem quando dois ou mais clientes gravam no mesmo registro do banco de dados em um aplicativo da Windows Store. Dois ou mais clientes podem gravar alterações no mesmo item, ao mesmo tempo, em alguns cenários. Sem uma detecção de conflitos, a última gravação substituirá qualquer atualização anterior, mesmo que isso não seja o resultado desejado. Os Serviços Moveis do Azure oferecem suporte para detectar e resolver esses conflitos. Este tópico descreve as etapas que permitem tratar com conflitos de gravação do banco de dados no servidor e em seu aplicativo.

Neste tutorial, você adicionará funcionalidade ao aplicativo de início rápido para tratar de contenções que podem ocorrer ao atualizar o banco de dados TodoItem.


##Pré-requisitos

Este tutorial requer o seguinte:

+ Microsoft Visual Studio 2013 ou posterior.
+ Este tutorial baseia-se no início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis]. 
+ [Conta do Azure]
+ Pacote NuGet 1.1.0 ou posterior dos Serviços Móveis do Azure. Para obter a versão mais recente, siga as etapas abaixo:
	1. No Visual Studio, abra o projeto e clique com o botão direito do mouse no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**. 

		![][19]

	2. Expanda **Online** e clique em **Microsoft e .NET**. Na caixa de texto Pesquisar, digite **Serviços Móveis do Azure**. Clique em **Instalar** no Pacote NuGet dos **Serviços Móveis do Azure**.

		![][20]


 

##Atualizar o aplicativo para permitir atualizações

Nesta seção, você atualizará a interface do usuário do TodoList para permitir a atualização do texto de cada item em um controle ListBox. O ListBox conterá uma CheckBox e uma TextBox para cada item da tabela de banco de dados. Você poderá atualizar o campo de texto do TodoItem. O aplicativo tratará o evento `LostFocus` da CaixaDeTexto para atualizar o item no banco de dados.


1. No Visual Studio, abra o projeto TodoList que você baixou no tutorial [Introdução aos Serviços Móveis].
2. No Gerenciador de Soluções do Visual Studio, abra MainPage.xaml e substitua a definição de `ListView` pela de `ListView` mostrada abaixo e salve a alteração.

		<ListView Name="ListItems" Margin="62,10,0,0" Grid.Row="1">
			<ListView.ItemTemplate>
				<DataTemplate>
					<StackPanel Orientation="Horizontal">
						<CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
						<TextBox x:Name="ToDoText" Height="25" Width="300" Margin="10" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
					</StackPanel>
				</DataTemplate>
			</ListView.ItemTemplate>
		</ListView>


4. No Gerenciador de Soluções do Visual Studio, abra MainPage.cs no projeto compartilhado. Adicione o manipulador de eventos a MainPage para o evento da CaixaDeTexto `LostFocus`, como mostrado abaixo.


        private async void ToDoText_LostFocus(object sender, RoutedEventArgs e)
        {
            TextBox tb = (TextBox)sender;
            TodoItem item = tb.DataContext as TodoItem;
            //let's see if the text changed
            if (item.Text != tb.Text)
            {
                item.Text = tb.Text;
                await UpdateToDoItem(item);
            }
        }

4. No MainPage.cs do projeto compartilhado, adicione a definição do método `UpdateToDoItem()` da MainPage referenciado no manipulador de eventos, conforme mostrado abaixo.

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;			
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                exception = ex;
            }			
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
            }
        }

Agora o aplicativo grava as alterações de texto em cada item do banco de dados quando a TextBox perde o foco.

##Habilitar a detecção de conflitos em seu aplicativo

Dois ou mais clientes podem gravar alterações no mesmo item, ao mesmo tempo, em alguns cenários. Sem uma detecção de conflitos, a última gravação substituirá qualquer atualização anterior, mesmo que isso não seja o resultado desejado. O [Controle de Simultaneidade Otimista] pressupõe que cada transação possa ser confirmada e, portanto, não usa nenhum recurso de bloqueio. Antes de confirmar uma transação, o controle de simultaneidade otimista verifica se nenhuma outra transação modificou os dados. Se os dados foram modificados, a transação de confirmação será revertida. Os Serviços Móveis do Azure oferecem suporte ao controle de simultaneidade otimista acompanhando as alterações em cada item usando a coluna de propriedades do sistema `__version` que é adicionada a cada tabela. Nesta seção, habilitaremos o aplicativo para detectar esses conflitos de gravação por meio da propriedade do sistema `__version`. O aplicativo será notificado por uma `MobileServicePreconditionFailedException` durante uma tentativa de atualização, se o registro tiver sido alterado desde a última consulta. Em seguida, ele poderá optar por confirmar a alteração no banco de dados ou deixar a última alteração no banco de dados intacta. Para obter mais informações sobre as propriedades do sistema para Serviços Móveis, consulte [Propriedades do sistema].

1. Abra TodoItem.cs no projeto compartilhado e atualize a definição de classe `TodoItem` com o seguinte código para incluir a propriedade do sistema `__version` \_\_version ativando o suporte para a detecção de conflitos de gravação.

		public class TodoItem
		{
			public string Id { get; set; }			
			[JsonProperty(PropertyName = "text")]
			public string Text { get; set; }			
			[JsonProperty(PropertyName = "complete")]
			public bool Complete { get; set; }			
			[JsonProperty(PropertyName = "__version")]
			public string Version { set; get; }
		}

	> [AZURE.NOTE]Ao usar tabelas sem tipo, habilite a simultaneidade otimista para adicionar o sinalizador Version a SystemProperties da tabela.
	>
	>````` 
	//Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
`````


2. Adicionando a propriedade `Version` à classe `TodoItem`, o aplicativo será notificado com uma exceção `MobileServicePreconditionFailedException` durante uma atualização caso o registro tenha sido alterado desde a última consulta. Essa exceção inclui a versão mais recente do item do servidor. No MainPage.cs para o projeto compartilhado, adicione o seguinte código para tratar da exceção no método `UpdateToDoItem()`.

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;			
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (MobileServicePreconditionFailedException<TodoItem> writeException)
            {
                exception = writeException;
            }
            catch (Exception ex)
            {
                exception = ex;
            }			
            if (exception != null)
            {
                if (exception is MobileServicePreconditionFailedException)
                {
                    //Conflict detected, the item has changed since the last query
                    //Resolve the conflict between the local and server item
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>) exception).Item);
                }
                else
                {
                    await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
                }
            }
        }


3. No MainPage.cs, adicione a definição para o método `ResolveConflict()` referenciado em `UpdateToDoItem()`. Observe que para resolver o conflito, você define a versão do item local como a versão atualizada do servidor antes de confirmar a decisão do usuário. Caso contrário, você encontrará o conflito continuamente.


        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
        {
            //Ask user to choose the resolution between versions
            MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n", 
                                                        serverItem.Text, localItem.Text), 
                                                        "CONFLICT DETECTED - Select a resolution:");
            UICommand localBtn = new UICommand("Commit Local Text");
            UICommand ServerBtn = new UICommand("Leave Server Text");
            msgDialog.Commands.Add(localBtn);
            msgDialog.Commands.Add(ServerBtn);			
            localBtn.Invoked = async (IUICommand command) =>
            {
                // To resolve the conflict, update the version of the 
                // item being committed. Otherwise, you will keep
                // catching a MobileServicePreConditionFailedException.
                localItem.Version = serverItem.Version;				
                // Updating recursively here just in case another 
                // change happened while the user was making a decision
                await UpdateToDoItem(localItem);
            };			
            ServerBtn.Invoked = async (IUICommand command) =>
            {
				RefreshTodoItems();
            };			
            await msgDialog.ShowAsync();
        }



##Testar conflitos de gravação do banco de dados no aplicativo

Nesta seção você criará um pacote de aplicativos da Windows Store para instalar o aplicativo em uma segunda máquina ou em uma máquina virtual. Em seguida, você executará o aplicativo nas duas máquinas gerando um conflito de gravação para testar o código. As duas instâncias do aplicativo tentarão atualizar a mesma propriedade `text` do item, exigindo que o usuário resolva o conflito.


1. Crie um pacote de aplicativos da Windows Store para instalação na segunda máquina ou na máquina virtual. Para fazer isso, clique em **Projeto**->**Repositório**->**Criar Pacotes de Aplicativos** no Visual Studio.

	![][0]

2. Na tela Criar seus Pacotes, clique em **Não**, uma vez que esse pacote não será carregado na Windows Store. Em seguida, clique em **Próximo**.

	![][1]

3. Na tela Selecionar e Configurar Pacotes, aceite os padrões e clique em **Criar**.

	![][10]

4. Na tela Criação de Pacote Concluída, clique no link **Local de saída** para abrir o local do pacote.

   	![][11]

5. Copie a pasta do pacote "todolist\_1.0.0.0\_AnyCPU\_Debug\_Test" na segunda máquina. Nessa máquina, abra a pasta do pacote e clique com o botão direito do mouse no script do PowerShell **Add-AppDevPackage.ps1** e clique em **Executar com o PowerShell** conforme mostrado abaixo. Siga os prompts para instalar o aplicativo.

	![][12]
  
5. Execute a instância 1 do aplicativo no Visual Studio clicando em **Depurar**->**Iniciar Depuração**. Na tela Iniciar da segunda máquina, clique na seta para baixo para ver "Aplicativos por nome". Em seguida, clique no aplicativo **todolist** para executar a instância 2 do aplicativo.

	Instância do Aplicativo 1 ![][2]

	Instância do Aplicativo 2 ![][2]


6. Na instância 1 do aplicativo, atualize o texto do último item para **Gravação de Teste 1** e, em seguida, clique em outra caixa de texto para que o manipulador de eventos `LostFocus` atualize o banco de dados. A captura de tela abaixo mostra um exemplo.
	
	Instância do Aplicativo 1 ![][3]

	Instância do Aplicativo 2 ![][2]

7. Neste ponto, o item correspondente na instância 2 do aplicativo tem uma versão antiga do item. Nessa instância do aplicativo, insira **Gravação de Teste 2** para a propriedade `text`. Em seguida, clique em outra caixa de texto para que o manipulador de eventos `LostFocus` tente atualizar o banco de dados com a propriedade `_version` antiga.

	Instância do Aplicativo 1 ![][4]

	Instância do Aplicativo 2 ![][5]

8. Uma vez que o valor `__version` usado na tentativa de atualização não correspondeu ao valor `__version` do servidor, o SDK dos Serviços Móveis lança uma `MobileServicePreconditionFailedException`, permitindo que o aplicativo resolva esse conflito. Para resolver o conflito, você pode clicar em **Confirmar texto Local** para confirmar os valores da instância 2. Como alternativa, clique em **Deixar Texto de Servidor** para descartar os valores na instância 2, deixando os valores da instância 1 do aplicativo confirmados.

	Instância do Aplicativo 1 ![][4]

	Instância do Aplicativo 2 ![][6]



##Tratanado automaticamente a resolução de conflitos em scripts de servidor

Você pode detectar e resolver conflitos de gravação em scripts de servidor. Essa é uma boa ideia quando você pode usar a lógica de scripts em vez da interação do usuário para resolver o conflito. Nesta seção, você irá adicionar um script do lado do servidor à tabela TodoItem para o aplicativo. A lógica que esse script usará para resolver conflitos é a seguinte:

+  Se o campo ` complete` do TodoItem estiver definido como true, ele será considerado concluído e `text` não poderá mais ser alterado.
+  Se o campo ` complete` do TodoItem ainda for falso, as tentativas de atualizar `text` serão confirmadas.

As etapas a seguir mostram como adicionar o script de atualização do servidor e testá-lo.

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique no seu aplicativo. 

   	![][7]

2. Clique na guia **Dados** e clique na tabela **TodoItem**.

   	![][8]

3. Clique em **Script** e selecione a operação **Atualizar**.

   	![][9]

4. Substitua o script existente pela função a seguir e clique em **Salvar**.

		function update(item, user, request) { 
			request.execute({ 
				conflict: function (serverRecord) {
					// Only committing changes if the item is not completed.
					if (serverRecord.complete === false) {
						//write the updated item to the table
						request.execute();
					}
					else
					{
						request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
					}
				}
			}); 
		}   
5. Execute o aplicativo **todolist** nas duas máquinas. Altere o TodoItem `text` para o último item na instância 2. Em seguida, clique em outra caixa de texto para que o manipulador de eventos `LostFocus` atualize o banco de dados.

	Instância do Aplicativo 1 ![][4]

	Instância do Aplicativo 2 ![][5]

6. Na instância 1 do aplicativo, insira um valor diferente para a propriedade do último texto. Em seguida, clique em outra caixa de texto para que o manipulador de eventos `LostFocus` tente atualizar o banco de dados com uma propriedade `__version` incorreta.

	Instância do Aplicativo 1 ![][13]

	Instância do Aplicativo 2 ![][14]

7. Observe que nenhuma exceção foi encontrada no aplicativo porque o script do servidor resolveu o conflito permitindo a atualização pois o item não está marcado como concluído. Para ver se a atualização foi realmente bem-sucedida, clique em **Atualizar** na instância 2 para consultar o banco de dados novamente.

	Instância do Aplicativo 1 ![][15]

	Instância do Aplicativo 2 ![][15]

8. Na instância 1, clique na caixa de seleção para concluir o último item de tarefas pendentes.

	Instância do Aplicativo 1 ![][16]

	Instância do Aplicativo 2 ![][15]

9. Na instância 2, tente atualizar o texto do TodoItem e disparar o evento `LostFocus`. Em resposta ao conflito, o script o resolveu recusando a atualização porque o item já estava concluído.

	Instância do Aplicativo 1 ![][17]

	Instância do Aplicativo 2 ![][18]

##Próximas etapas

Este tutorial demonstrou como habilitar um aplicativo da Windows Store para tratar de conflitos de gravação ao trabalhar com dados nos Serviços Móveis. Em seguida, considere a opção de concluir um dos seguintes tutoriais da Windows Store:

* [Introdução à autenticação para seu aplicativo] <br/>Saiba como autenticar usuários de seu aplicativo.

* [Adicionar notificações por push ao seu aplicativo] <br/>Saiba como enviar uma notificação por push bem básica ao seu aplicativo com os Serviços Móveis.
 


<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png
[3]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png

<!-- URLs. -->
[Controle de Simultaneidade Otimista]: http://go.microsoft.com/fwlink/?LinkId=330935
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Conta do Azure]: http://www.windowsazure.com/pricing/free-trial/
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[Introdução aos Serviços Móveis]: /develop/mobile/tutorials/get-started
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Introdução à autenticação para seu aplicativo]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Adicionar notificações por push ao seu aplicativo]: /develop/mobile/tutorials/get-started-with-push-dotnet

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=271146
[Propriedades do sistema]: http://go.microsoft.com/fwlink/?LinkId=331143
 

<!---HONumber=August15_HO8-->