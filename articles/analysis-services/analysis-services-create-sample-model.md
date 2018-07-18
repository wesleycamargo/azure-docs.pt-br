---
title: Adicionar um modelo de tabela de amostra para o servidor do Azure Analysis Services | Microsoft Docs
description: Saiba como adicionar um modelo de amostra no Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 27353ff8c05f44b76304279e09a8a8d817041d78
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-add-a-sample-model"></a>Tutorial: adicionar um modelo de amostra

Neste tutorial, você deve adicionar um modelo de amostra do Adventure Works ao seu servidor. O modelo de amostra é uma versão completa do tutorial de modelagem dos dados de Vendas pela Internet do Adventure Works (1200). Um modelo de amostra é útil para testar o gerenciamento de modelos, a conexão com ferramentas e aplicativos do cliente e consultar os dados do modelo.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este tutorial, você precisará:

- Um servidor do Azure Analysis Services
- Permissões de administrador do servidor

## <a name="sign-in-to-the-azure-portal"></a>Entrar no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-sample-model"></a>Criar um modelo de amostra

1. Na **Visão Geral** do servidor, clique em **Novo modelo**.

    ![Criar um modelo de amostra](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. Em **Novo modelo** > **Escolha uma fonte de dados**,  verifique se **Dados de exemplo**  está selecionado e, em seguida, clique em **Adicionar**.

    ![Selecionar dados de exemplo](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. Em **Visão geral**, verifique se a amostra `adventureworks` é criada.

    ![Selecionar dados de exemplo](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)

## <a name="clean-up-resources"></a>Limpar recursos

Seu modelo de amostra está usando recursos de memória cache. Se você não estiver usando o modelo de amostra para testes, você deverá removê-lo do servidor.

> [!NOTE]
> Essas etapas descrevem como excluir um modelo de um servidor usando o SSMS. Também é possível excluir um modelo, usando a versão prévia do recurso de Web Designer.

1. No SSMS > **Pesquisador de Objetos**, clique em **Conectar** > **Analysis Services**.

2. Em **Conectar ao servidor**, cole no nome do servidor e, em seguida, em **Autenticação**, escolha **Active Directory - Universal com suporte MFAA**, insira seu nome de usuário e, em seguida, clique em **Conectar**.

    ![Entrar](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. No **Pesquisador de Objetos**, clique com o botão direito do mouse no banco de dados de exemplo `adventureworks` e, em seguida, clique em **Excluir**.

    ![Excluir banco de dados de exemplo](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>Próximas etapas 

[Conexão no Power BI Desktop](analysis-services-connect-pbi.md)   
[Gerenciar usuários e funções de banco de dados](analysis-services-database-users.md)


