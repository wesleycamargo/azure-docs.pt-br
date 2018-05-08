---
title: Vincular conta do Azure para ID de parceiro | Microsoft Docs
description: Acompanhar os contratos com clientes do Azure por meio da vinculação de ID de parceiro à conta de usuário que você usa para gerenciar recursos do cliente.
services: billing
author: dhirajgandhi
ms.author: dhgandhi
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: b135f62e9cb7946dfda106640497850716410a29
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2018
---
# <a name="link-partner-id-to-your-azure-accounts"></a>Vincular ID de parceiro a suas contas do Azure 
Como parceiro, você pode controlar o impacto em seus contratos de clientes por meio da vinculação de sua ID de parceiro às contas usadas para gerenciar recursos do cliente.

Esse recurso está disponível em uma visualização pública. 

## <a name="get-access-from-your-customer"></a>Obter o acesso do cliente 
Antes de vincular sua ID de parceiro, o cliente deve oferecer acesso a seus recursos do Azure, usando uma das seguintes opções:

- **Usuário convidado:** seu cliente pode adicioná-lo como usuário convidado e atribuir funções RBAC. Para obter mais informações, consulte [Adicionar usuários convidados de outro diretório](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Conta de diretório:** seu cliente pode criar um novo usuário de sua organização em seu diretório e atribuir qualquer função RBAC. 

- **Entidade de serviço:** seu cliente pode adicionar um aplicativo ou script de sua organização em seu diretório e atribuir qualquer função RBAC. A identidade do aplicativo ou script é conhecida como entidade de serviço.

## <a name="link-partner-id"></a>Vincular ID do parceiro
Quando você tem acesso aos recursos do cliente, use o PowerShell ou a CLI para vincular sua ID da Microsoft Partner Network (ID da MPN) à sua ID de usuário ou entidade de serviço. Você tem de vincular a ID de parceiro a cada locatário do cliente. 

### <a name="use-powershell-to-link-new-partner-id"></a>Use o PowerShell para vincular a nova ID de parceiro

1. Instalar o módulo do Azure PowerShell [AzurePartnerRP](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner/0.1.0-preview).

2. Faça logon no locatário do cliente com a conta de usuário ou entidade de serviço. Para obter mais informações, consulte [Logon com o Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. Vincular a nova ID de parceiro. A ID do parceiro é a ID do [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) da sua organização.

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>Obter a ID de parceiro vinculada
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>Atualizar a ID de parceiro vinculada
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>Excluir a ID de parceiro vinculada
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-cli-to-link-new-partner-id"></a>Usar a CLI para vincular a nova ID de parceiro
1.  Instalar a Extensão de CLI.

    ```azure-cli
    C:\ az extension add --name managementpartner
    ``` 

2.  Faça logon no locatário do cliente com a conta de usuário ou a entidade de serviço. Para obter mais informações, consulte [Entrar com a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azure-cli
    C:\ az login --tenant <tenant>
    ``` 


3.  Vincular a nova ID de parceiro. A ID do parceiro é a ID do [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) da sua organização.

     ```azure-cli
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Obter a ID de parceiro vinculada
```azure-cli
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>Atualizar a ID de parceiro vinculada
```azure-cli
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>Excluir a ID de parceiro vinculada
```azure-cli
C:\ az managementpartner delete --partner-id 12345
``` 


## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Quem pode vincular a ID de parceiro?**

Qualquer usuário da organização do parceiro que esteja gerenciando o recurso do cliente pode vincular a ID de parceiro à conta.
  

**Depois que uma ID de parceiro é vinculada, ela pode ser alterada?**

Sim, a ID de parceiro vinculada pode ser alterada, adicionada ou removida.

**E se um usuário tiver uma conta em vários locatários do cliente?**

A vinculação entre a ID de parceiro e a conta é feita para cada locatário do cliente.  Você tem de vincular a ID de parceiro em cada locatário do cliente.

**Outro parceiro ou cliente pode editar ou remover a vinculação à ID de parceiro?**

A vinculação está associada no nível da conta. Só você pode editar ou remover a vinculação à ID de parceiro. O cliente e o outros parceiros não podem alterar a vinculação à ID de parceiro. 


