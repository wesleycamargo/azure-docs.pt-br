1.	Entre no [portal do Microsoft Azure](https://portal.azure.com/) online.
2.	Na barra de navegação, clique em **Novo**, em seguida, clique em **Dados + Armazenamento** e, em seguida, clique em **Banco de Dados de Documentos do Azure**. 
  
	![Captura de tela do portal do Azure para criação de um banco de dados, destacando o botão Novo, Dados + Armazenamento na folha Criar, e o Banco de Dados de Documentos do Azure na folha Dados + Armazenamento](media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Na folha **Nova conta do Banco de Dados de Documentos**, especifique a configuração desejada para a conta do Banco de Dados de Documentos.
 
	![Captura de tela da lâmina Novo Banco de Dados de Documentos](media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)


	- No campo **ID**, digite um nome para identificar a conta do Banco de Dados de Documentos. Quando o **ID** for validado, uma marca de seleção verde será exibida na caixa de **ID**. Esse valor de **ID** torna-se o nome de host no URI. A **ID** pode conter somente letras minúsculas, números e o caractere “-”, e deve ter entre 3 e 50 caracteres. Observe que *documents.azure.com* é anexado ao nome do ponto de extremidade que você escolher, e seu resultado vai se tornar o ponto de extremidade da sua conta do Banco de Dados de Documentos.
	

	- A **Camada de Conta** está bloqueada porque o Banco de Dados de Documentos dá suporte a uma única camada de conta padrão. Para obter mais informações, consulte [Preços do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).
	
	- Para **Assinatura**, selecione a assinatura do Azure que deseja usar para a conta do Banco de Dados de Documentos. Se a sua conta tiver apenas uma assinatura, essa conta será selecionada por padrão.

	- Em **Grupo de Recursos**, selecione ou crie um grupo de recursos para sua conta do Banco de Dados de Documentos. Por padrão, um novo Grupo de recursos será criado. Você pode, no entanto, optar por selecionar um grupo de recursos existente ao qual gostaria de adicionar sua conta do Banco de Dados de Documentos. Para saber mais, veja [Como usar o portal do Azure para gerenciar os recursos do Azure](resource-group-portal.md).
 
	- Use **Local** para especificar a localização geográfica na qual hospedar a sua conta do Banco de Dados de Documentos.

4.	Após as opções da nova conta do Banco de Dados de Documentos serem configuradas, clique em **Criar**. Pode levar alguns minutos para criar a conta do Banco de Dados de Documentos. Para verificar o status, você pode monitorar o progresso no quadro inicial. ![Captura de tela do bloco Criando no quadro inicial - criador de banco de dados online](media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-3.png)
  
	Ou então, você pode monitorar o progresso do hub de notificações.

	![Crie bancos dados rapidamente - Captura de tela do hub de notificações, mostrando que a conta do Banco de Dados de Documentos está sendo criada](media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)

	![Captura de tela do hub de notificações, mostrando que a conta do Banco de Dados de Documentos foi criada com êxito e implantada em um grupo de recursos - Criador de banco de dados online](media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)

5.	Após criar a conta do Banco de Dados de Documentos, ele está pronto para uso com as configurações padrão no portal online. Observe que a consistência padrão da conta do Banco de Dados de Documentos será definida como **Sessão**. Você pode ajustar a configuração de consistência padrão clicando no bloco **Consistência padrão** na folha da **Conta do Banco de Dados de Documentos**.

    ![Captura de tela da folha Grupo de Recursos - começar o desenvolvimento do aplicativo](media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-6.png)

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]: ../articles/documentdb/documentdb-manage.md

<!---HONumber=AcomDC_1223_2015-->