1.	Fazer logon no [Portal de Visualização do Microsoft Azure](https://portal.azure.com/) online.
2.	Na barra de navegação, clique em **Novo**, em seguida, clique em **Dados + Armazenamento** e, em seguida, clique em **Banco de Dados de Documentos do Azure**. 

	![Captura de tela do Portal de Visualização do Azure, destacando o botão Novo, Dados + Armazenamento na folha Criar e o Banco de Dados de Documentos do Azure na folha Dados + Armazenamento][1]

	<!-- Alternatively, from the Startboard, you can browse the Azure Marketplace, select **Data + storage**, choose **DocumentDB**, and then click **Create**.  --><!-- ![Screen shot of the Azure preview portal, showing the Marketplace blade with the DocumentDB tile highlighted, and the DocumentDB blade with the Create database button highlighted][2]    -->
   

3. Na folha **Nova conta do Banco de Dados de Documentos**, especifique a configuração desejada para a conta do Banco de Dados de Documentos.
 
	![Captura de tela da lâmina Novo Banco de Dados de Documentos][3]


	- No campo **ID**, digite um nome para identificar a conta do Banco de Dados de Documentos. Quando o **ID** for validado, uma marca de seleção verde será exibida na caixa de **ID**. Esse valor de **ID** torna-se o nome de host no URI. A **ID** pode conter somente letras minúsculas, números e o caractere “-”, e deve ter entre 3 e 50 caracteres. Observe que *documents.azure.com* é anexado ao nome do ponto de extremidade que você escolher, e seu resultado vai se tornar o ponto de extremidade da sua conta do Banco de Dados de Documentos.

	- A **Camada de Conta** está bloqueada porque o Banco de Dados de Documentos dá suporte a uma única camada de conta padrão. Para obter mais informações, consulte [Preços do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- Em **Grupo de recursos**, selecione ou crie um grupo de recursos para sua conta do Banco de Dados de Documentos. Por padrão, um novo Grupo de recursos será criado. Você pode, no entanto, optar por selecionar um grupo de recursos existente ao qual gostaria de adicionar sua conta do Banco de Dados de Documentos. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure](resource-group-portal.md).

	- Para **Assinatura**, selecione a assinatura do Azure que deseja usar para a conta do Banco de Dados de Documentos. Se a sua conta tiver apenas uma assinatura, essa conta será selecionada por padrão.
 
	- Use **Local** para especificar a localização geográfica na qual hospedar a sua conta do Banco de Dados de Documentos.

4.	Após as opções da nova conta do Banco de Dados de Documentos serem configuradas, clique em **Criar**. Pode levar alguns minutos para que a conta do Banco de Dados de Documentos seja criada. Para verificar o status, você pode monitorar o progresso no quadro inicial. ![Captura de tela do bloco Criando no quadro inicial - criador de banco de dados online][4]
  
	Ou então, você pode monitorar o progresso do hub de notificações.

	![Criar bancos de dados rapidamente - Captura de tela do hub Notificações, mostrando que a conta do Banco de Dados de Documentos está sendo criada - Notificação do criador de banco de dados online][5]

	![Captura de tela do hub de notificações, mostrando que a conta do Banco de Dados de Documentos foi criada com êxito e implantada em um grupo de recursos][6]

5.	Após criar a conta do Banco de Dados de Documentos, ele está pronto para uso com as configurações padrão no portal online. Observe que a consistência padrão da conta do Banco de Dados de Documentos será definida como **Sessão**. Você pode ajustar a configuração de consistência padrão clicando no bloco **Consistência padrão** na folha da **Conta do Banco de Dados de Documentos**.

    ![Captura de tela da lâmina Grupo de recursos][7]

<!--Image references-->
[1]: media/documentdb-create-dbaccount/ca1.png
[2]: media/documentdb-create-dbaccount/ca2.png
[3]: media/documentdb-create-dbaccount/ca3.png
[4]: media/documentdb-create-dbaccount/ca4.png
[5]: media/documentdb-create-dbaccount/ca5.png
[6]: media/documentdb-create-dbaccount/ca6.png
[7]: media/documentdb-create-dbaccount/ca7.png

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]: ../articles/documentdb/documentdb-manage.md

<!---HONumber=July15_HO3-->