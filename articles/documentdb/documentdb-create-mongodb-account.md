---
title: Criar uma conta do DocumentDB para uso com aplicativos MongoDB | Microsoft Docs
description: "Use este tutorial para saber como criar uma conta do DocumentDB com suporte ao protocolo do MongoDB, agora disponível para a versão de visualização."
keywords: aplicativos mongodb, aplicativo mongodb
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 413f3ae1-a68e-43be-b0d7-fa2987644f3e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 74f80f14a66fa61c19cb5d5fd758cea0ccdd43c5
ms.openlocfilehash: bf72fa88f9444578dd0192e94c89c862273886b2


---

# <a name="create-a-documentdb-account-for-use-with-mongodb-apps"></a>Criar uma conta do DocumentDB para uso com aplicativos MongoDB
Os bancos de dados do Banco de Dados de Documentos agora podem ser usados como o repositório de dados para aplicativos escritos para o MongoDB. Para usar essa funcionalidade, você precisa de uma conta do Azure e uma conta do DocumentDB. Este tutorial orienta você pelo processo de criar uma conta do DocumentDB para uso com os aplicativos MongoDB. 

## <a name="prerequisite"></a>Pré-requisito
Uma conta do Azure. Se você ainda não tiver uma, crie agora mesmo uma [conta gratuita do Azure](https://azure.microsoft.com/free/).
## <a name="create-a-documentdb-account"></a>Criar uma conta do Banco de Dados de Documentos

1. Em um navegador da Internet, entre no [Portal do Azure](https://portal.azure.com).
2. No painel de navegação esquerdo, clique em **NoSQL (DocumentDB)**.

    ![Captura de tela do painel de navegação esquerdo do Portal, realçando a entrada NoSQL do DocumentDB](./media/documentdb-create-mongodb-account/portalleftnav.png)

3. Como alternativa, clique em **Mais serviços >**, digite **DocumentDB** na barra de pesquisa superior e clique em **NoSQL (DocumentDB)**.

    ![Captura de tela da folha Mais serviços, pesquisando a entrada NoSQL do DocumentDB](./media/documentdb-create-mongodb-account/more-services-search.PNG)

4. Na parte superior da folha **NoSQL (DocumentDB)**, clique em **+ Adicionar** na barra de ação superior.

    ![Captura de tela do botão Adicionar na folha de recursos NoSQL do DocumentDB](./media/documentdb-create-mongodb-account/add-documentdb-account.png)

5. Na folha **Conta do Banco de Dados de Documentos** , especifique a configuração desejada para a conta.

   ![Captura de tela da folha Novo Banco de Dados de Documentos com suporte de protocolo para MongoDB](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.PNG)

    - Na caixa **ID** , insira um nome para identificar a conta.  Quando a **ID** for validada, uma marca de seleção verde será exibida na caixa de **ID**. Esse valor de **ID** torna-se o nome de host no URI. A **ID** pode conter somente letras minúsculas, números e o caractere “-”, e deve ter entre 3 e 50 caracteres. Observe que *documents.azure.com* é anexado ao nome do ponto de extremidade escolhido, o resultado disso se tornará o ponto de extremidade da sua conta.

    - Para **API NoSQL**, selecione **MongoDB**. Isso especifica a API de comunicação que você deseja usar para interagir com o banco de dados do DocumentDB.

    - Em **Assinatura**, escolha a assinatura do Azure que deseja usar para a conta. Se a sua conta tiver apenas uma assinatura, essa conta será selecionada por padrão.

    - No **Grupo de Recursos**, escolha ou crie um grupo de recursos para a conta.  Por padrão, um grupo de Recursos na assinatura do Azure será escolhido.  No entanto, você pode optar por criar um novo grupo de recursos ao qual deseja adicionar a conta. Para saber mais, veja [Como usar o portal do Azure para gerenciar os recursos do Azure](../azure-portal/resource-group-portal.md).

    - Use **Localização** para especificar o local geográfico no qual hospedar a conta.

6. Depois que as novas opções de conta forem configuradas, clique em **Criar**.  Pode levar alguns minutos para criar a conta.

   Você pode monitorar o progresso no hub de notificações.  

   ![Captura de tela do hub de notificações, mostrando que está sendo criada a conta do Banco de Dados de Documentos](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-deployment-status.png)  

7. Para acessar sua nova conta, clique em **DocumentDB (NoSQL)** no menu esquerdo. Na lista de DocumentDB regulares e DocumentDB com contas de suporte de protocolo Mongo, clique no nome da sua nova conta.
8. Sua conta do DocumentDB agora está pronta para ser usada com seu aplicativo MongoDB.

   ![Captura de tela da folha conta padrão](./media/documentdb-create-mongodb-account/defaultaccountblade.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba como se [conectar](documentdb-connect-mongodb-account.md) a uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB.



<!--HONumber=Nov16_HO4-->


