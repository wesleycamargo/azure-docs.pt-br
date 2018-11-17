---
title: Removendo o provedor de recursos do MySQL no Azure Stack | Microsoft Docs
description: Saiba como você pode remover o provedor de recursos MySQL da sua implantação do Azure Stack.
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
ms.date: 11/14/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: ca5322c3c874c434f16a42900227a47245851b02
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854026"
---
# <a name="remove-the-mysql-resource-provider"></a>Remover o provedor de recursos do MySQL

Antes de remover o provedor de recursos do MySQL, você deve remover todas as dependências do provedor. Você também precisará de uma cópia do pacote de implantação que foi usado para instalar o provedor de recursos.

> [!NOTE]
> Você pode encontrar os links de download para o recurso de instaladores de provedor na [implantar os pré-requisitos do provedor de recursos](.\azure-stack-mysql-resource-provider-deploy.md#prerequisites).

## <a name="dependency-cleanup"></a>Limpeza de dependência

Há várias tarefas de limpeza antes de executar o script DeployMySqlProvider.ps1 para remover o provedor de recursos.

Os usuários de locatário do Azure Stack serão responsáveis para as seguintes tarefas de limpeza:

* Exclua todos os seus bancos de dados do provedor de recursos. (Excluir os bancos de dados de locatário não excluirá os dados.)
* Cancelar o registro do namespace do provedor.

O operador de pilha do Azure é responsável para as seguintes tarefas de limpeza:

* Exclui os servidores de hospedagem do adaptador de MySQL.
* Exclui todos os planos que referenciam o adaptador do MySQL.
* Exclui todas as cotas que estão associadas com o adaptador do MySQL.

## <a name="to-remove-the-mysql-resource-provider"></a>Para remover o provedor de recursos do MySQL

1. Verifique se você removeu todas as MySQL recurso provedor dependências existentes.

   >[!NOTE]
   >Desinstalar o provedor de recursos MySQL continuará mesmo que os recursos dependentes estiver atualmente usando o provedor de recursos.
  
2. Obtenha uma cópia do provedor de recursos MySQL binário e, em seguida, execute o Self-extractor para extrair o conteúdo para um diretório temporário.
3. Obtenha uma cópia do provedor de recursos SQL binário e, em seguida, execute o Self-extractor para extrair o conteúdo para um diretório temporário.
4. Abra uma janela de console novo com privilégios elevada do PowerShell e altere o diretório onde você extraiu os arquivos binários de provedor de recursos MySQL.
5. Execute o script DeployMySqlProvider.ps1 usando os seguintes parâmetros:
    - **Desinstalar**. Remove o provedor de recursos e todos os recursos associados.
    - **PrivilegedEndpoint**. O endereço IP ou nome DNS do ponto de extremidade com privilégios.
    - **AzureEnvironment**. O ambiente do Azure usado para implantar o Azure Stack. Necessário apenas para implantações do AD do Azure.
    - **CloudAdminCredential**. A credencial do administrador da nuvem, necessária para acessar o ponto de extremidade com privilégios.
    - **DirectoryTenantID**
    - **AzCredential**. A credencial para a conta de administrador de serviço do Azure Stack. Use as mesmas credenciais que você usou para implantar o Azure Stack.

## <a name="next-steps"></a>Próximas etapas

[Oferta de serviços de aplicativo como PaaS](azure-stack-app-service-overview.md)
