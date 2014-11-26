1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique no seu aplicativo.

    ![][1]

2.  Clique na guia **API** e, em seguida, clique em **Criar uma API personalizada**.

    ![][2]

    Isso exibe a caixa de diálogo **Criar uma nova API personalizada**.

3.  Digite *completeall* em **Nome da API** e clique no botão de seleção.

    ![][3]

    Isso cria a nova API.

    > [WACOM.NOTE] As permissões padrão são definidas, o que significa que qualquer usuário do aplicativo pode chamar a API personalizada. No entanto, a chave de aplicativo não é distribuída ou armazenada de forma segura e não pode ser considerada uma credencial segura. Por isso, você deve restringir o acesso somente para usuários autenticados em operações que modificam dados ou afetam o serviço móvel.

4.  Clique na nova entrada **completeall** na tabela de API.

    ![][4]

5.  Clique na guia **Script**, substitua o código existente pelo código a seguir e clique em **Salvar**.

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

    Esse código usa o [objeto mssql][objeto mssql] para acessar a tabela **todoitem** diretamente para definir o sinalizador de conclusão em todos os itens. Como a função **exports.post** é usada, os clientes enviam uma solicitação POST para executar a operação. O número de linhas alteradas é retornado ao cliente como um valor inteiro.

> [WACOM.NOTE]
>  O objeto de [solicitação][solicitação] e [resposta][resposta] fornecido para funções da API personalizadas é implementado pela [biblioteca Express.js][biblioteca Express.js]. Para obter mais informações, consulte [API personalizada][API personalizada].

Em seguida, você modificará o aplicativo quickstart para adicionar um novo botão e o código que chama assincronamente a nova API personalizada.





  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-create-custom-api/mobile-services-selection.png
  [2]: ./media/mobile-services-create-custom-api/mobile-custom-api-create.png
  [3]: ./media/mobile-services-create-custom-api/mobile-custom-api-create-dialog2.png
  [4]: ./media/mobile-services-create-custom-api/mobile-custom-api-select2.png
  [objeto mssql]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554212.aspx
  [solicitação]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554218.aspx
  [resposta]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn303373.aspx
  [biblioteca Express.js]: http://go.microsoft.com/fwlink/p/?LinkId=309046
  [API personalizada]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn280974.aspx
