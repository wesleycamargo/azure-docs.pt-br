---
title: Como gerenciar sua conta e chaves dos Mapas do Azure | Microsoft Docs
description: Você pode usar o portal do Azure para gerenciar sua conta dos Mapas do Azure e gerenciar suas chaves de acesso.
services: azure-maps
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
manager: timlt
ms.openlocfilehash: 091b91b51f7c880a1edbf65a0e2c83560d4bc38b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-manage-your-azure-maps-account-and-keys"></a>Como gerenciar sua conta e chaves dos Mapas do Azure

Você pode gerenciar sua conta e chaves dos Mapas do Azure através do portal do Azure. Depois que você tiver uma conta e uma chave, poderá implementar as APIs no site ou aplicativo móvel.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-new-account"></a>Criar uma nova conta

1. Entre no [Portal do Azure](http://portal.azure.com).

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.

2. Procure e selecione **Mapas** e, em seguida, clique em **Criar**.

3. Insira as informações para sua nova conta. 

![Inserir as informações da conta no portal](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>Gerenciar chaves na página da conta

Depois de criar uma conta, você obtém duas chaves geradas aleatoriamente. Você pode usar as chaves para autenticar as APIs dos Mapas quando você quiser recuperar os dados de mapa ou criar uma nova instância de mapa de JavaScript. 

É possível encontrar suas chaves no Portal do Azure. Navegue até a sua conta e selecione **Chaves** no menu.

![Gerenciar chaves de conta no portal](./media/how-to-manage-account-keys/account-keys-portal.png)

Nessa página, você pode copiar as chaves ou gerar novas. 

## <a name="delete-an-account"></a>Excluir uma conta

Você pode excluir uma conta no Portal do Azure. Navegue até a página de visão geral da conta e selecione **Excluir**.

![Excluir sua conta no portal](./media/how-to-manage-account-keys/account-delete-portal.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como usar a [API de gerenciamento de Mapas](https://docs.microsoft.com/rest/api/maps-management/accounts) para criar, atualizar e excluir contas de Mapas. 