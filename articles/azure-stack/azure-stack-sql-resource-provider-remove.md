---
title: Removendo o provedor de recursos do SQL na pilha do Azure | Microsoft Docs
description: Saiba como você pode remover o provedor de recursos do SQL de sua implantação de pilha do Azure.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 9f90201cad0f74923460c2f25eff4de98dc6690a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294773"
---
# <a name="removing-the-mysql-resource-provider"></a>Removendo o provedor de recursos MySQL  
Antes de remover o provedor de recursos do SQL, é essencial para primeiro remova todas as dependências.

## <a name="remove-the-mysql-resource-provider"></a>Remover o provedor de recursos MySQL 

1. Verifique se você removeu quaisquer dependências de provedor de recursos existentes do SQL.

  > [!NOTE]
  > Desinstalar o provedor de recursos SQL continuará mesmo que os recursos dependentes estão usando atualmente o provedor de recursos. 
  
2. Certifique-se de que você tenha o pacote de implantação original que foi baixado para esta versão do adaptador de provedor de recursos do SQL.
3. Executar novamente o script de implantação usando os seguintes parâmetros:
    - Use-desinstalar o parâmetro
    - O endereço IP ou nome DNS do ponto de extremidade com privilégios.
    - A credencial do administrador da nuvem, necessário para acessar o ponto de extremidade com privilégios.
    - As credenciais para a conta de administrador de serviço de pilha do Azure. Use as mesmas credenciais que você usou para implantar a pilha do Azure.

## <a name="next-steps"></a>Próximas etapas
[Oferecer serviços de aplicativos como PaaS](azure-stack-app-service-overview.md)
