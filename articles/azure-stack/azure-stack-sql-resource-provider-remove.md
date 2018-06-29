---
title: Removendo o provedor de recursos do SQL na pilha do Azure | Microsoft Docs
description: Saiba como remover o provedor de recursos do SQL de sua implantação de pilha do Azure.
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
ms.date: 06/27/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b73deebb10d0c81a06df9cd192eaa2ef28de744d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083031"
---
# <a name="remove-the-sql-resource-provider"></a>Remover o provedor de recursos do SQL

Antes de remover o provedor de recursos do SQL, você deve remover todas as dependências do provedor. Você também precisará de uma cópia do pacote de implantação que foi usada para instalar o provedor de recursos.

Há várias tarefas de limpeza para fazer antes de executar o _DeploySqlProvider.ps1_ script para remover o provedor de recursos.
Os locatários são responsáveis pelas seguintes tarefas de limpeza:

* Exclua todos os seus bancos de dados do provedor de recursos. (Excluir os bancos de dados de locatário não exclui a dados).
* Cancelar o registro do namespace de provedor de recursos.

O administrador é responsável para as seguintes tarefas de limpeza:

* Exclui os servidores de hospedagem do provedor de recursos do SQL.
* Exclui todos os planos que referenciam o provedor de recursos do SQL.
* Exclui todas as cotas que estão associadas com o provedor de recursos do SQL.

## <a name="to-remove-the-sql-resource-provider"></a>Para remover o provedor de recursos do SQL

1. Verifique se você removeu todas as existentes SQL provedor dependências do recurso.

   > [!NOTE]
   > Desinstalar o provedor de recursos SQL continuará mesmo que os recursos dependentes estão usando atualmente o provedor de recursos.
  
2. Obtenha uma cópia do provedor de recursos SQL binário e, em seguida, execute o Self-extractor para extrair o conteúdo para um diretório temporário.

3. Abra uma janela de console novo com privilégios elevada do PowerShell e altere o diretório onde você extraiu os arquivos binários do provedor de recursos SQL.

4. Execute o script DeploySqlProvider.ps1 usando os seguintes parâmetros:

    * **Desinstalar**. Remove o provedor de recursos e todos os respectivos recursos.
    * **PrivilegedEndpoint**. O endereço IP ou nome DNS do ponto de extremidade com privilégios.
    * **CloudAdminCredential**. A credencial do administrador da nuvem, necessária para acessar o ponto de extremidade com privilégios.
    * **AzCredential**. A credencial para a conta de administrador de serviço de pilha do Azure. Use as mesmas credenciais que você usou para implantar a pilha do Azure.

## <a name="next-steps"></a>Próximas etapas

[Oferecer serviços de aplicativos como PaaS](azure-stack-app-service-overview.md)
