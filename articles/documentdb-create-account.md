<properties 
	pageTitle="Crie uma conta de banco de dados | Azure" 
	description="Saiba como criar uma conta de Banco de Dados de Documentos do NoSQL e escolha as configurações de conta no portal de visualização do Azure." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/09/2014" 
	ms.author="mimig"/>

#Crie uma conta de banco de dados
Para usar o Banco de Dados de Documentos, você precisa criar uma conta do Banco de Dados de Documentos.  Este tópico descreve como criar uma conta uma conta do Banco de Dados de Documentos no portal de gerenciamento de visualização do Azure.  


1.	Entre no [portal de gerenciamento](https://portal.azure.com/#gallery/Microsoft.DocumentDB)..
2.	Clique em Novo -> Conta do Banco de Dados de Documentos.  
	![][1]  

	Você pode, como alternativa, navegar pela Marketplace do Azure, selecionar a categoria "Dados + analíticos", selecionar **Banco de Dados de Documentos** e clicar em **Criar**.  

	![][2]   

3. Na lâmina **Novo Banco de Dados de Documentos (visualização)**, especifique a configuração desejada para a conta do Banco de Dados de Documentos. 
 
	![][3] 


	- No campo **Id**, digite um nome para identificar a conta do Banco de Dados de Documentos. Esse valor torna-se o nome de host no URI. O Id pode conter somente letras minúsculas, números e o caractere "-", e deve ter entre 3 e 50 caracteres. Observe que documents.azure.com é anexado ao nome do ponto de extremidade que você escolher, e seu resultado se tornará o ponto de extremidade da sua conta do Banco de Dados de Documentos.

	- A lente **Camada de preços** fica bloqueada porque a visualização do Banco de Dados de Documentos suporta apenas uma camada de preço padrão. Para obter mais informações, consulte [Preços do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- A lente **Configuração opcional** é usada para especificar a capacidade inicial alocada à conta do Banco de Dados de Documentos.  O Banco de Dados de Documentos utiliza as unidades de capacidade para permitir que você dimensione sua conta do Banco de Dados de Documentos, em que cada unidade de capacidade inclui o armazenamento e a taxa de transferência reservados para o banco de dados.  Por padrão, uma unidade de capacidade é provisionada.  Você pode ajustar o número de unidades de capacidade disponível para a sua conta do Banco de Dados de Documentos a qualquer momento por meio do [portal de visualização de gerenciamento](https://portal.azure.com/#gallery/Microsoft.DocumentDB). Para ver detalhes sobre a capacidade e a taxa de transferência do Banco de Dados de Documentos, consulte o artigo [Gerenciar a capacidade e o desempenho do Banco de Dados de Documentos][documentdb-manage].

	- Em **Grupo de recursos**, selecione ou crie um grupo de recursos para sua conta do Banco de Dados de Documentos.  Por padrão, um novo Grupo de recursos será criado.  Você pode, no entanto, optar por selecionar um grupo de recursos existente ao qual gostaria de adicionar sua conta do Banco de Dados de Documentos. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups/).

	- Para **Assinatura**, selecione a assinatura do Azure que deseja usar para a conta do Banco de Dados de Documentos. Se sua conta tem apenas uma assinatura, ela será selecionada automaticamente.*
 
	- Utilize a **Localização** para especificar a localização geográfica em que a conta do Banco de Dados de Documentos será hospedada.   

3.	Após as opções da nova conta do Banco de Dados de Documentos serem configuradas, clique em **Criar**.  Pode levar alguns minutos para que a conta do Banco de Dados de Documentos seja criada.  Para verificar o status, você pode monitorar o progresso na Startboard.  
	![][4]  
  
	Ou, você pode monitorar o progresso do hub de notificações.  

	![][5]  

	![][6]

4.	Após a conta do Banco de Dados de Documentos ter sido criada, ela estará pronta para uso com as configurações padrão.

	*Observe que a consistência padrão da conta do Banco de Dados de Documentos será definida como Sessão.  Você pode ajustar a configuração da consistência padrão por meio do [portal de visualização de gerenciamento](https://portal.azure.com/#gallery/Microsoft.DocumentDB)  
	![][7]  

5.	Você também pode acessar suas contas existentes do Banco de Dados de Documentos a partir da lâmina **Navegar**.  
	![][8]

##<a id="NextSteps"></a>Próximas etapas
Para começar a usar o Banco de Dados de Documentos do Azure, explore estes recursos:

-	[Modelo e conceitos de recursos do Banco de Dados de Documentos](/documentation/articles/documentdb-resources/)
-	[Interagir com recursos do Banco de Dados de Documentos](/documentation/articles/documentdb-interactions-with-resources/)
-	[Como criar uma conta do Banco de Dados de Documentos](/documentation/articles/documentdb-create-account/)
-	[Introdução à conta do Banco de Dados de Documentos](/documentation/articles/documentdb-get-started/)

Para saber mais sobre o Banco de Dados de Documento, consulte a documentação do Banco de Dados de Documentos do Azure em [azure.com](http://go.microsoft.com/fwlink/p/?LinkID=402319)

[Como: Criar uma conta do Banco de Dados de Documentos]: #Howto
[Próximas etapas]: #NextSteps
[documentdb-manage]:../documentdb-manage/

<!--Image references-->
[1]: ./media/documentdb-create-account/ca1.png
[2]: ./media/documentdb-create-account/ca2.png
[3]: ./media/documentdb-create-account/ca3.png
[4]: ./media/documentdb-create-account/ca4.png
[5]: ./media/documentdb-create-account/ca5.png
[6]: ./media/documentdb-create-account/ca6.png
[7]: ./media/documentdb-create-account/ca7.png
[8]: ./media/documentdb-create-account/ca8.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
