---
title: Identidade de Serviço Gerenciada para recursos do Azure
description: Uma visão geral do recurso Identidade de Serviço Gerenciada para recursos do Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 03/28/2018
ms.author: daveba
ms.openlocfilehash: 1edd6f846d539b1f263877a5e0af107148513c6e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398378"
---
# <a name="what-is-managed-service-identity-for-azure-resources"></a>O que é a Identidade de Serviço Gerenciada para recursos do Azure?

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Um desafio comum ao criar aplicativos de nuvem é como gerenciar as credenciais no código para autenticar serviços de nuvem. Proteger as credenciais é uma tarefa importante. O ideal é que as credenciais nunca apareçam em estações de trabalho do desenvolvedor e não sejam verificadas no controle de origem. O Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais, os segredos e outras chaves, mas seu código precisa se autenticar no Key Vault para recuperá-los. 

O recurso Identidade de Serviço Gerenciada no Azure AD (Azure Active Directory) resolve esse problema. O recurso oferece aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. Você pode usar a identidade para se autenticar em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.

O recurso Identidade de Serviço Gerenciada é gratuito com o Azure AD em assinaturas do Azure. Não há nenhum custo adicional.

## Como funciona o recurso?<a name="how-does-it-work"></a>

Há dois tipos de identidades gerenciadas:

- Uma **identidade atribuída pelo sistema** é habilitada diretamente em uma instância de serviço do Microsoft Azure. Quando a identidade é habilitada, o Azure cria uma identidade para a instância do locatário do Azure AD confiado pela assinatura da instância. Depois que a identidade é criada, as credenciais são provisionadas para a instância. O ciclo de vida de uma identidade atribuída ao sistema está diretamente relacionado à instância de serviço do Azure na qual ela está habilitada. Se a instância é excluída, o Azure limpa automaticamente as credenciais e a identidade no Azure AD.
- Uma **identidade atribuída pelo usuário** é criada como um recurso autônomo do Azure. Por meio de um processo de criação, o Microsoft Azure cria uma identidade no locatário do Azure AD confiado pela assinatura em uso. Depois que a identidade é criada, ela pode ser atribuída a uma ou mais instâncias de serviço do Azure. O ciclo de vida de uma identidade atribuída pelo usuário é gerenciado separadamente do ciclo de vida das instâncias de serviço do Azure a que ela é atribuída.

Seu código pode usar uma identidade de serviço gerenciada para solicitar tokens de acesso para os serviços que dão suporte à autenticação do Azure AD. O Azure é responsável por reverter as credenciais que são usadas pela instância de serviço.

O diagrama abaixo mostra como as identidades de serviço gerenciadas funcionam com VMs (máquinas virtuais):

![Identidades de serviço gerenciadas e VMs do Azure](media/overview/msi-vm-vmextension-imds-example.png)

### <a name="how-a-system-assigned-identity-works-with-an-azure-vm"></a>Como uma identidade atribuída pelo sistema funciona com uma VM do Azure

1. O Azure Resource Manager recebe uma mensagem para habilitar a identidade atribuída pelo sistema em uma máquina virtual.
2. O Azure Resource Manager cria uma entidade de serviço no Azure AD para a identidade da VM. A entidade de serviço é criada no locatário do Azure AD confiado pela assinatura.
3. O Azure Resource Manager configura a identidade na máquina virtual:
    1. Atualiza o ponto de extremidade de identidade do Serviço de Metadados de Instância com o certificado e a ID do cliente da entidade de serviço.
    1. Provisiona a extensão da VM e adiciona o certificado e a ID de cliente da entidade de serviço. (Esta etapa está programada para reprovação.)
4. Depois que a máquina virtual ganhar uma identidade, use as informações da entidade de serviço para conceder acesso aos recursos do Microsoft Azure à VM. Para chamar o Azure Resource Manager, use o RBAC (controle de acesso baseado em função) no Azure AD para atribuir a função apropriada à entidade de serviço da VM. Para chamar o Key Vault, conceda o acesso de código para o segredo ou chave específica no Key Vault.
5. O código em execução na máquina virtual pode solicitar um token de dois pontos de extremidade que só podem ser acessados de dentro da VM:

    - Ponto de extremidade de identidade do Serviço de Metadados de Instância do Azure (recomendado): `http://169.254.169.254/metadata/identity/oauth2/token`
        - O parâmetro de recurso especifica o serviço ao qual o token é enviado. Para se autenticar no Azure Resource Manager, use `resource=https://management.azure.com/`.
        - O parâmetro de versão de API Especifica a versão IMDS, use a versão da API = 2018-02-01 ou superior.
    - Ponto de extremidade da VM (programado para reprovação): `http://localhost:50342/oauth2/token` 
        - O parâmetro de recurso especifica o serviço ao qual o token é enviado. Para se autenticar no Azure Resource Manager, use `resource=https://management.azure.com/`.

6. A chamada é feita ao Azure AD para solicitar acesso ao token (conforme especificado na etapa 5) usando a ID do cliente e o certificado configurados na etapa 3. O Azure AD retorna um token de acesso do JSON Web Token (JWT).
7. Seu código envia o token de acesso em uma chamada para um serviço que dá suporte à autenticação do Azure AD.

### <a name="how-a-user-assigned-identity-works-with-an-azure-vm"></a>Como uma identidade atribuída pelo usuário funciona com uma VM do Azure

