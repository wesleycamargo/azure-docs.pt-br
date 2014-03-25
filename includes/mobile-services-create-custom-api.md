

1. Faça logon no [Portal de Gerenciamento do Windows Azure], clique em **Serviços Móveis** e, em seguida, clique no seu aplicativo.

	![][0]

2. Clique na guia **API** e, em seguida, clique em **Criar uma API personalizada**.

	![][1]

	Isso exibe a caixa de diálogo **Criar uma nova API personalizada**.

3. Digite _completeall_ in **Nome da API** e, em seguida, clique no botão de seleção.

	![][2]

	Isso cria a nova API.


	> [WACOM.NOTE]
	> Permissões padrão são definidas, o que significa que qualquer usuário do aplicativo pode chamar a API personalizada. No entanto, a chave de aplicativo não é distribuída ou armazenada de forma segura e não pode ser considerada uma credencial segura. Por isso, você deve restringir o acesso somente para usuários autenticados em operações que modificam dados ou afetam o serviço móvel.

4. Clique na nova entrada **completeall** na tabela de API.

	![][3]

5. Clique na guia **Script**, substitua o código existente pelo código a seguir e clique em **Salvar**.

		exports.post = function(request, response) {
			var mssql = request.service.mssql;
			var sql = "UPDATE todoitem SET complete = 1 " + 
                "WHERE complete = 0; SELECT @@ROWCOUNT as count";
			mssql.query(sql, {
				success: function(results) {			
					if(results.length == 1)							
						response.send(200, results[0]);			
				}
			})
		};


	Esse código usa o [objeto mssql] para acessar a tabela **todoitem** diretamente para definir o sinalizador de conclusão em todos os itens. Como a função **exports.post** é usada, os clientes enviam uma solicitação POST para executar a operação. O número de linhas alteradas é retornado ao cliente como um valor inteiro.

> [WACOM.NOTE]
> O objeto <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/jj554218.aspx" target="_blank">solicitação</a> e <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dn303373.aspx" target="_blank">resposta</a> fornecido para funções de API personalizadas é implementado pela <a href="http://go.microsoft.com/fwlink/p/?LinkId=309046" target="_blank">Express.js library</a>. Para obter mais informações, consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dn280974.aspx" target="_blank">API personalizada</a>. 

Em seguida, você modificará o aplicativo quickstart para adicionar um novo botão e o código que chama assincronamente a nova API personalizada.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-create-custom-api/mobile-services-selection.png
[1]: ./media/mobile-services-create-custom-api/mobile-custom-api-create.png
[2]: ./media/mobile-services-create-custom-api/mobile-custom-api-create-dialog2.png
[3]: ./media/mobile-services-create-custom-api/mobile-custom-api-select2.png

<!-- URLs. -->
[Portal de Gerenciamento do Windows Azure]: https://manage.windowsazure.com/
[objeto mssql]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554212.aspx

