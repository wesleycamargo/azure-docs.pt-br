<properties urlDisplayName="Optimistic concurrency" pageTitle="Tratar conflitos de gravação do banco de dados com simultaneidade otimista (Windows Store) | Centro de desenvolvimento móvel" metaKeywords="" description="Saiba como tratar conflitos de gravação do banco de dados tanto no servidor como no seu aplicativo da Windows Store." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Tratando conflitos de gravação do banco de dados

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#" class="current">C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript">JavaScript da Windows Store</a>
<a href="/pt-br/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone">Windows Phone</a></div>	


Este tutorial tem o objetivo de ajudá-lo a compreender melhor como tratar conflitos que ocorrem quando dois ou mais clientes gravam no mesmo registro do banco de dados em um aplicativo da Windows Store. Dois ou mais clientes podem gravar alterações no mesmo item, ao mesmo tempo, em alguns cenários. Sem uma detecção de conflitos, a última gravação substituirá qualquer atualização anterior, mesmo que isso não seja o resultado desejado. Os Serviços Moveis do Azure oferecem suporte para detectar e resolver esses conflitos. Este tópico descreve as etapas que permitem tratar com conflitos de gravação do banco de dados no servidor e em seu aplicativo.

Neste tutorial, você adicionará funcionalidade ao aplicativo de início rápido para tratar de contenções que ocorrem ao atualizar o banco de dados TodoItem. Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Atualizar o aplicativo para permitir atualizações]
2. [Habilitar a detecção de conflitos em seu aplicativo]
3. [Testar conflitos de gravação do banco de dados no aplicativo]
4. [Tratando automaticamente a resolução de conflitos em scripts de servidor]


Este tutorial requer o seguinte:

+ Microsoft Visual Studio 2012 Express para Windows ou posterior.
+ Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis]. 
+ [Conta do Azure].
+ Pacote NuGet 1.1.0 ou posterior dos Serviços Móveis do Azure. Para obter a versão mais recente, siga as etapas abaixo:
	1. No Visual Studio, abra o projeto e clique com o botão direito do mouse no Gerenciador de Soluções e clique em **Gerenciar Pacotes do NuGet**. 

		![][19]

	2. Expanda **Online** e clique em **Microsoft e .NET**. Na caixa de texto Pesquisar, digite **Serviços Móveis do Azure**. Clique em **Instalar** no Pacote NuGet dos **Serviços Móveis do Azure**.

		![][20]


 

<h2><a name="uiupdate"></a>Atualizar o aplicativo para permitir atualizações</h2>

Nesta seção, você atualizará a interface do usuário do TodoList para permitir a atualização do texto de cada item em um controle ListBox. O ListBox conterá uma CheckBox e uma TextBox para cada item da tabela de banco de dados. Você poderá atualizar o campo de texto do TodoItem. O aplicativo manipulará o evento "LostFocus" da TextBox para atualizar o item no banco de dados.


1. No Visual Studio, abra o projeto TodoList que você baixou no tutorial [Introdução aos Serviços Móveis].
2. No Gerenciador de Soluções do Visual Studio, abra MainPage.xaml e substitua a definição do "ListView" pelo ListView mostrado abaixo e salve a alteração.

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


3. Em MainPage.xaml.cs, adicione a seguinte diretiva "using" à parte superior da página.

		using System.Threading.Tasks;


4. No Gerenciador de Soluções do Visual Studio, abra a MainPage.xaml.cs. Adicione o manipulador de eventos à MainPage para o evento "LostFocus" da TextBox, conforme mostrado abaixo.


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

4. Na MainPage.xaml.cs, adicione a definição do método "UpdateToDoItem()" da MainPage referenciado no manipulador de eventos, conforme mostrado abaixo.

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

<h2><a name="enableOC"></a>Habilitar a detecção de conflitos em seu aplicativo</h2>

Dois ou mais clientes podem gravar alterações no mesmo item, ao mesmo tempo, em alguns cenários. Sem uma detecção de conflitos, a última gravação substituirá qualquer atualização anterior, mesmo que isso não seja o resultado desejado. O [Controle de Simultaneidade Otimista] pressupõe que cada transação possa ser confirmada e, portanto, não usa nenhum recurso de bloqueio. Antes de confirmar uma transação, o controle de simultaneidade otimista verifica se nenhuma outra transação modificou os dados. Se os dados foram modificados, a transação de confirmação será revertida. Os Serviços Móveis do Azure oferecem suporte ao controle de simultaneidade otimista acompanhando as alterações em cada item usando a coluna de propriedades do sistema "__version" que é adicionada a cada tabela. Nesta seção, habilitaremos o aplicativo para detectar esses conflitos de gravação através da propriedade do sistema "__version". O aplicativo será notificado por uma "MobileServicePreconditionFailedException" durante uma tentativa de atualização, se o registro tiver sido alterado desde a última consulta. Em seguida, ele poderá optar por confirmar a alteração no banco de dados ou deixar a última alteração no banco de dados intacta. Para obter mais informações sobre as propriedades do sistema para Serviços Móveis, consulte [Propriedades do sistema].