1. O Azure Resource Manager recebe uma mensagem para criar uma identidade atribuída pelo usuário.
2. O Azure Resource Manager cria uma entidade de serviço no Azure AD para a identidade atribuída pelo usuário. A entidade de serviço é criada no locatário do Azure AD confiado pela assinatura.
3. O Azure Resource Manager recebe uma solicitação para criar uma identidade atribuída pelo usuário em uma máquina virtual:
    1. Atualiza o ponto de extremidade de identidade do Serviço de Metadados de Instância com o certificado e a ID do cliente da entidade do serviço de identidade.
    1. Provisiona a extensão de VM e adiciona o certificado e a ID de cliente da entidade de serviço da identidade atribuída pelo usuário. (Esta etapa está programada para reprovação.)
4. Depois que a identidade atribuída pelo usuário for criada, use as informações da entidade de serviço para conceder o acesso de identidade aos recursos do Azure. Para chamar o Azure Resource Manager, use RBAC no Azure AD para atribuir a função apropriada à entidade de serviço da identidade atribuída pelo usuário. Para chamar o Key Vault, conceda o acesso de código para o segredo ou chave específica no Key Vault.

   > [!Note]
   > Você também pode executar essa etapa antes da etapa 3.

5. O código em execução na máquina virtual pode solicitar um token de dois pontos de extremidade que só podem ser acessados de dentro da VM:

    - Ponto de extremidade de identidade do Serviço de Metadados de Instância do Azure (recomendado): `http://169.254.169.254/metadata/identity/oauth2/token`
        - O parâmetro de recurso especifica o serviço ao qual o token é enviado. Para se autenticar no Azure Resource Manager, use `resource=https://management.azure.com/`.
        - O parâmetro de ID de cliente especifica a identidade para a qual o token é solicitado. Esse valor é necessário para eliminar a ambiguidade quando mais de uma identidade atribuída ao usuário está em uma mesma VM.
        - O parâmetro de versão da API especifica a versão do Serviço de Metadados de Instância do Azure. Use `api-version=2018-02-01` ou superior.

    - Ponto de extremidade da VM (programado para reprovação): `http://localhost:50342/oauth2/token`
        - O parâmetro de recurso especifica o serviço ao qual o token é enviado. Para se autenticar no Azure Resource Manager, use `resource=https://management.azure.com/`.
        - O parâmetro de ID de cliente especifica a identidade para a qual o token é solicitado. Esse valor é necessário para eliminar a ambiguidade quando mais de uma identidade atribuída ao usuário está em uma mesma VM.
6. A chamada é feita ao Azure AD para solicitar acesso ao token (conforme especificado na etapa 5) usando a ID do cliente e o certificado configurados na etapa 3. O Azure AD retorna um token de acesso do JSON Web Token (JWT).
7. Seu código envia o token de acesso em uma chamada para um serviço que dá suporte à autenticação do Azure AD.

## <a name="how-can-i-use-managed-service-identities"></a>Como usar as identidades de serviço gerenciadas?

Para saber como usar as identidades de serviço gerenciadas para acessar diferentes recursos do Azure, experimente estes tutoriais.

Saiba como usar uma identidade de serviço gerenciada com uma VM Windows:

* [Acessar Azure Data Lake Store](tutorial-windows-vm-access-datalake.md)
* [Acessar o Azure Resource Manager](tutorial-windows-vm-access-arm.md)
* [Acessar SQL do Azure](tutorial-windows-vm-access-sql.md)
* [Acessar o Armazenamento do Azure usando uma chave de acesso](tutorial-windows-vm-access-storage.md)
* [Acessar o Armazenamento do Azure usando assinaturas de acesso compartilhado](tutorial-windows-vm-access-storage-sas.md)
* [Acessar um recurso que não seja do Azure AD com o Azure Key Vault](tutorial-windows-vm-access-nonaad.md)

Saiba como usar uma identidade de serviço gerenciada com uma VM Linux:

* [Acessar Azure Data Lake Store](tutorial-linux-vm-access-datalake.md)
* [Acessar o Azure Resource Manager](tutorial-linux-vm-access-arm.md)
* [Acessar o Armazenamento do Azure usando uma chave de acesso](tutorial-linux-vm-access-storage.md)
* [Acessar o Armazenamento do Azure usando assinaturas de acesso compartilhado](tutorial-linux-vm-access-storage-sas.md)
* [Acessar um recurso que não seja do Azure AD com o Azure Key Vault](tutorial-linux-vm-access-nonaad.md)

Saiba como usar uma identidade de serviço gerenciada com outros serviços do Azure:

* [Serviço de Aplicativo do Azure](/azure/app-service/app-service-managed-service-identity)
* [Funções do Azure](/azure/app-service/app-service-managed-service-identity)
* [Barramento de Serviço do Azure](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Hubs de eventos do Azure](../../event-hubs/event-hubs-managed-service-identity.md)
* [Gerenciamento de API do Azure](../../api-management/api-management-howto-use-managed-service-identity.md)

## O recurso tem suporte de quais serviços do Azure?<a name="which-azure-services-support-managed-service-identity"></a>

As identidades de serviço gerenciadas podem ser usadas para autenticar os serviços que dão suporte à autenticação do Azure AD. Para obter uma lista de serviços do Azure que dão suporte ao recurso Identidade de Serviço Gerenciada, consulte [Serviços que dão suporte à Identidade de Serviço Gerenciada](services-support-msi.md).

## <a name="next-steps"></a>Próximas etapas

Comece a usar o recurso Identidade de Serviço Gerenciada do Azure com os seguintes inícios rápidos:

* [Usar uma identidade de serviço gerenciada da VM do Windows para acessar o Resource Manager](tutorial-windows-vm-access-arm.md)
* [Usar uma identidade de serviço gerenciada de VM Linux para acessar o Azure Resource Manager](tutorial-linux-vm-access-arm.md)
