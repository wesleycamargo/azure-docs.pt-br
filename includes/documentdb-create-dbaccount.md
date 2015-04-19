1.	Entre no [Portal de Visualização do Azure](https://portal.azure.com/).
2.	Na barra de navegação rápida, clique em **Novo**, selecione **Dados + Armazenamento** e clique em **Banco de Dados de Documentos**. 

	![Screen shot of the Azure Preview portal, highlighting the **New** button, **Data + storage** in the Create blade, and **DocumentDB** in the Data + storage blade][1]   

	Você pode, como alternativa, navegar pela Marketplace do Azure, selecionar **Dados + analíticos**, selecionar **Banco de Dados de Documentos** e clicar em **Criar**.  
	
	![Screen shot of the Azure Preview portal, showing the Marketplace blade with the DocumentDB tile highlighted, and the DocumentDB blade with the Create button highlighted][2] 
   

3. Na lâmina **Novo Banco de Dados de Documentos (visualização)**, especifique a configuração desejada para a conta do Banco de Dados de Documentos. 
 
	![Screen shot of the New DocumentDB (Preview) blade][3] 


	- No campo **Id**, digite um nome para identificar a conta do Banco de Dados de Documentos. Esse valor torna-se o nome de host no URI. O Id pode conter somente letras minúsculas, números e o caractere "-", e deve ter entre 3 e 50 caracteres. Observe que documents.azure.com é anexado ao nome do ponto de extremidade que você escolher, e seu resultado se tornará o ponto de extremidade da sua conta do Banco de Dados de Documentos.

	- A lente **Camada de preços** fica bloqueada porque a visualização do Banco de Dados de Documentos suporta apenas uma camada de preço padrão. Para obter mais informações, consulte [Preços do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- A lente **Configuração opcional** é usada para especificar a capacidade inicial alocada à conta do Banco de Dados de Documentos.  O Banco de Dados de Documentos utiliza as unidades de capacidade para permitir que você dimensione sua conta do Banco de Dados de Documentos, em que cada unidade de capacidade inclui o armazenamento e a taxa de transferência reservados para o banco de dados.  Por padrão, uma unidade de capacidade é provisionada.  Você pode ajustar o número de unidades de capacidade disponível para a sua conta do Banco de Dados de Documentos a qualquer momento por meio do [portal de visualização de gerenciamento](https://portal.azure.com/#gallery/Microsoft.DocumentDB). Para ver detalhes sobre a capacidade e a taxa de transferência do Banco de Dados de Documentos, consulte o artigo [Gerenciar a capacidade e o desempenho do Banco de Dados de Documentos ][documentdb-manage].

	- Em **Grupo de recursos**, selecione ou crie um grupo de recursos para sua conta do Banco de Dados de Documentos.  Por padrão, um novo Grupo de recursos será criado.  Você pode, no entanto, optar por selecionar um grupo de recursos existente ao qual gostaria de adicionar sua conta do Banco de Dados de Documentos. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure](azure-preview-portal-using-resource-groups.md)..

	- Para **Assinatura**, selecione a assinatura do Azure que deseja usar para a conta do Banco de Dados de Documentos. Se sua conta tem apenas uma assinatura, ela será selecionada automaticamente.*
 
	- Utilize a **Localização** para especificar a localização geográfica em que a conta do Banco de Dados de Documentos será hospedada.   

3.	Após as opções da nova conta do Banco de Dados de Documentos serem configuradas, clique em **Criar**.  Pode levar alguns minutos para que a conta do Banco de Dados de Documentos seja criada.  Para verificar o status, você pode monitorar o progresso na Startboard.  
	![Screen shot of the Creating tile on the Startboard][4]  
  
	Ou então, você pode monitorar o progresso do hub de notificações.  

	![Screen shot of the Notifications hub, showing that the DocumentDB account is being created][5]  

	![Screen shot of the Notifications hub, showing that the DocumentDB account was created successfully and deplyed to a resource group][6]

4.	Após a conta do Banco de Dados de Documentos ter sido criada, ela estará pronta para uso com as configurações padrão.

	*Observe que a consistência padrão da conta do Banco de Dados de Documentos será definida como Sessão.  Você pode ajustar a configuração da consistência padrão por meio do [portal de visualização de gerenciamento](https://portal.azure.com/#gallery/Microsoft.DocumentDB).*  
	![Screen shot of the Resource Group blade][7]  


<!--Image references-->
[1]: ./media/documentdb-create-dbaccount/ca1.png
[2]: ./media/documentdb-create-dbaccount/ca2.png
[3]: ./media/documentdb-create-dbaccount/ca3.png
[4]: ./media/documentdb-create-dbaccount/ca4.png
[5]: ./media/documentdb-create-dbaccount/ca5.png
[6]: ./media/documentdb-create-dbaccount/ca6.png
[7]: ./media/documentdb-create-dbaccount/ca7.png

[Como: Criar uma conta do Banco de Dados de Documentos]: #Howto
[Próximas etapas]: #NextSteps
[documentdb-manage]:../articles/documentdb-manage.md

<!--HONumber=49-->