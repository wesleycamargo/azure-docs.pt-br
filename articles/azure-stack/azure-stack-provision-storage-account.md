---
title: Contas de armazenamento na pilha do Azure | Microsoft Docs
description: Saiba como criar uma conta de armazenamento do Azure pilha.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
ms.assetid: e1152110-b756-4c1a-9fa2-73fe3ab0ad8e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: a599d809ba3da8487a6c5d115bf04922a546e6ad
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2018
---
# <a name="storage-accounts-in-azure-stack"></a>Contas de armazenamento no Azure Stack
As contas de armazenamento incluem serviços Blob e Tabela e um namespace exclusivo para os objetos de dados de armazenamento. Por padrão, os dados em sua conta estão disponíveis somente para você, o proprietário da conta de armazenamento.

1. No computador de POC de pilha do Azure, faça logon no `https://adminportal.local.azurestack.external` como [administrador](azure-stack-connect-azure-stack.md)e, em seguida, clique em **novo** > **dados + armazenamento**  >  **Conta de armazenamento**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. No **criar conta de armazenamento** folha, digite um nome para sua conta de armazenamento. Criar um novo **grupo de recursos**, ou selecione um existente e clique em **criar** para criar a conta de armazenamento.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Para ver sua nova conta de armazenamento, clique em **todos os recursos**, em seguida, procure a conta de armazenamento e clique em seu nome.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Próximas etapas
[Usar modelos do Azure Resource Manager](user/azure-stack-arm-templates.md)

[Saiba mais sobre contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md)

[Baixar o guia de validação de armazenamento do Azure consistente de pilha do Azure](http://aka.ms/azurestacktp1doc)
