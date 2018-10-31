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
ms.openlocfilehash: 0b95441fd2805308c601509f1afc477f72bde321
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49476373"
---
Entre no [portal do Microsoft Azure](https://portal.azure.com/) usando as credenciais de assinatura do Azure que você usará. Caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

Painel de workspace do portal tem suporte nos navegadores Microsoft Edge, Chrome e Firefox apenas.

   ![Portal do Azure](./media/aml-create-in-portal/portal-dashboard.png)

No canto superior esquerdo do Portal, selecione **Criar um recurso**.

   ![Criar um recurso no Portal do Azure](./media/aml-create-in-portal/portal-create-a-resource.png)

Na barra de pesquisa, digite **Machine Learning**. Selecione o resultado da pesquisa do **Espaço de trabalho de serviço de Machine Learning**.

   ![Pesquisa de espaço de trabalho](./media/aml-create-in-portal/allservices-search.PNG)

No painel **Espaço de trabalho do Machine Learning**, role até a parte inferior e selecione **Criar** para começar.

   ![Criar](./media/aml-create-in-portal/portal-create-button.png)

No painel **Espaço de trabalho de serviço ML**, configure seu espaço de trabalho.

   Campo|DESCRIÇÃO
   ---|---
   Nome do workspace |Insira um nome único que identifique seu workspace. Aqui usaremos docs-ws. Os nomes devem ser únicos em todo o grupo de recursos. Use um nome que seja fácil de lembrar e diferenciar de espaços de trabalho criados por outras pessoas.  
   Assinatura |Selecione a assinatura do Azure que você deseja usar.
   Grupo de recursos | Use um grupo de recursos existente na sua assinatura ou insira um nome para criar um novo grupo de recursos. Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. Aqui usamos docs-aml. 
   Local padrão | Selecione a localização mais próxima a seus usuários e recursos de dados. Este local é onde o local de trabalho é criado.

   ![Criar workspace](./media/aml-create-in-portal/workspace-create.png)

Para iniciar o processo de criação, selecione **Criar**. Pode demorar um pouco para o workspace ser criado.

Para verificar o status da implantação, selecione o ícone de notificações (sino) na barra de ferramentas.

   ![Status de criação do espaço de trabalho](./media/aml-create-in-portal/notifications.png)

Quando o processo é finalizado, será exibida uma mensagem de êxito da implantação. Ele também está presente na seção notificações. Para exibir o novo espaço de trabalho, selecione **Ir para o recurso**.
