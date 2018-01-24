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
ms.date: 01/05/2018
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c5f71d27a9e07cc6d6a260b809e91aaa2a50270c
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2018
---
# <a name="sign-in-using-a-vm-user-assigned-managed-service-identity-msi"></a>Entrar usando uma MSI (Identidade de Serviço Gerenciado) de VM atribuída pelo usuário

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)] Este artigo fornece exemplos de script da CLI para se conectar usando uma entidade de serviço da MSI atribuída pelo usuário e diretrizes sobre tópicos importantes, como tratamento de erro.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Para usar os exemplos da CLI do Azure neste artigo, instale a versão mais recente da [CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Todo os scripts de exemplo neste artigo pressupõem que o cliente de linha de comando está sendo executado em uma Máquina Virtual habilitada para MSI. Use o recurso "Conectar" da VM no Portal do Azure para conectar-se remotamente à sua VM. Para obter detalhes sobre como habilitar a MSI em uma VM, consulte [Configurar uma MSI (Identidade de Serviço Gerenciado) de VM usando a CLI do Azure](msi-qs-configure-cli-windows-vm.md) ou um dos artigos variantes (usando o PowerShell, o Portal, um modelo ou um SDK do Azure). 
> - Para evitar erros durante a conexão e o acesso aos recursos, a MSI deverá receber pelo menos acesso de "Leitor" no escopo apropriado (a VM ou superior) para permitir operações do Azure Resource Manager na VM. Consulte [Atribuir um acesso de MSI (Identidade de Serviço Gerenciado) a um recurso usando a CLI do Azure](msi-howto-assign-access-cli.md) para obter mais detalhes.

## <a name="overview"></a>Visão geral

Uma MSI fornece uma [entidade de serviço](~/articles/active-directory/develop/active-directory-dev-glossary.md#service-principal-object), [criada após a habilitação da MSI](msi-overview.md#how-does-it-work) na VM. A entidade de serviço pode receber acesso aos recursos do Azure e pode ser usada como uma identidade pelos clientes de script/linha de comando para conexão e acesso aos recursos. Tradicionalmente, para acessar recursos protegidos em sua própria identidade, um cliente de script precisa:  

   - estar registrado e ter o consentimento do Azure AD como um aplicativo cliente Web/confidencial
   - entrar com sua entidade de serviço, usando as credenciais do aplicativo (que estão provavelmente inseridas no script)

Com a MSI, seu cliente de script não precisa mais fazer nenhuma das duas, uma vez que ele pode entrar com a entidade de serviço MSI. 

## <a name="azure-cli"></a>CLI do Azure

O script a seguir demonstra como:

1. Entrar no Azure AD, na entidade de serviço da MSI atribuída pelo usuário.  
2. Chamar o Azure Resource Manager e obter o local da região do Azure de uma VM. A CLI cuida do gerenciamento de uso/aquisição de token para você automaticamente. Certifique-se de substituir o nome da sua VM por `<VM NAME>` e a ID de recurso da MSI atribuída pelo usuário por `<MSI ID>`. A ID de recurso da MSI é retornada na propriedade `id` durante a criação de uma MSI atribuída pelo usuário (consulte [Configurar uma MSI (Identidade de Serviço Gerenciado) atribuída pelo usuário para uma VM, usando a CLI do Azure](msi-qs-configure-cli-windows-vm.md) para obter exemplos do comando `az identity create`).

    ```azurecli
    az login --msi –u <MSI ID>
   
    vmLocation=$(az resource list -n <VM NAME> --query [*].location --out tsv)
    echo The VM region location is $vmLocation
    ```

    Respostas de exemplo:
   
    ```bash
    user@vmLinux:~$ az login --msi -u /subscriptions/80c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName
    [
      {
        "environmentName": "AzureCloud",
        "id": "90c696ff-5efa-4909-a64d-z1b616f423bl",
        "isDefault": true,
        "name": "MSIResource-/subscriptions/90c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName@50342",
        "state": "Enabled",
        "tenantId": "933a8f0e-ec41-4e69-8ad8-971zc4b533ll",
        "user": {
          "name": "userAssignedIdentity",
          "type": "servicePrincipal"
        }
      }
    ]  
    user@vmLinux:~$ vmLocation=$(az resource list -n vmLinux --query [*].location --out tsv)
    user@vmLinux:~$ echo The VM region location is $vmLocation
    The VM region location is westcentralus
    ```

## <a name="resource-ids-for-azure-services"></a>IDs de recurso para serviços do Azure

Consulte [Azure services that support Azure AD authentication](msi-overview.md#azure-services-that-support-azure-ad-authentication) (Serviços do Azure que dão suporte à autenticação do Azure AD) para obter uma lista dos recursos compatíveis com o Azure AD e que foram testados com a MSI e as respectivas IDs do recurso.

## <a name="error-handling-guidance"></a>Diretrizes sobre tratamento de erro 

As seguintes respostas podem indicar que a MSI não foi configurada corretamente:

- CLI: *MSI: falha ao recuperar um token de 'http://localhost:50342/oauth2/token' com um erro de 'HTTPConnectionPool(host='localhost', port=50342)* 

Se você receber um desses erros, retorne para a VM do Azure no [Portal do Azure](https://portal.azure.com) e:

- Vá para a página **Configuração** e certifique-se de que "Identidade de serviço gerenciado" está definido como "Sim."
- Vá para a página **Extensões** e certifique-se de que a extensão da MSI foi implantada com êxito.

Se um deles estiver incorreto, atribua novamente a MSI ao seu recurso ou solucione a falha de implantação. Consulte [Configurar uma MSI (Identidade de Serviço Gerenciado) de VM usando a CLI do Azure](msi-qs-configure-cli-windows-vm.md) se precisar de ajuda com a configuração da VM.

## <a name="next-steps"></a>Próximas etapas

- Para habilitar a MSI em uma VM do Azure, confira [Configurar uma MSI (Identidade de Serviço Gerenciado) de VM usando a CLI do Azure](msi-qs-configure-cli-windows-vm.md).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.








