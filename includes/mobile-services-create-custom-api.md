

1. Faça logon no Portal de Gerenciamento do Azure, clique em **Serviços Móveis** e selecione seu serviço móvel.

2. Clique na guia **API** e, em seguida, clique em **Criar**. Isso exibe a caixa de diálogo **Criar uma nova API personalizada**.

3. Digite _completeall_ em **Nome da API** e clique no botão de seleção para criar a nova API.

	> [AZURE.NOTE]Com as permissões padrão, qualquer pessoa com a chave do aplicativo pode chamar a API personalizada. No entanto, a chave de aplicativo não é considerada uma credencial segura porque ela não pode ser distribuída ou armazenada com segurança. Considere a possibilidade de restringir o acesso somente para usuários autenticados para obter segurança adicional.

4. Clique em **completeall** na tabela de API.

5. Clique na guia **Script**, substitua o código existente pelo código a seguir e clique em **Salvar**. Esse código usa o [objeto mssql] para acessar a tabela **todoitem** diretamente para definir o sinalizador `complete` em todos os itens. Como a função **exports.post** é usada, os clientes enviam uma solicitação POST para executar a operação. O número de linhas alteradas é retornado ao cliente como um valor inteiro.


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



> [AZURE.NOTE]O objeto de <a href="http://msdn.microsoft.com/library/windowsazure/jj554218.aspx" target="_blank">solicitação</a> e <a href="http://msdn.microsoft.com/library/windowsazure/dn303373.aspx" target="_blank">resposta</a> fornecido para funções da API personalizadas é implementado pela <a href="http://go.microsoft.com/fwlink/p/?LinkId=309046" target="_blank">biblioteca Express.js</a>. Para obter mais informações, consulte <a href="http://msdn.microsoft.com/library/windowsazure/dn280974.aspx" target="_blank">API personalizada</a>.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[objeto mssql]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx

<!---HONumber=July15_HO3-->