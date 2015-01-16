<properties urlDisplayName="Optimistic concurrency" pageTitle="Tratar conflitos de gravação do banco de dados com simultaneidade otimista (Windows Store) | Centro de desenvolvimento móvel" metaKeywords="" description="Saiba como tratar conflitos de gravação do banco de dados no servidor e em seu aplicativo da Windows Store." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conlicts" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Tratando conflitos de gravação do banco de dados

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#">C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript">JavaScript da Windows Store</a>
<a href="/pt-br/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>

O objetivo deste tutorial é ajudar a compreender melhor como tratar conflitos que ocorrem quando dois ou mais clientes gravam no mesmo registro do banco de dados em um aplicativo do Windows Phone 8. Dois ou mais clientes podem gravar alterações no mesmo item, ao mesmo tempo, em alguns cenários. Sem uma detecção de conflitos, a última gravação substituirá qualquer atualização anterior, mesmo que isso não seja o resultado desejado. Os Serviços Móveis do Azure oferecem suporte para detectar e resolver esses conflitos. Este tópico descreve as etapas que permitem tratar conflitos de gravação do banco de dados no servidor e em seu aplicativo.

Neste tutorial, você adicionará funcionalidade ao aplicativo de início rápido para tratar contenções que ocorrem ao atualizar o banco de dados TodoItem. Este tutorial explica as seguintes etapas básicas:

1. [Atualizar o aplicativo para permitir atualizações]
2. [Habilitar a detecção de conflitos em seu aplicativo]
3. [Testar conflitos de gravação do banco de dados no aplicativo]
4. [Tratanado automaticamente a resolução de conflitos em scripts de servidor]


Este tutorial requer o seguinte:

+ Microsoft Visual Studio 2012 Express para Windows Phone 8 ou posterior.
+ [SDK do Windows Phone 8] em execução no Windows 8. 
+ [Conta do Azure]
+ Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis].
+ Pacote NuGet 1.1.0 ou posterior dos Serviços Móveis do Azure. Para obter a versão mais recente, siga as etapas abaixo:
	1. No Visual Studio, abra o projeto e clique com o botão direito do mouse no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**. 

		![][13]

	2. Expanda **Online** e clique em **Microsoft e .NET**. Na caixa de texto Pesquisar, digite **Serviços Móveis do Azure**. Clique em **Instalar** no Pacote NuGet dos **Serviços Móveis do Azure**.

		![][14]


 

<h2><a name="uiupdate"></a>Atualizar o aplicativo para permitir atualizações</h2>

Nesta seção, você atualizará a interface do usuário de TodoList para permitir a atualização do texto de cada item em um controle ListBox. O ListBox conterá uma CheckBox e uma TextBox para cada item da tabela de banco de dados. Você poderá atualizar o campo de texto do TodoItem. O aplicativo tratará o evento LostFocus da TextBox para atualizar o item no banco de dados.


1. No Visual Studio, abra o projeto TodoList que você baixou no tutorial [Introdução aos Serviços Móveis].
2. No Gerenciador de Soluções do Visual Studio, abra MainPage.xaml e substitua a definição de `phone:LongListSelector` pela ListBox mostrada abaixo e salve a alteração.

		<ListBox Grid.Row="4" Grid.ColumnSpan="2" Name="ListItems">
			<ListBox.ItemTemplate>
				<DataTemplate>
					<StackPanel Orientation="Horizontal">
						<CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
						<TextBox x:Name="ToDoText" Width="330" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
					</StackPanel>
				</DataTemplate>
			</ListBox.ItemTemplate>
		</ListBox>


2. No Gerenciador de Soluções do Visual Studio, abra o arquivo MainPage.xaml.cs e adicione ou remova o seguinte `usando a diretiva`.

		using System.Threading.Tasks;


3. No Gerenciador de Soluções do Visual Studio, abra MainPage.xaml.cs. Adicione o manipulador de eventos à MainPage para o evento `LostFocus` de TextBox como mostrado abaixo.


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

4. In MainPage.xaml.cs, add the definition for the MainPage `UpdateToDoItem()` method referenced in the event handler as shown below.

        private async Task UpdateToDoItem(TodoItem item)
        {
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Update Failed", MessageBoxButton.OK);
            }
        }

Agora o aplicativo grava as alterações de texto em cada item do banco de dados quando a TextBox perde o foco.

<h2><a name="enableOC"></a>Habilitar a detecção de conflitos em seu aplicativo</h2>

