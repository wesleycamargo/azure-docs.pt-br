---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-create-account
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 4b2ae4a8d3b93b5c27e180a5875ae9e90483068a
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017



---

# <a name="create-an-azure-cosmos-db-account-with-mongodb-api"></a>Criar uma conta do Azure Cosmos DB com a API do MongoDB
Os bancos de dados do Azure Cosmos DB agora podem ser usados como o armazenamento de dados para aplicativos escritos para o MongoDB. Para usar essa funcionalidade, você precisa de uma conta do Azure e uma conta do Azure Cosmos DB. Este tutorial orienta você pelo processo de criação de uma conta do Azure Cosmos DB para uso com os aplicativos do MongoDB. 

Crie uma conta do Azure Cosmos DB com suporte no MongoDB usando o portal do Azure ou a CLI do Azure com os modelos do Azure Resource Manager. Este artigo mostra como criar uma conta do Azure Cosmos DB com suporte no MongoDB usando o portal do Azure. Para criar uma conta usando a CLI do Azure com o Azure Resource Manager, consulte [Automatizar o gerenciamento de conta do Azure Cosmos DB usando a CLI 2.0 do Azure](documentdb-automation-resource-manager-cli.md).

## <a name="prerequisite"></a>Pré-requisito
Uma conta do Azure. Se você ainda não tiver uma, crie agora mesmo uma [conta gratuita do Azure](https://azure.microsoft.com/free/).
## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

1. Em um navegador da Internet, entre no [Portal do Azure](https://portal.azure.com).
2. No painel de navegação esquerdo, clique em **Azure Cosmos DB**.

    ![Captura de tela do painel de navegação esquerdo do Portal, realçando a entrada NoSQL do DocumentDB](./media/documentdb-create-mongodb-account/portalleftnav.png)

3. Como alternativa, clique em **Mais serviços >**, digite **DocumentDB** na barra de pesquisa superior e clique em **Azure Cosmos DB**.

    ![Captura de tela da folha Mais serviços, pesquisando a entrada NoSQL do DocumentDB](./media/documentdb-create-mongodb-account/more-services-search.PNG)

4. Na parte superior da folha **Azure Cosmos DB**, clique em **+ Adicionar** na barra de ação superior.

    ![Captura de tela do botão Adicionar na folha de recursos do Cosmos DB](./media/documentdb-create-mongodb-account/add-documentdb-account.png)

5. Na folha **Conta do Azure Cosmos DB**, especifique a configuração desejada para a conta.

   ![Captura de tela da folha Novo Azure Cosmos DB com suporte de protocolo no MongoDB](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.PNG)

    - Na caixa **ID** , insira um nome para identificar a conta.  Quando a **ID** for validada, uma marca de seleção verde será exibida na caixa de **ID**. Esse valor de **ID** torna-se o nome de host no URI. A **ID** pode conter somente letras minúsculas, números e o caractere “-”, e deve ter entre 3 e 50 caracteres. Observe que *documents.azure.com* é anexado ao nome do ponto de extremidade escolhido, o resultado disso se tornará o ponto de extremidade da sua conta.

    - Para **API**, selecione **MongoDB**. Isso especifica a API de comunicação que você deseja usar para interagir com o banco de dados do Azure Cosmos DB.

    - Em **Assinatura**, escolha a assinatura do Azure que deseja usar para a conta. Se a sua conta tiver apenas uma assinatura, essa conta será selecionada por padrão.

    - No **Grupo de Recursos**, escolha ou crie um grupo de recursos para a conta.  Por padrão, um grupo de Recursos na assinatura do Azure será escolhido.  No entanto, você pode optar por criar um novo grupo de recursos ao qual deseja adicionar a conta. Para saber mais, veja [Como usar o portal do Azure para gerenciar os recursos do Azure](../azure-portal/resource-group-portal.md).

    - Use **Localização** para especificar o local geográfico no qual hospedar a conta.

6. Depois que as novas opções de conta forem configuradas, clique em **Criar**.  Pode levar alguns minutos para criar a conta.

   Você pode monitorar o progresso no hub de notificações.  

   ![Captura de tela do hub de Notificações, mostrando que a conta do Azure Cosmos DB está sendo criada](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-deployment-status.png)  

7. Para acessar sua nova conta, clique em **Azure Cosmos DB** no menu esquerdo. Na lista de DocumentDB regulares e DocumentDB com contas de suporte de protocolo Mongo, clique no nome da sua nova conta.
8. Sua conta do Azure Cosmos DB agora está pronta para ser usada com seu aplicativo do MongoDB.

   ![Captura de tela da folha conta padrão](./media/documentdb-create-mongodb-account/defaultaccountblade.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba como se [conectar](documentdb-connect-mongodb-account.md) a uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB.

