---
title: Execute os comandos da CLI do Azure ou PowerShell sob uma identidade do Microsoft Azure Active Directory para acessar o Armazenamento Microsoft do Azure (visualização) | Microsoft Docs
description: A CLI do Azure e o PowerShell oferecem suporte para registros com uma identidade do Microsoft Azure Active Directory para executar comandos em contêineres de Armazenamento do Microsoft Azure e filas e seus dados. Um token de acesso é fornecido para a sessão e usado para autorizar operações de chamada. Permissões dependem da função atribuída a identidade do Microsoft Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 345e7c6985f03081048019912d636bba8e9a2361
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426474"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Use uma identidade do Microsoft Azure Active Directory para acessar o Armazenamento do Microsoft Azure com CLI ou PowerShell (visualização)

O Armazenamento do Microsoft Azure fornece extensões de visualização para CLI do Azure e do PowerShell que permitem que você faça logon e execute comandos de script sob uma identidade do Microsoft Azure Active Directory (Azure AD). A identidade do Azure AD pode ser um usuário, grupo, entidade de serviço de aplicativo ou pode ser uma [identidade gerenciada para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). Você pode atribuir permissões para acessar recursos de armazenamento à identidade o Microsoft Azure Active Directory através do controle de acesso com base em função (RBAC). Para obter mais informações sobre as funções RBAC no Armazenamento do Microsoft Azure, consulte [Gerenciar direitos de acesso a dados de Armazenamento com RBAC (versão prévia)](storage-auth-aad-rbac.md).

Ao realizar logon na CLI do Azure ou PowerShell com uma identidade do Microsoft Azure Active Directory, um token de acesso é devolvido para acessar o Armazenamento do Microsoft Azure com essa identidade. Esse token é usado automaticamente pela CLI ou o PowerShell para autorizar operações no Armazenamento do Microsoft Azure. Para operações com suporte, você não precisa passar uma chave de conta ou token SAS com o comando.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="supported-operations"></a>Operações com suporte

As extensões de versão prévia têm suporte para operações em contêineres e filas. As operações que você pode chamar dependem de permissões concedidas para a identidade do Microsoft Azure Active Directory com o qual você fez logon na CLI do Azure ou PowerShell. Permissões para contêineres de Armazenamento do Microsoft Azure ou filas são atribuídas por meio do controle de acesso baseado em função (RBAC). Por exemplo, se uma função de Leitor de Dados é atribuída para a identidade, você pode executar comandos de script que leem dados de uma fila ou um contêiner. Se uma função do Contribuidor de Dados é atribuída para a identidade, você pode executar comandos de script que ler, gravar ou excluir um contêiner ou fila ou os dados que eles contêm. 

Para obter detalhes sobre as permissões para cada operação de Armazenamento do Microsoft Azure em um contêiner ou fila, veja [Permissões para chamar operações REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).  

## <a name="call-cli-commands-with-an-azure-ad-identity"></a>Chame os comandos de CLI com uma identidade do Microsoft Azure Active Directory

Para instalar a extensão da versão prévia para a CLI do Azure:

1. Certifique-se de que tenha a CLI do Azure versão 2.0.32 ou posterior. Execute `az --version` para verificar sua versão instalada.
2. Execute o seguinte comando para instalar a extensão de versão prévia: 

    ```azurecli
    az extension add -n storage-preview
    ```

A extensão de versão prévia adiciona um novo `--auth-mode` parâmetro para comandos com suporte:

- Defina o `--auth-mode` parâmetro `login` para entrar usando uma identidade do Microsoft Azure Active Directory.
- Defina o `--auth-mode` parâmetro o valor `key` herdado para tentar consultar uma chave de conta, se nenhum parâmetro de autenticação para a conta for fornecido. 

Por exemplo, para fazer o download de um blob na CLI do Azure usando uma identidade do Microsoft Azure Active Directory, primeiro execute `az login`, em seguida, chame o comando com `--auth-mode` definido como `login`:

```azurecli
az login
az storage blob download --account-name storagesamples --container sample-container --name myblob.txt --file myfile.txt --auth-mode login 
```

A variável de ambiente associada ao `--auth-mode` parâmetro é `AZURE_STORAGE_AUTH_MODE`.

## <a name="call-powershell-commands-with-an-azure-ad-identity"></a>Chame os comandos de PowerShell com uma identidade do Microsoft Azure Active Directory

O Azure PowerShell dá suporte para entrar com uma identidade do Azure AD somente com um dos seguintes módulos de versão prévia: 

- 4.4.0-preview 
- 4.4.1-preview 

Para usar o Microsoft Azure PowerShell para entrar com uma identidade do Microsoft Azure Active Directory:

1. Desinstale as instalações anteriores do Azure PowerShell:

    - Remova as instalações anteriores do Azure PowerShell do Windows usando a configuração **Aplicativos e recursos** em **Configurações**.
    - Remova todos os módulos do **Azure*** de `%Program Files%\WindowsPowerShell\Modules`.

1. Verifique se tem a versão mais recente do PowerShellGet instalado. Abra uma janela do Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```
1. Feche e reabra a janela do PowerShell depois de instalar o PowerShellGet. 

1. Instale a versão mais recente do Azure PowerShell:

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

1. Instale um dos módulos de versão prévia do Armazenamento do Azure que dá suporte ao Azure AD:

    ```powershell
    Install-Module Azure.Storage –Repository PSGallery -RequiredVersion 4.4.1-preview  –AllowPrerelease –AllowClobber –Force 
    ```
1. Feche e reabra a janela do PowerShell.
1. Chame o cmdlet do [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) para criar um contexto e incluir o `-UseConnectedAccount` parâmetro. 
1. Para chamar um cmdlet com uma identidade do Azure AD, passe o contexto recém-criado para o cmdlet.

O exemplo a seguir mostra como listar os blobs em um contêiner do Azure PowerShell usando uma identidade do Azure AD. Lembre-se de substituir os valores de espaço reservado para conta e os nomes de contêineres pelos seus próprios valores: 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName storagesamples -UseConnectedAccount 
Get-AzureStorageBlob -Container sample-container -Context $ctx 
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as funções RBAC para armazenamento do Azure, consulte [Gerenciar os direitos de acesso aos dados de armazenamento com RBAC (versão prévia)](storage-auth-aad-rbac.md).
- Para saber mais sobre como usar identidades gerenciadas para recursos do Azure com o Armazenamento do Azure, confira [Autenticar o acesso aos blobs e às filas com as identidades gerenciadas do Azure para Recursos do Azure (versão prévia)](storage-auth-aad-msi.md).
- Para saber como autorizar o acesso aos contêineres e filas de seus aplicativos de armazenamento, consulte [Use o Microsoft Azure Active Directory com aplicativos de armazenamento](storage-auth-aad-app.md).
- Para obter informações adicionais sobre a integração do Microsoft Azure Active Directory para Blobs e Filas do Azure, consulte a postagem de blog da equipe do Armazenamento do Microsoft Azure, [ Anunciando a Visualização da Autenticação do Azure AD para o Armazenamento do Azure ](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
