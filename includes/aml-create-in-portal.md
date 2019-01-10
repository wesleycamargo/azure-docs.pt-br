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
ms.openlocfilehash: 6f73b15ed16cfe26bf14e60a5206568e1a1564fd
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53594192"
---
Entre no [portal do Microsoft Azure](https://portal.azure.com/) usando as credenciais de assinatura do Azure que você usará. 

Painel de workspace do portal tem suporte nos navegadores Microsoft Edge, Chrome e Firefox apenas.

   ![Portal do Azure](./media/aml-create-in-portal/portal-dashboard.png)

No canto superior esquerdo do Portal, selecione **Criar um recurso**.

   ![Criar um recurso no Portal do Azure](./media/aml-create-in-portal/portal-create-a-resource.png)

Na barra de pesquisa, digite **Machine Learning**. Selecione o resultado da pesquisa do **Espaço de trabalho de serviço de Machine Learning**.

   ![Pesquisa de workspace](./media/aml-create-in-portal/allservices-search.PNG)

No painel **Workspace do Machine Learning**, role até a parte inferior e selecione **Criar** para começar.

   ![Criar](./media/aml-create-in-portal/portal-create-button.png)

No painel **Espaço de trabalho de serviço ML**, configure seu espaço de trabalho.

   Campo|DESCRIÇÃO
   ---|---
   Nome do workspace |Insira um nome único que identifique seu workspace. Para este exemplo, usamos **docs-ws**. Os nomes devem ser únicos em todo o grupo de recursos. Use um nome que seja fácil de lembrar e diferenciar de espaços de trabalho criados por outras pessoas.  
   Assinatura |Selecione a assinatura do Azure que você deseja usar.
   Grupo de recursos | Use um grupo de recursos existente na sua assinatura ou insira um nome para criar um novo grupo de recursos. Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. Para este exemplo, usamos **docs-aml**. 
   Local padrão | Selecione a localização mais próxima a seus usuários e recursos de dados. Este local é onde o local de trabalho é criado.

   ![Criar workspace](./media/aml-create-in-portal/workspace-create.png)

Para iniciar o processo de criação, selecione **Criar**. Pode demorar um pouco para o workspace ser criado.

Para verificar o status da implantação, selecione o ícone de notificações **sino** na barra de ferramentas.

   ![Status de criação do espaço de trabalho](./media/aml-create-in-portal/notifications.png)

Quando o processo é finalizado, será exibida uma mensagem de êxito da implantação. Ele também está presente na seção notificações. Para exibir o novo espaço de trabalho, selecione **Ir para o recurso**.