Dois ou mais clientes podem gravar alterações no mesmo item, ao mesmo tempo, em alguns cenários. Sem uma detecção de conflitos, a última gravação substituirá qualquer atualização anterior, mesmo que isso não seja o resultado desejado. O [Controle de Simultaneidade Otimista] pressupõe que cada transação possa ser confirmada e, portanto, não usa nenhum recurso de bloqueio. Antes de confirmar uma transação, o controle de simultaneidade otimista verifica se nenhuma outra transação modificou os dados. Se os dados foram modificados, a transação de confirmação será revertida. Os Serviços Móveis do Azure oferecem suporte ao controle de simultaneidade otimista acompanhando as alterações em cada item usando a coluna de propriedades do sistema `__version` que é adicionada a cada tabela. Nesta seção, habilitaremos o aplicativo para detectar esses conflitos através da propriedade do sistema `__version`. O aplicativo será notificado por uma `MobileServicePreconditionFailedException` durante uma tentativa de atualização, se o registro tover sido alterado desde a última consulta. Em seguida, ele poderá optar por confirmar a alteração no banco de dados ou deixar a última alteração no banco de dados intacta. Para obter mais informações sobre as propriedades do sistema para Serviços Móveis, consulte [Propriedades do sistema].

1. Em MainPage.xaml.cs atualize a definição da classe **TodoItem** com o código a seguir para incluir a propriedade do sistema **__version** habilitando o suporte para detecção de conflitos de gravação:

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
	<p>Ao usar tabelas não tipadas, habilite a simultaneidade otimista para adicionar o sinalizador Version a SystemProperties da tabela.</p>
	<pre><code>//Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties | = MobileServiceSystemProperties.Version;
</code></pre>
	</div>


2. Adicionando a propriedade `Version` à classe `TodoItem`, o aplicativo será notificado com uma exceção `MobileServicePreconditionFailedException` durante uma atualização, se o registro tiver sido alterado desde a última consulta. Essa exceção inclui a versão mais recente do item do servidor. Em MainPage.xaml.cs, adicione o seguinte código para tratar da exceção no método`UpdateToDoItem()`.

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
                if (exception is MobileServicePreconditionFailedException<TodoItem>)
                {
                    //conflict detected, the item has changed since the last query
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>)exception).Item);
                }
                else
                    MessageBox.Show(exception.Message, "Update Failed", MessageBoxButton.OK);
            }
        }


3. Em MainPage.xaml.cs, adicione a definição para o método `ResolveConflict()` referenciado em `UpdateToDoItem()`. Observe que para resolver o conflito, você define a versão do item local como a versão atualizada do servidor antes de confirmar a decisão do usuário. Caso contrário, você encontrará o conflito continuamente.


        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)		
        {
            // Ask user to choose between the local text value or leaving the 
			// server's updated text value
            MessageBoxResult mbRes = MessageBox.Show(String.Format("The item has already been updated on the server.\n\n" +
                                                                   "Server value: {0} \n" +
                                                                   "Local value: {1}\n\n" +
                                                                   "Press OK to update the server with the local value.\n\n" +
                                                                   "Press CANCEL to keep the server value.", serverItem.Text, localItem.Text), 
                                                                   "CONFLICT DETECTED ", MessageBoxButton.OKCancel);
            // OK : After examining the updated text from the server, overwrite it
            //      with the changes made in this client.
            if (mbRes == MessageBoxResult.OK)
            {
                // Update the version of the item to the latest version
                // to resolve the conflict. Otherwise the exception
                // will be thrown again for the attempted update.
                localItem.Version = serverItem.Version;
                // Recursively updating just in case another conflict 
				// occurs while the user is deciding.
                await this.UpdateToDoItem(localItem);
            }
            // CANCEL : After examining the updated text from the server, leave 
			// the server item intact and refresh this client's query discarding 
			// the proposed changes.
            if (mbRes == MessageBoxResult.Cancel)
            {
                RefreshTodoItems();
            }
        }



<h2><a name="test-app"></a>Testar conflitos de gravação do banco de dados no aplicativo</h2>

Nesta seção, você testará o código que trata conflitos de gravação, executando o aplicativo em dois diferentes emuladores do Windows Phone 8 (WVGA e WVGA de 512 M). Os dois aplicativos clientes tentarão atualizar a propriedade `text` do mesmo item exigindo que o usuário resolva o conflito.


1. No Visual Studio, verifique se o **Emulador WVGA 512 MB** está selecionado na lista suspensa como o destino da implantação, conforme mostrado na captura de tela abaixo.

	![][0]

2. No Visual Studio, no menu, clique em **COMPILAÇÃO** em **Implantar Solução**. Se o emulador não estava sendo executado anteriormente, levará alguns minutos para que o emulador carregue o sistema operacional Windows Phone 8. Verifique na janela de saída, na parte inferior, se a compilação e a implantação no emulador do Windows Phone 8 foram bem-sucedidas.

	![][2]

3. No Visual Studio, altere a caixa suspensa de destino da implantação para **Emulador WVGA**.

	![][1]

4. No Visual Studio, no menu, clique em **COMPILAÇÃO** em **Implantar Solução**. Verifique na janela de saída, na parte inferior, se a compilação e a implantação no emulador do Windows Phone 8 foram bem-sucedidas.

   	![][2]
  
5. Coloque os dois emuladores em execução lado a lado. É possível simular conflitos de gravação simultânea entre os aplicativos cliente com a execução nesses emuladores. Passe o dedo da direita para a esquerda nos dois emuladores para exibir a lista de aplicativos instalados. Role até a parte inferior de cada lista e clique no aplicativo **todolist**.

	![][3]

