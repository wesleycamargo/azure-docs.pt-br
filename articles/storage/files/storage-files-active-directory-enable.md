---
title: Ativar a autenticação do Active Directory do Azure em SMB para arquivos do Azure (visualização) - Armazenamento do Azure
description: Saiba como habilitar a autenticação baseada em identidade em SMB (Bloqueio de Mensagens do Servidor) (visualização) para os Arquivos do Azure por meio dos Serviços de Domínio do Azure AD (Azure Active Directory). As máquinas virtuais Windows (VMs) associadas ao domínio podem acessar os compartilhamentos de arquivos do Azure usando as credenciais do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/19/2018
ms.author: tamram
ms.openlocfilehash: 31bfcd99e23cc7fe0575a293e3dc22507dbd9e13
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973262"
---
# <a name="enable-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>Ativar a autenticação do Active Directory do Azure em SMB para arquivos do Azure (visualização)

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para obter uma visão geral da autenticação do AD do Azure em SMB para arquivos do Azure, consulte [Visão geral da autenticação do Active Directory do Azure em SMB para arquivos do Azure (visualização)](storage-files-active-directory-overview.md).

## <a name="workflow-overview"></a>Visão geral do fluxo de trabalho

Antes de habilitar o Azure AD em SMB para arquivos do Azure, verifique se os ambientes do Azure AD e do Armazenamento do Azure estão configurados corretamente. É recomendável que você analise os [pré-requisitos](#prerequisites) para ter certeza de que realizou todas as etapas necessárias. 

Em seguida, conceda acesso aos recursos do Azure Files com credenciais do Azure AD seguindo estas etapas: 

1. Ative a autenticação do Azure AD em SMB para que sua conta de armazenamento registre a conta de armazenamento com a implantação associada dos Serviços de Domínio do Azure AD.
2. Atribuir permissões de acesso para um compartilhamento a uma identidade do Azure AD (um usuário, grupo ou responsável pelo serviço).
3. Configurar permissões NTFS em SMB para diretórios e arquivos.
4. Montar um compartilhamento de arquivos do Azure de uma VM associada ao domínio.

O diagrama abaixo ilustra o fluxo de trabalho de ponta a ponta para habilitar a autenticação do Azure AD em SMB para arquivos do Azure. 

![Diagrama mostrando o fluxo de trabalho do Azure AD sobre SMB para arquivos do Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Pré-requisitos 

1.  **Selecione ou crie um locatário do Azure AD.**

    Você pode usar um inquilino novo ou existente para a autenticação do Azure AD em SMB. O locatário e o compartilhamento de arquivos que você deseja acessar devem estar associados à mesma assinatura.

    Para criar um novo locatário do Azure AD, você pode [adicionar um locatário do Azure AD e uma assinatura do Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Se você tiver um inquilino do Azure AD existente, mas quiser criar um novo inquilino para uso com os Arquivos do Azure, consulte [Criar um inquilino do Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Ative os Serviços de Domínio do Azure AD no locatário do Azure AD.**

    Para oferecer suporte à autenticação com credenciais do Azure AD, você deve habilitar os Serviços de Domínio do Azure AD para o seu locatário do Azure AD. Se você não for o administrador do locatário do Azure AD, entre em contato com o administrador e siga as orientações passo a passo para [Habilitar os Serviços de Domínio do Active Directory do Azure usando o portal do Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md).

    Normalmente leva cerca de 15 minutos para uma implantação do Azure AD Domain Services concluir. Verifique se o status de integridade dos Serviços de Domínio do AD do Azure exibe **Em Execução**, com a sincronização de senha ativada, antes de prosseguir para a próxima etapa.

3.  **Domine e associe uma VM do Azure aos Serviços de Domínio do Azure AD.**

    Para acessar um compartilhamento de arquivo usando as credenciais do Azure AD de uma VM, sua VM deve estar associada ao domínio aos Serviços de Domínio do Azure AD. Para obter mais informações sobre como ingressar no domínio de uma VM, consulte [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado](../../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal.md).

    > [!NOTE]
    > A autenticação do Azure AD em SMB com arquivos do Azure é suportada somente em VMs do Azure em execução nas versões do sistema operacional acima do Windows 7 ou do Windows Server 2008 R2.

4.  **Selecione ou crie um compartilhamento de arquivos do Azure.**

    Selecione um compartilhamento de arquivos novo ou existente associado à mesma assinatura que o seu locatário do Azure AD. Para obter informações sobre como criar um novo compartilhamento de arquivos, consulte [Criar um compartilhamento de arquivos no Azure Files](storage-how-to-create-file-share.md). 

    O locatário do Azure AD deve ser implantado em uma região com suporte para a visualização do Azure AD em vez de SMB. A pré-visualização está disponível em todas as regiões públicas, exceto: Oeste dos EUA, Oeste dos EUA 2, Sul dos EUA Central, Leste dos EUA, Leste dos EUA 2, Central dos EUA, Norte Central dos EUA, Leste da Austrália, Europa Ocidental e Norte da Europa.

    Para um desempenho ideal, a Microsoft recomenda que o compartilhamento de arquivos esteja na mesma região da VM da qual você planeja acessar o compartilhamento.

5.  **Verifique a conectividade de arquivos do Azure montando compartilhamentos de arquivos do Azure usando a chave da sua conta de armazenamento.**

    Para verificar se sua VM e seu compartilhamento de arquivos estão configurados corretamente, tente montar o compartilhamento de arquivos usando sua chave de conta de armazenamento. Para obter mais informações, consulte [Montar um compartilhamento de arquivos do Azure e acessar o compartilhamento no Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-authentication"></a>Habilitar a autenticação do Azure AD

Depois de concluir os [pré-requisitos](#prerequisites), você poderá habilitar a autenticação do AD do Azure via SMB.

### <a name="step-1-enable-azure-ad-authentication-over-smb-for-your-storage-account"></a>Etapa 1: habilite a autenticação do Azure AD em SMB para sua conta de armazenamento

Para habilitar a autenticação do Azure AD em SMB para Azure Files, você pode definir uma propriedade nas contas de armazenamento criadas após 29 de agosto de 2018, usando o Provedor de Recursos de Armazenamento do Azure do PowerShell ou CLI do Azure. A configuração da propriedade no portal do Azure não é suportada para a versão de visualização. 

A configuração dessa propriedade registra a conta de armazenamento com a implantação dos Serviços de Domínio do Azure AD associados. A autenticação do AD do Azure em SMB é habilitada para todos os compartilhamentos de arquivos novos e existentes na conta de armazenamento. 

Lembre-se de que você pode habilitar a autenticação do Azure AD somente em SMB após ter implantado com êxito os Serviços de Domínio do Azure AD em seu locatário do Azure AD. Para obter mais informações, consulte o [pré-requisitos](#prerequisites).

**Powershell**

Para habilitar a autenticação do AD do Azure no SMB, instale o `AzureRM.Storage 6.0.0-preview` módulo do PowerShell. Para obter informações sobre como instalar o PowerShell, consulte [Instalar o Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Em seguida, chame [Set-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/set-azurermstorageaccount) e defina o parâmetro **EnableAzureFilesAadIntegrationForSMB** como **verdadeiro**. No exemplo abaixo, lembre-se de substituir os valores de espaço reservado por seus próprios valores.

```powershell
# Create a new storage account
New-AzureRmStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureFilesAadIntegrationForSMB $true

# Update an existing storage account
# Supported for storage accounts created after August 29, 2018 only
Set-AzureRmStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureFilesAadIntegrationForSMB $true```
```

**CLI**

"Você precisa primeiro adicionar a extensão de visualização de armazenamento e, em seguida, seguir o exemplo. Lembre-se de substituir os valores de espaço reservado por seus próprios valores. Consulte este artigo para obter mais informações sobre como adicionar extensões CLI do Azure. "

Para habilitar a autenticação do Azure AD em SMB do Azure CLI 2.0, primeiro instale a extensão *storage preview*:

```azurecli-interactive
az extension add --name storage-preview
```

Em seguida, chame [atualização de conta de armazenamento az](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) e defina o `--file-aad` propriedade **verdadeiro**. No exemplo abaixo, lembre-se de substituir os valores de espaço reservado por seus próprios valores.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true

# Update an existing storage account
# Supported for storage accounts created after August 29, 2018 only
az storage account update -n <storage-account-name> -g <resource-group-name> --file-aad true
```

### <a name="step-2-assign-access-permissions-to-an-identity"></a>Etapa 2: atribua permissões de acesso a uma identidade 

Para acessar os recursos do Azure Files usando credenciais do Azure AD, uma identidade (um usuário, grupo ou serviço principal) deve ter as permissões necessárias no nível do compartilhamento. A orientação passo a passo abaixo demonstra como atribuir permissões de leitura, gravação ou exclusão para um compartilhamento de arquivos a uma identidade.

> [!IMPORTANT]
> O controle administrativo total de um compartilhamento de arquivos, incluindo a capacidade de atribuir uma função a uma identidade, requer o uso da chave da conta de armazenamento. O controle administrativo não é suportado com credenciais do Azure AD. 

#### <a name="step-21-define-a-custom-role"></a>Etapa 2.1: Definir uma função personalizada

Para conceder permissões no nível do compartilhamento, defina uma função RBAC personalizada e atribua-a a uma identidade, definindo-a para um compartilhamento de arquivo específico. Esse processo é semelhante à especificação de permissões do Windows Share, em que você especifica o tipo de acesso que um determinado usuário tem a um compartilhamento de arquivos.  

Os modelos mostrados nas seções a seguir fornecem as permissões Ler ou Alterar para um compartilhamento de arquivos. Para definir uma função personalizada, crie um arquivo JSON e copie o modelo apropriado para esse arquivo. Para obter mais informações sobre como definir funções personalizadas do RBAC, consulte [Funções personalizadas no Azure](../../role-based-access-control/custom-roles.md).

**Definição de função para permissões de alteração em nível de compartilhamento**

O modelo de função personalizado a seguir fornece permissões de alteração no nível do compartilhamento, concedendo acesso de leitura, gravação e exclusão de identidade ao compartilhamento.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read, write and delete access to Azure File Share",
  "Actions": [
    "*"
  ],
  "NotActions": [
      "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [
    "*"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

**Definição de função para permissões de leitura em nível de compartilhamento**

O modelo de função personalizado a seguir fornece permissões de leitura no nível do compartilhamento, concedendo um acesso de leitura de identidade ao compartilhamento.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure File Share",
  "Actions": [
    "*/read"
  ],
  "DataActions": [
    "*/read"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

#### <a name="step-22-create-the-custom-role-and-assign-it-to-the-target-identity"></a>Etapa 2.2: crie a função personalizada e atribua-a à identidade de destino

Em seguida, use o PowerShell ou a CLI do Azure para criar a função e atribuí-la a uma identidade do Azure AD. 

**Powershell**

Para habilitar a autenticação do AD do Azure no SMB, instale o `AzureRM.Storage 6.0.0-preview` módulo do PowerShell. Para obter informações sobre como instalar o PowerShell, consulte [Instalar o Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

O seguinte comando do PowerShell cria uma função personalizada e atribui a função a uma identidade do Azure AD, com base no nome de entrada. Para obter mais informações sobre como atribuir funções do RBAC ao PowerShell, consulte [Gerenciar acesso usando o RBAC e o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Ao executar o exemplo de script a seguir, lembre-se de substituir os valores de marcador por seus próprios valores.

```powershell
#Create a custom role based on the sample template above
New-AzureRmRoleDefinition -InputFile "<custom-role-def-json-path>"
#Get the name of the custom role
$FileShareContributorRole = Get-AzureRmRoleDefinition "<role-name>"
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzureRmRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

**CLI**

O comando CLI 2.0 a seguir cria uma função personalizada e atribui a função a uma identidade do Azure AD, com base no nome de entrada. Para obter mais informações sobre como atribuir funções do RBAC à CLI do Azure, consulte [Gerenciar acesso usando o RBAC e o Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

Ao executar o exemplo de script a seguir, lembre-se de substituir os valores de marcador por seus próprios valores.

```azurecli-interactive
#Create a custom role based on the sample templates above
az role definition create --role-definition "<Custom-role-def-JSON-path>"
#List the custom roles
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
#Assign the custom role to the target identity
az role assignment create --role "<custome-role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

### <a name="step-3-configure-ntfs-permissions-over-smb"></a>Etapa 3: Configurar permissões NTFS em SMB 

Depois de atribuir permissões no nível de compartilhamento com o RBAC, você deve atribuir permissões de NTFS corretas no nível raiz, de diretório ou de arquivo. Pense nas permissões de nível de compartilhamento como o gatekeeper de alto nível que determina se um usuário pode acessar o compartilhamento, enquanto as permissões de NTFS agem em um nível mais granular para determinar quais operações o usuário pode executar no nível de diretório ou arquivo. 

Os arquivos do Azure dá suporte a todo o conjunto de permissões de NTFS básicos e avançados. Você pode exibir e configurar permissões NTFS em diretórios e arquivos em um compartilhamento de arquivos do Azure, montando o compartilhamento e, em seguida, executando o comando [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) ou [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) do Windows. 

> [!NOTE]
> A versão de visualização dá suporte a permissões de exibição somente com o Explorador de arquivos do Windows. Ainda não há suporte para a edição de permissões.

Para configurar permissões NTFS com privilégios de superusuário, você deve montar o compartilhamento com sua chave de conta de armazenamento de sua VM ingressado no domínio. Siga as instruções na próxima seção para montar um compartilhamento de arquivos do Azure do prompt de comando e configurar permissões NTFS apropriadas.

Conjuntos de permissões a seguir têm suporte para o diretório raiz de um compartilhamento de arquivos:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(Rx)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

#### <a name="step-31-mount-an-azure-file-share-from-the-command-prompt"></a>Etapa 3.1 Montar um compartilhamento de arquivos do Azure no prompt de comando

Usar o Windows **net use** comando para montar o compartilhamento de arquivos do Azure. Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores. Para obter mais informações sobre a montagem de compartilhamentos de arquivos, consulte [Montar um compartilhamento de arquivos do Azure e acessar o compartilhamento no Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

#### <a name="step-32-configure-ntfs-permissions-with-icacls"></a>Permissões de NTFS configurar etapa 3.2 com icacls

Use o seguinte comando do Windows para conceder permissões completas para todos os diretórios e arquivos no compartilhamento de arquivos, incluindo o diretório raiz. Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores.

```
icacls <mounted-drive-letter> /grant <user-email>:(f)
```

Para obter mais informações sobre como usar icacls para configurar permissões NTFS e sobre o tipo diferente de permissões suportadas, consulte a [referência de linha de comando para icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

### <a name="step-4-mount-an-azure-file-share-from-a-domain-joined-vm"></a>Etapa 4: montar um compartilhamento de arquivos do Azure de uma VM associada ao domínio 

Agora você está pronto para verificar se concluiu as etapas acima usando as credenciais do Azure AD para acessar um compartilhamento de arquivos do Azure a partir de uma VM associada ao domínio. Primeiro, faça login na VM usando a identidade do Azure AD para a qual você concedeu permissões, conforme mostrado na imagem a seguir.

![Captura de tela mostrando a tela de entrada do Azure AD para autenticação do usuário](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Em seguida, use o seguinte comando para montar o compartilhamento de arquivos do Azure. Lembre-se de substituir os valores de espaço reservado por seus próprios valores. Como você já foi autenticado, não é necessário fornecer a chave da conta de armazenamento ou o nome de usuário e a senha do Azure AD. O Azure AD sobre SMB oferece suporte a uma experiência de logon único usando as credenciais do Azure AD.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Agora você habilitou com êxito a autenticação do Azure AD em SMB e atribuiu uma função personalizada que fornece acesso a um compartilhamento de arquivos para uma identidade do Azure AD. Para conceder acesso ao seu compartilhamento de arquivos para usuários adicionais, siga as instruções fornecidas na etapa 2.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os arquivos do Azure e usar o Azure AD sobre o SMB, consulte estes recursos:

- [Introdução aos Arquivos do Azure](storage-files-introduction.md)
- [Visão geral da autenticação do Active Directory do Azure em SMB para arquivos do Azure (visualização)](storage-files-active-directory-overview.md)
- [Perguntas frequentes](storage-files-faq.md)