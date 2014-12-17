<properties urlDisplayName="Optimistic concurrency" pageTitle="Tratar conflitos de gravação do banco de dados com simultaneidade otimista (Windows Store)| Centro de desenvolvimento móvel " metaKeywords="" writer="wesmc" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Tratando conflitos de gravação do banco de dados

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#">C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript" class="current">JavaScript da Windows Store</a>
<a href="/pt-br/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone">Windows Phone</a></div>	

Este tutorial tem o objetivo de ajudá-lo a compreender melhor como tratar conflitos que ocorrem quando dois ou mais clientes gravam no mesmo registro do banco de dados em um aplicativo da Windows Store. Dois ou mais clientes podem gravar alterações no mesmo item, ao mesmo tempo, em alguns cenários. Sem uma detecção de conflitos, a última gravação substituirá qualquer atualização anterior, mesmo que isso não seja o resultado desejado. Os Serviços Moveis do Azure oferecem suporte para detectar e resolver esses conflitos. Este tópico descreve as etapas que permitem tratar conflitos de gravação do banco de dados no servidor e em seu aplicativo.

Neste tutorial, você adicionará funcionalidade ao aplicativo de início rápido para tratar contenções que ocorrem ao atualizar o banco de dados TodoItem. Este tutorial explica as seguintes etapas básicas:

1. [Atualizar o aplicativo para permitir atualizações]
2. [Habilitar a detecção de conflitos em seu aplicativo]
3. [Testar conflitos de gravação do banco de dados no aplicativo]
4. [Tratanado automaticamente a resolução de conflitos em scripts de servidor]


Este tutorial requer o seguinte:

+ Microsoft Visual Studio 2013 Express para Windows ou posterior.
+ Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis] baixando a versão da linguagem JavaScript do projeto inicial. 
+ [Conta do Azure]
+ Pacote NuGet 1.1.5 ou posterior dos Serviços Móveis do Windows Azure. Para obter a versão mais recente, siga as etapas abaixo:
	1. No Visual Studio, abra o projeto e clique com o botão direito do mouse no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**. 


	2. Expanda **Online** e clique em **Microsoft e .NET**. Na caixa de texto Pesquisar, digite **WindowsAzure.MobileServices.WinJS**. Clique em **Instalar** no **Microsoft Azure Mobile Services for WinJS** Pacote NuGet.

		![][20]


 

<h2><a name="uiupdate"></a>Atualizar o aplicativo para permitir atualizações</h2>

Nesta seção, você atualizará a interface do usuário para permitir a atualização do texto de cada item. O modelo de ligação conterá um controle de caixa de seleção e de classe de texto para cada item na tabela do banco de dados. Você poderá atualizar o campo de texto do TodoItem. O aplicativo tratará o evento `keydown` para que o item seja atualizado pressionando a tecla **Enter**.


1. No Visual Studio, abra a versão da linguagem JavaScript do projeto TodoList que você baixou no tutorial [Introdução aos Serviços Móveis].
2. No Gerenciador de Soluções do Visual Studio, abra default.html e substitua a definição da marca div de `TemplateItem` pela marca div mostrada abaixo e salve a alteração. Isso adiciona um controle de caixa de texto para permitir que você edite o texto de um TodoItem.

        <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
          <div style="display: -ms-grid; -ms-grid-columns: auto 1fr">
            <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; dataContext: this" />
              <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px">
                <input id="modifytextbox" class="text win-interactive" data-win-bind="value: text; dataContext: this" />
              </div>
          </div>
        </div>


3. No Gerenciador de Soluções do Visual Studio, expanda a pasta **js**. Abra o arquivo default.js e substitua a função `updateTodoItem` pela definição a seguir que não removerá itens atualizados da interface do usuário.

        var updateTodoItem = function (todoItem) {
          // This code takes a freshly completed TodoItem and updates the database. 
          todoTable.update(todoItem);
          };


4. No arquivo default.js, adicione o seguinte manipulador de eventos ao evento `keydown` para que o item seja atualizado pressionando a tecla **Enter**.

        listItems.onkeydown = function (eventArgs) {
          if (eventArgs.key == "Enter") {
            var todoItem = eventArgs.target.dataContext.backingData;
            todoItem.text = eventArgs.target.value;
            updateTodoItem(todoItem);
            }
          };

O aplicativo agora grava as alterações de texto em cada item de volta no banco de dados quando a tecla **Enter** é pressionada.

<h2><a name="enableOC"></a>Habilitar a detecção de conflitos em seu aplicativo</h2>