6. No emulador da esquerda, atualize o `text` do último TodoItem para **Test Write 1**, em seguida, clique em outra caixa de texto para que o manipulador de eventos `LostFocus` atualize o banco de dados. A captura de tela abaixo mostra um exemplo. 

	![][4]

7. Nesse momento, o item correspondente no emulador da direita terá uma versão antiga e o valor do texto antigo. No emulador da direita, digite **Test Write 2** para a propriedade text. Em seguida, clique em outra caixa de texto para que o manipulador de eventos `LostFocus` no emulador da direita tente atualizar o banco de dados com a versão antiga.

	![][5]

8. Como a versão usada na tentativa de atualização não corresponde à versão do servidor, o SDK dos Serviços Móveis gera a `MobileServicePreconditionFailedException` permitindo que o aplicativo resolva esse conflito. Para resolver o conflito, você pode clicar em **ok** para confirmar os valores no aplicativo certo. Como alternativa, clique em **Cancelar** para descartar os valores no aplicativo da direita, deixando os valores do aplicativo da esquerda confirmados. 

	![][6]



<h2><a name="scriptsexample"></a>Tratanado automaticamente a resolução de conflitos em scripts de servidor</h2>

Você pode detectar e resolver conflitos de gravação em scripts de servidor. Essa é uma boa ideia quando você pode usar a lógica de scripts em vez da interação do usuário para resolver o conflito. Nesta seção, você irá adicionar um script do lado do servidor à tabela TodoItem para o aplicativo. A lógica que esse script usará para resolver conflitos é a seguinte:

+  Se o campo `complete` de TodoItem estiver definido como true, ele será considerado concluído e`text` não poderá mais ser alterado.
+  Se o campo`complete` do TodoItem ainda for falso, as tentativas de atualizar `text` serão confirmadas.

As etapas a seguir mostram como adicionar o script de atualização do servidor e testá-lo.

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo. 

   	![][7]

2. Clique na guia **Dados** e na tabela **TodoItem**.

   	![][8]

3. Clique em **Script**, e selecione a operação **Atualizar**.

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
5. Altere o texto de TodoItem para o último item no aplicativo no emulador da esquerda. Em seguida, clique em outra caixa de texto para que o manipulador de eventos `LostFocus` atualize o banco de dados.

	![][4]

6. No emulador da direita, insira um valor diferente para a propriedade text do último TodoItem. Em seguida, clique em outra caixa de texto para que o manipulador de eventos `LostFocus`no emulador da direita tente atualizar o banco de dados com a versão antiga

	![][5]

7. Observe que nenhuma exceção foi encontrada no aplicativo porque o script do servidor resolveu o conflito permitindo a atualização uma vez que o item não está marcado como concluído. Para ver se a atualização foi realmente bem-sucedida, clique em **Atualizar** no aplicativo do emulador da esquerda para consultar o banco de dados novamente.

	![][10]

8. No aplicativo no emulador da esquerda, clique na caixa de seleção para concluir o último TodoItem.

	![][11]

9. No aplicativo do emulador da direita, tente atualizar o texto do mesmo TodoItem e disparar o evento `LostFocus`. Em resposta ao conflito, o script o resolveu recusando a atualização porque o item já estava concluído. 

	![][12]


## <a name="next-steps"> </a>Próximas etapas

	Este tutorial demonstrou como habilitar um aplicativo do Windows Phone 8 para tratar conflitos de gravação ao trabalhar com dados nos Serviços Móveis. Em seguida, considere concluir um dos seguintes tutoriais da nossa série de dados:

* [Validar e modificar dados com scripts]
  <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar dados enviados do seu aplicativo.

* [Refinar consultas com paginação]
  <br/>Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, você também pode tentar um dos seguintes tutoriais do Windows Phone 8:

* [Introdução à autenticação] 
  <br/>Saiba como autenticar usuários de seu aplicativo.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo com os Serviços Móveis.
 
<!-- Anchors. -->
[Atualizar o aplicativo para permitir atualizações]: #uiupdate
[Habilitar a detecção de conflitos em seu aplicativo]: #enableOC
[Testar conflitos de gravação do banco de dados no aplicativo]: #test-app
[Tratanado automaticamente a resolução de conflitos em scripts de servidor]: #scriptsexample
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA512MB.png
[1]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA.png
[2]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-build-deploy-wp8.png
[3]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-start-apps-oc-wp8.png
[4]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write1-wp8.png
[5]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write2-wp8.png
[6]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-exception-wp8.png
[7]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-insync-wp8.png
[11]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-complete-checkbox-wp8.png
[12]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-already-completed-wp8.png
[13]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[14]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png


<!-- URLs. -->
[Controle de simultaneidade otimista]: http://go.microsoft.com/fwlink/?LinkId=330935
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Conta do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/
[Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-wp8
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-wp8
[Introdução aos dados]: ./mobile-services-get-started-with-data-wp8.md
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-wp8
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-wp8

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[SDK do Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Site de Exemplos de Código do Desenvolvedor]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[Propriedades do Sistema]: http://go.microsoft.com/fwlink/?LinkId=331143
