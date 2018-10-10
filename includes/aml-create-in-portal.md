---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/24/2018
ms.openlocfilehash: 6d6e6a2aea867c5b603d950a4bbaa33f14695f45
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47010882"
---
Entre no [Portal do Azure](https://portal.azure.com/) usando as credenciais de assinatura do Azure que você usará. Caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

Painel de workspace do portal tem suporte nos navegadores Microsoft Edge, Chrome e Firefox apenas.

   ![Portal do Azure](./media/aml-create-in-portal/portal-dashboard.png)

Selecione o botão **Criar um recurso** (+) no canto superior esquerdo do portal.

   ![Criar um recurso no Portal do Azure](./media/aml-create-in-portal/portal-create-a-resource.png)

Digite **Machine Learning** na barra de pesquisa. Selecione o resultado de pesquisa denominado **Workspace do Machine Learning**.

   ![pesquisa de workspace](./media/aml-create-in-portal/allservices-search.PNG)

No painel **Workspace do Machine Learning**, role até a parte inferior e selecione **Criar** para começar.

   ![create](./media/aml-create-in-portal/portal-create-button.png)

No painel **Workspace do ML**, configure seu workspace.

   Campo|DESCRIÇÃO
   ---|---
   Nome do espaço de trabalho |Insira um nome único que identifique seu workspace.  Aqui usaremos docs-ws. Os nomes devem ser únicos em todo o grupo de recursos. Use um nome fácil de lembrar e de diferenciar dos workspaces criados por outros.  
   Assinatura |Escolha a assinatura do Azure que você deseja usar. Se você tiver várias assinaturas, escolha a que for adequada para a cobrança do recurso.
   Grupo de recursos | Use um grupo de recursos existente na sua assinatura ou insira um nome para criar um novo grupo de recursos. Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure.  Aqui usaremos docs-aml. 
   Local padrão | Escolha a localização mais próxima a seus usuários e recursos de dados. É nela em que o workspace é criado.

   ![criar workspace](./media/aml-create-in-portal/workspace-create.png)

Selecione **Criar** para iniciar o processo de criação.  Pode demorar um pouco para o workspace ser criado.

   Para verificar o status da implantação, selecione o ícone de notificações (sino) na barra de ferramentas.

   ![criar workspace](./media/aml-create-in-portal/notifications.png)

   Quando terminar, será exibida uma mensagem de êxito da implantação.  Ela também está presente na seção de notificações.   Clique no botão **Ir para o recurso** para exibir o novo workspace.
