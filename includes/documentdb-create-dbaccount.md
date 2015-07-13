1.	Fazer logon no [portal de visualização do Microsoft Azure](https://portal.azure.com/).
2.	Na barra de navegação rápida, clique em **Novo**, selecione **Dados + Armazenamento** e clique em **Banco de Dados de Documentos**. 

	![Captura de tela do portal de visualização do Azure, destacando o botão Novo, Dados + Armazenamento na lâmina Criar e Banco de Dados de Documentos na lâmina Dados + Armazenamento][1]

	<!-- Alternatively, from the Startboard, you can browse the Azure Marketplace, select **Data + storage**, choose **DocumentDB**, and then click **Create**.  --><!-- ![Screen shot of the Azure Preview portal, showing the Marketplace blade with the DocumentDB tile highlighted, and the DocumentDB blade with the Create button highlighted][2]    -->
   

3. Na lâmina **Novo Banco de Dados de Documentos**, especifique a configuração desejada para a conta do Banco de Dados de Documentos.
 
	![Captura de tela da lâmina Novo Banco de Dados de Documentos][3]


	- No campo **ID**, digite um nome para identificar a conta do Banco de Dados de Documentos. Esse valor torna-se o nome de host no URI. A **ID** pode conter somente letras minúsculas, números e o caractere “-”, e deve ter entre 3 e 50 caracteres. Observe que *documents.azure.com* é anexado ao nome do ponto de extremidade que você escolher, e seu resultado vai se tornar o ponto de extremidade da sua conta do Banco de Dados de Documentos.

	- A **Camada de Conta** está bloqueada porque o Banco de Dados de Documentos dá suporte a uma única camada de conta padrão. Para obter mais informações, consulte [Preços do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- Em **Grupo de recursos**, selecione ou crie um grupo de recursos para sua conta do Banco de Dados de Documentos. Por padrão, um novo Grupo de recursos será criado. Você pode, no entanto, optar por selecionar um grupo de recursos existente ao qual gostaria de adicionar sua conta do Banco de Dados de Documentos. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure](resource-group-portal.md).

	- Para **Assinatura**, selecione a assinatura do Azure que deseja usar para a conta do Banco de Dados de Documentos. Se sua conta tem apenas uma assinatura, ela será selecionada automaticamente.
 
	- Utilize a **Localização** para especificar a localização geográfica em que a conta do Banco de Dados de Documentos será hospedada.

4.	Após as opções da nova conta do Banco de Dados de Documentos serem configuradas, clique em **Criar**. Pode levar alguns minutos para que a conta do Banco de Dados de Documentos seja criada. Para verificar o status, você pode monitorar o progresso no quadro inicial. ![Captura de tela do bloco Criando, no quadro inicial][4]
  
	Ou então, você pode monitorar o progresso do hub de notificações.

	![Captura de tela do hub de notificações, mostrando que está sendo criada a conta do Banco de Dados de Documentos][5]

	![Captura de tela do hub de notificações, mostrando que a conta do Banco de Dados de Documentos foi criada com êxito e implantada em um grupo de recursos][6]

5.	Após a conta do Banco de Dados de Documentos ter sido criada, ela estará pronta para uso com as configurações padrão. Observe que a consistência padrão da conta do Banco de Dados de Documentos será definida como Sessão. Você pode ajustar a configuração da consistência padrão por meio do [portal de visualização do Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB).

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

<!---HONumber=62-->