Os Serviços Móveis do Azure oferecem suporte ao controle de simultaneidade otimista acompanhando as alterações em cada item usando a coluna de propriedades do sistema `__version` que é adicionada a cada tabela. Nesta seção, habilitaremos o aplicativo para detectar esses conflitos através da propriedade do sistema `__version`. Quando essa propriedade do sistema é habilitada na todoTable, o aplicativo será notificado por uma `MobileServicePreconditionFailedException` durante uma tentativa de atualização se o registro tiver sido alterado desde a última consulta. O aplicativo poderá então optar por confirmar a alteração no banco de dados ou deixar a última alteração no banco de dados intacta. Para obter mais informações sobre as propriedades do sistema para Serviços Móveis, consulte [Propriedades do sistema].

1. No arquivo default.js, sob a declaração da variável `todoTable` adicione o código para incluir a propriedade do sistema **__version** habilitando suporte para a detecção de conflitos de gravação.

        var todoTable = client.getTable('TodoItem');
        todoTable.systemProperties |= WindowsAzure.MobileServiceTable.SystemProperties.Version;


2. Adicionando a propriedade do sistema `Version` às propriedades do sistema de tabelas, o aplicativo será notificado com uma exceção `MobileServicePreconditionFailedException` durante uma atualização se o registro tiver sido alterado desde a última consulta. Essa exceção será capturada no JavaScript com uma função de erro. O erro inclui a versão mais recente do item do servidor que será usada para resolver conflitos. No arquivo default.js, atualize a função `updateTodoItem` para capturar o erro e chamar uma função `resolveDatabaseConflict`.

        var updateTodoItem = function (todoItem) {
          // This code takes a freshly completed TodoItem and updates the database. 
          // If the server version of the record has been updated, we get the updated
          // record from the Precondition Failed error in order to resolve the conflict.
          var serverItem = null;
          todoTable.update(todoItem).then(null, function (error) {
            if (error.message == "Precondition Failed") {
              serverItem = error.serverInstance;
            }
            else {
              var msgDialog =
                new Windows.UI.Popups.MessageDialog(error.request.responseText,"Update Failed");
                msgDialog.showAsync();
            }
          }).done(function () {
            if (serverItem != null)
              resolveDatabaseConflict(todoItem, serverItem);
          });
        };


3. No arquivo default.js, adicione a definição da função `resolveDatabaseConflict()` que é referenciada na função `updateTodoItem`. Observe que para resolver o conflito, você define a versão do item local como a versão atualizada do servidor antes de atualizar o item no banco de dados. Caso contrário, você encontrará um conflito continuamente.


        var resolveDatabaseConflict = function (localItem, serverItem) {
          var content = "This record has been changed as follows on the server already..\n\n" +
              "id : " + serverItem.id + "\n" +
              "text : " + serverItem.text + "\n" +
              "complete : " + serverItem.complete + "\n\n" +
              "Do you want to overwrite the server instance with your data?";
          var msgDialog = new Windows.UI.Popups.MessageDialog(content, "Resolve Database Conflict");
          msgDialog.commands.append(new Windows.UI.Popups.UICommand("Yes"));
          msgDialog.commands.append(new Windows.UI.Popups.UICommand("No"));
          msgDialog.showAsync().done(function (command) {
              if (command.label == "Yes") {
                  localItem.__version = serverItem.__version;
                  updateTodoItem(localItem);
              }
          });
        }


<h2><a name="test-app"></a>Testar conflitos de gravação do banco de dados no aplicativo</h2>

Nesta seção você criará um pacote do aplicativo da Windows Store para instalar o aplicativo em uma segunda máquina ou em uma máquina virtual. Em seguida, você executará o aplicativo nas duas máquinas gerando um conflito de gravação para testar o código. As duas instâncias do aplicativo tentarão atualizar a mesma propriedade `text`  do item exigindo que o usuário resolva o conflito.


1. Crie um pacote de aplicativos da Windows Store para instalação na segunda máquina ou na máquina virtual. Para fazer isso, clique em **Projeto**->**Repositório**->**Criar Pacotes de Aplicativos** no Visual Studio.

	![][0]

2. Na tela Criar seus Pacotes, clique em **Não**, uma vez que esse pacote não será carregado na Windows Store. Em seguida, clique em **Avançar**.

	![][1]

3. Na tela Selecionar e Configurar Pacotes, aceite os padrões e clique em **Criar**.

	![][10]

4. Na tela Criação de Pacote Concluída, clique no link **Local de saída** para abrir o local do pacote.

   	![][11]