1. Na MainPage.xaml.cs atualize a definição da classe **TodoItem** com o seguinte código para incluir a propriedade do sistema **__version** ativando o suporte para a detecção de conflitos de gravação.

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

	<div class="dev-callout"><strong>Observação</strong>
	<p>Ao usar tabelas sem tipo, habilite a simultaneidade otimista para adicionar o sinalizador Version a SystemProperties da tabela.</p>
	<pre><code>//Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
</code></pre>
	</div>


2. Adicionando a propriedade "Version" à classe "TodoItem", a aplicação será notificada com uma exceção "MobileServicePreconditionFailedException" durante uma atualização se o registro foi alterado desde a última consulta. Essa exceção inclui a versão mais recente do item do servidor. Na MainPage.xaml.cs, adicione o código a seguir para manipular a exceção no método "UpdateToDoItem()".

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


3. Na MainPage.xaml.cs, adicione a definição do método "ResolveConflict ()" referenciada na "Updatetodoitem ()". Observe que para resolver o conflito, você define a versão do item local como a versão atualizada do servidor antes de confirmar a decisão do usuário. Caso contrário, você encontrará o conflito continuamente.


        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
        {
            //Ask user to choose the resolution between versions
            MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n", 
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



<h2><a name="test-app"></a>Testar conflitos de gravação do banco de dados no aplicativo</h2>

Nesta seção você criará um pacote do aplicativo da Windows Store para instalar o aplicativo em uma segunda máquina ou em uma máquina virtual. Em seguida, você executará o aplicativo nas duas máquinas gerando um conflito de gravação para testar o código. As duas instâncias do aplicativo tentarão atualizar a mesma propriedade do item "text" exigindo que o usuário resolva o conflito.


1. Crie um pacote de aplicativos da Windows Store para instalação na segunda máquina ou na máquina virtual. Para fazer isso, clique em **Projeto**->**Repositório**->**Criar Pacotes de Aplicativos** no Visual Studio.

	![][0]

2. Na tela Criar seus Pacotes, clique em **Não**, uma vez que esse pacote não será carregado na Windows Store. Em seguida, clique em **Próximo**.

	![][1]

3. Na tela Selecionar e Configurar Pacotes, aceite os padrões e clique em **Criar**.

	![][10]

4. Na tela Criação de Pacote Concluída, clique no link **Local de saída** para abrir o local do pacote.

   	![][11]

5. Copie a pasta do pacote "todolist_1.0.0.0_AnyCPU_Debug_Test" na segunda máquina. Nessa máquina, abra a pasta do pacote e clique com o botão direito do mouse no script do PowerShell **Add-AppDevPackage.ps1** e clique em **Executar com o PowerShell** conforme mostrado abaixo. Siga os prompts para instalar o aplicativo.

	![][12]
  
5. Execute a instância 1 do aplicativo no Visual Studio clicando em **Depurar**->**Iniciar Depuração**. Na tela Iniciar da segunda máquina, clique na seta para baixo para ver "Aplicativos por nome". Em seguida, clique no aplicativo **todolist** para executar a instância 2 do aplicativo.  

	Instância 1 do aplicativo	
	![][2]

	Instância 2 do aplicativo	
	![][2]


6. Na instância 1 do aplicativo, atualize o texto do último item para **Test Write 1**, depois clique em outra caixa de texto para que o manipulador de eventos "LostFocus" atualize o banco de dados. A captura de tela abaixo mostra um exemplo.
	
	Instância 1 do aplicativo	
	![][3]

	Instância 2 do aplicativo	
	![][2]

7. Neste ponto, o item correspondente na instância 2 do aplicativo tem uma versão antiga do item. Nessa instância do aplicativo, digite **Test Write 2** para a propriedade "text". Em seguida, clique em outra caixa de texto para que o manipulador de eventos "LostFocus" tente atualizar o banco de dados com a propriedade "_version" antiga.

	Instância 1 do aplicativo	
	![][4]

	Instância 2 do aplicativo	
	![][5]

8. Como o valor de "__version" usado com a tentativa de atualização não corresponde ao valor "__version" do servidor, o SDK dos Serviços Móveis lança uma "MobileServicePreconditionFailedException" permitindo que o aplicativo resolva esse conflito. Para resolver o conflito, você pode clicar em **Confirmar texto Local** para confirmar os valores da instância 2. Como alternativa, clique em **Deixar texto do servidor** para descartar os valores na instância 2, deixando os valores da instância 1 do aplicativo confirmados.  

	Instância 1 do aplicativo	
	![][4]

	Instância 2 do aplicativo	
	![][6]



<h2><a name="scriptsexample"></a>Tratando automaticamente a resolução de conflitos em scripts de servidor</h2>

Você pode detectar e resolver conflitos de gravação em scripts de servidor. Essa é uma boa ideia quando você pode usar a lógica de scripts em vez da interação do usuário para resolver o conflito. Nesta seção, você irá adicionar um script do lado do servidor à tabela TodoItem para o aplicativo. A lógica que esse script usará para resolver conflitos é a seguinte:

+  Se o campo "complete" do TodoItem estiver definido como verdadeiro, ele será considerado concluído e "text" não poderá mais ser alterado.
+  Se o campo "complete" do TodoItem ainda for falso, as tentativas de atualização de "text" serão confirmadas.

As etapas a seguir mostram como adicionar o script de atualização do servidor e testá-lo.

1. Faça o logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique no seu aplicativo. 

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
5. Execute o aplicativo **todolist** nas duas máquinas. Altere o TodoItem "text" para o último item na instância 2. Em seguida, clique em outra caixa de texto para que o manipulador de eventos "LostFocus" atualize o banco de dados.

	Instância 1 do aplicativo	
	![][4]

	Instância 2 do aplicativo	
	![][5]

6. Na instância 1 do aplicativo, insira um valor diferente para a propriedade do último texto. Em seguida, clique em outra caixa de texto para que o manipulador de eventos "LostFocus" tente atualizar o banco de dados com uma propriedade "__ version" incorreta.

	Instância 1 do aplicativo	
	![][13]

	Instância 2 do aplicativo	
	![][14]

7. Observe que nenhuma exceção foi encontrada no aplicativo porque o script do servidor resolveu o conflito permitindo a atualização pois o item não está marcado como concluído. Para ver se a atualização foi realmente bem-sucedida, clique em **Atualizar** na instância 2 para consultar o banco de dados novamente.

	Instância 1 do aplicativo	
	![][15]

	Instância 2 do aplicativo	
	![][15]

8. Na instância 1, clique na caixa de seleção para concluir o último item de tarefas pendentes.

	Instância 1 do aplicativo	
	![][16]

	Instância 2 do aplicativo	
	![][15]

9. Na instância 2, tente atualizar o texto do TodoItem e disparar o evento "LostFocus". Em resposta ao conflito, o script o resolveu recusando a atualização porque o item já estava concluído.  

	Instância 1 do aplicativo	
	![][17]

	Instância 2 do aplicativo	
	![][18]

## <a name="next-steps"> </a>Next steps

Este tutorial demonstrou como habilitar um aplicativo da Windows Store para tratar de conflitos de gravação ao trabalhar com dados nos Serviços Móveis. Em seguida, considere concluir um dos seguintes tutoriais da nossa série de dados:

* [Validar e modificar dados com scripts]
  <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

* [Refinar consultas com paginação]
  <br/>Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, você também pode tentar um dos seguintes tutoriais da Windows Store:

* [Introdução à autenticação] 
  <br/>Saiba como autenticar usuários de seu aplicativo.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo com os Serviços Móveis.
 
<!-- Anchors. -->
[Atualizar o aplicativo para permitir atualizações]: #uiupdate
[Habilitar a detecção de conflitos em seu aplicativo]: #enableOC
[Testar conflitos de gravação do banco de dados no aplicativo]: #test-app
[Tratando automaticamente a resolução de conflitos em scripts de servidor]: #scriptsexample
[Próximas etapas]: #next-steps

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
[Controle de simultaneidade otimista]: http://go.microsoft.com/fwlink/?LinkId=330935
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Conta do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/
[Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-dotnet
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[SDK do Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Site de Exemplos de Código do Desenvolvedor]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[Propriedades do Sistema]: http://go.microsoft.com/fwlink/?LinkId=331143

<!--HONumber=35.2-->
