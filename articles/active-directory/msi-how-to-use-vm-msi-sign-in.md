---
title: "Como usar uma Identidade de Serviço Gerenciado da VM do Azure para entrar"
description: "Instruções passo a passo sobre exemplos para usar uma entidade de serviço MSI da VM do Azure para entrada do cliente de script e acesso a recursos."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: bryanla
ms.openlocfilehash: 6c9e3ce4bbe33d06af64d97e1455ec20902d0ff4
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in"></a>Como usar uma MSI (Identidade de Serviço Gerenciado) da VM do Azure para entrar 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]  
Este artigo fornece exemplos de script do PowerShell e da CLI para se fazer logon usando uma entidade de serviço MSI e diretrizes sobre tópicos importantes, como o tratamento de erro.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Se você planeja usar os exemplos do Azure PowerShell ou da CLI do Azure neste artigo, instale a versão mais recente do [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) ou da [CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Todo os scripts de exemplo neste artigo pressupõem que o cliente de linha de comando está sendo executado em uma Máquina Virtual habilitada para MSI. Use o recurso "Conectar" da VM no Portal do Azure para conectar-se remotamente à sua VM. Para obter detalhes sobre como habilitar a MSI em uma VM, consulte [Configurar uma MSI (Identidade de Serviço Gerenciado) da VM usando o portal do Azure](msi-qs-configure-portal-windows-vm.md) ou um dos artigos variantes (o PowerShell, CLI, modelo ou SDK do Azure). 
> - Para evitar erros durante o acesso aos recursos, a MSI da VM deverá receber pelo menos acesso de "Leitor" no escopo apropriado (a VM ou superior) para permitir operações do Azure Resource Manager na VM. Consulte [Atribuir um acesso de MSI (Identidade de Serviço Gerenciado) a um recurso usando o portal do Azure](msi-howto-assign-access-portal.md) para obter detalhes.

## <a name="overview"></a>Visão geral

Uma MSI fornece uma [entidade de serviço](develop/active-directory-dev-glossary.md#service-principal-object), [criada após a habilitação da MSI](msi-overview.md#how-does-it-work) na VM. A entidade de serviço pode receber acesso aos recursos do Azure e pode ser usada como uma identidade pelos clientes de script/linha de comando para entrada e acesso aos recursos. Tradicionalmente, para acessar recursos protegidos em sua própria identidade, um cliente de script precisa:  

   - estar registrado e ter o consentimento do Azure AD como um aplicativo cliente Web/confidencial
   - entrar com sua entidade de serviço, usando as credenciais do aplicativo (que estão provavelmente inseridas no script)

Com a MSI, seu cliente de script não precisa mais fazer nenhuma das duas, uma vez que ele pode entrar com a entidade de serviço MSI. 

## <a name="azure-cli"></a>CLI do Azure

O script a seguir demonstra como:

1. Entrar no Azure AD com a entidade de serviço MSI da VM  
2. Chamar o Azure Resource Manager e obter a ID da entidade de serviço da VM. A CLI cuida do gerenciamento de uso/aquisição de token para você automaticamente. Substitua o nome da máquina virtual para `<VM-NAME>`.  

   ```azurecli
   az login --msi
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The MSI service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

O script a seguir demonstra como:

1. Adquirir um token de acesso da MSI para a VM.  
2. Usar o token de acesso para entrar no Azure AD com a entidade de serviço MSI correspondente.   
3. Chamar um cmdlet do Azure Resource Manager para obter informações sobre a VM. O PowerShell cuida do gerenciamento de uso de token para você automaticamente.  

   ```azurepowershell
   # Get an access token for the MSI
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                                 -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
   $content =$response.Content | ConvertFrom-Json
   $access_token = $content.access_token
   echo "The MSI access token is $access_token"

   # Use the access token to sign in under the MSI service principal. -AccountID can be any string to identify the session.
   Login-AzureRmAccount -AccessToken $access_token -AccountId "MSI@50342"

   # Call Azure Resource Manager to get the service principal ID for the VM's MSI. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The MSI service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>IDs de recurso para serviços do Azure

Consulte [Azure services that support Azure AD authentication](msi-overview.md#azure-services-that-support-azure-ad-authentication) (Serviços do Azure que dão suporte à autenticação do Azure AD) para obter uma lista dos recursos compatíveis com o Azure AD e que foram testados com a MSI e as respectivas IDs do recurso.

## <a name="error-handling-guidance"></a>Diretrizes sobre tratamento de erro 

Respostas como a seguinte podem indicar que a MSI da VM não foi configurada corretamente:

- PowerShell: *Invoke-WebRequest: não é possível conectar-se ao servidor remoto*
- CLI: *MSI: falha ao recuperar um token de 'http://localhost:50342/oauth2/token' com um erro de 'HTTPConnectionPool(host='localhost', port=50342)* 

Se você receber um desses erros, retorne para a VM do Azure no [Portal do Azure](https://portal.azure.com) e:

- Vá para a página **Configuração** e certifique-se de que "Identidade de serviço gerenciado" está definido como "Sim."
- Vá para a página **Extensões** e certifique-se de que a extensão da MSI foi implantada com êxito.

Se um deles estiver incorreto, reimplante o MSI em seu recurso ou solucione a falha de implantação. Consulte [Configure a VM Managed Service Identity (MSI) using the Azure portal](msi-qs-configure-portal-windows-vm.md) (Configurar uma MSI [Identidade de Serviço Gerenciado] da VM usando o Portal do Azure) se precisar de ajuda com a configuração da VM.

## <a name="related-content"></a>Conteúdo relacionado

- Para habilitar a MSI em uma VM do Azure, consulte [Configure a VM Managed Service Identity (MSI) using PowerShell](msi-qs-configure-powershell-windows-vm.md) (Configurar uma MSI (Identidade de Serviço Gerenciado) de VM usando o PowerShell) ou [Configure a VM Managed Service Identity (MSI) using Azure CLI](msi-qs-configure-cli-windows-vm.md) (Configurar uma MSI (Identidade de Serviço Gerenciado) de VM usando a CLI do Azure)

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.