5. Copie a pasta do pacote "todolist_1.0.0.0_AnyCPU_Debug_Test" na segunda máquina. Nessa máquina, abra a pasta do pacote e clique com o botão direito do mouse no script do PowerShell **Add-AppDevPackage.ps1** e clique em **Executar com o PowerShell** conforme mostrado abaixo. Siga os prompts para instalar o aplicativo.

	![][12]
  
6. Execute a instância 1 do aplicativo no Visual Studio clicando em **Depurar**->**Iniciar Depuração**. Na tela Iniciar da segunda máquina, clique na seta para baixo para ver "Aplicativos por nome". Em seguida, clique no aplicativo **todolist** para executar a instância 2 do aplicativo. 

	Instância 1 do aplicativo	
	![][2]

	Instância 2 do aplicativo	
	![][2]


7. Na instância 1 do aplicativo, atualize o texto do último item para **Gravação de Teste 1** e pressione a tecla **Enter** para atualizar o banco de dados. A captura de tela abaixo mostra um exemplo.
	
	Instância 1 do aplicativo	
	![][3]

	Instância 2 do aplicativo	
	![][2]

8. Neste ponto, o último item na instância 2 do aplicativo tem uma versão antiga do item. Nessa instância do aplicativo, digite **Test Write 2** para a propriedade `text` do último item e pressione **Enter** para atualizar o banco de dados com uma propriedade `_version` antiga.

	Instância 1 do aplicativo	
	![][4]

	Instância 2 do aplicativo	
	![][5]

9. Como o valor `__version` usado com a tentativa de atualização não corresponde ao valor `__version` do servidor, o SDK dos Serviços Móveis gera uma `MobileServicePreconditionFailedException` como um erro na função `updateTodoItem` permitindo que o aplicativo resolva esse conflito. Para resolver o conflito, você pode clicar em **Sim** para confirmar os valores da instância 2. Como alternativa, clique em **Não** para descartar os valores na instância 2, deixando os valores da instância 1 do aplicativo confirmados. 

	Instância 1 do aplicativo	
	![][4]

	Instância 2 do aplicativo	
	![][6]



<h2><a name="scriptsexample"></a>Tratanado automaticamente a resolução de conflitos em scripts de servidor</h2>

Você pode detectar e resolver conflitos de gravação em scripts de servidor. Essa é uma boa ideia quando você pode usar a lógica de scripts em vez da interação do usuário para resolver o conflito. Nesta seção, você irá adicionar um script do lado do servidor à tabela TodoItem para o aplicativo. A lógica que esse script usará para resolver conflitos é a seguinte:

+  Se o campo ` complete` de TodoItem estiver definido como true, ele será considerado concluído e`text` não poderá mais ser alterado.
+  Se o campo` complete` do TodoItem ainda for falso, as tentativas de atualizar `text` serão confirmadas.

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
5. Execute o aplicativo **todolist** nas duas máquinas. Altere o `texto ` de TodoItem do último item na instância 2 e pressione **Enter** para que o aplicativo atualize o banco de dados.

	Instância 1 do aplicativo	
	![][4]

	Instância 2 do aplicativo	
	![][5]

6. Na instância 1 do aplicativo, insira um valor diferente para a propriedade do último texto e, em seguida, pressione **Enter**. O aplicativo tenta atualizar o banco de dados com uma propriedade`__version` incorreta.

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

9. Na instância 2, tente atualizar o último texto TodoItem's e pressione **Enter**, isso provoca um conflito porque ele foi atualizado definindo o campo concluído como verdadeiro. Em resposta ao conflito, o script o resolveu recusando a atualização porque o item já estava concluído. O script fornece uma mensagem na resposta.  

	Instância 1 do aplicativo	
	![][17]

	Instância 2 do aplicativo	
	![][18]

## <a name="next-steps"> </a>Próximas etapas

	Este tutorial demonstrou como habilitar um aplicativo da Windows Store para tratar conflitos de gravação ao trabalhar com dados nos Serviços Móveis. Em seguida, considere concluir um dos seguintes tutoriais da nossa série de dados:

* [Validar e modificar dados com scripts]
  <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar dados enviados do seu aplicativo.

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
[Tratanado automaticamente a resolução de conflitos em scripts de servidor]: #scriptsexample
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png 
[3]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png

<!-- URLs. -->
[Controle de simultaneidade otimista]: http://go.microsoft.com/fwlink/?LinkId=330935
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Conta do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/
[Validar e modificar dados com scripts]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Refinar consultas com paginação]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[SDK do Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Site de Exemplos de Código do Desenvolvedor]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[Propriedades do Sistema]: http://go.microsoft.com/fwlink/?LinkId=331143
