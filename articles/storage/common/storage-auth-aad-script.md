---
title: Execute os comandos da CLI do Azure ou PowerShell sob uma identidade do Microsoft Azure Active Directory para acessar o Armazenamento Microsoft do Azure (visualização) | Microsoft Docs
description: A CLI do Azure e o PowerShell oferecem suporte para registros com uma identidade do Microsoft Azure Active Directory para executar comandos em contêineres de Armazenamento do Microsoft Azure e filas e seus dados. Um token de acesso é fornecido para a sessão e usado para autorizar operações de chamada. Permissões dependem da função atribuída a identidade do Microsoft Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/29/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: abd4a3b21ede2ddbdede2ec133938d412d5d4c8d
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43248158"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Use uma identidade do Microsoft Azure Active Directory para acessar o Armazenamento do Microsoft Azure com CLI ou PowerShell (visualização)

O Armazenamento do Microsoft Azure fornece extensões de visualização para CLI do Azure e do PowerShell que permitem que você faça logon e execute comandos de script sob uma identidade do Microsoft Azure Active Directory (Azure AD). Uma identidade do Azure Active Directory pode ser um usuário, grupo ou entidade de serviço de aplicativo ou pode ser uma [identidade de serviço gerenciada](../../active-directory/managed-service-identity/overview.md). Você pode atribuir permissões para acessar recursos de armazenamento à identidade o Microsoft Azure Active Directory através do controle de acesso com base em função (RBAC). Para obter mais informações sobre as funções RBAC no Armazenamento do Microsoft Azure, consulte [Gerenciar direitos de acesso a dados de Armazenamento com RBAC (versão prévia)](storage-auth-aad-rbac.md).

Ao realizar logon na CLI do Azure ou PowerShell com uma identidade do Microsoft Azure Active Directory, um token de acesso é devolvido para acessar o Armazenamento do Microsoft Azure com essa identidade. Esse token é usado automaticamente pela CLI ou o PowerShell para autorizar operações no Armazenamento do Microsoft Azure. Para operações com suporte, você não precisa passar uma chave de conta ou token SAS com o comando.

> [!IMPORTANT]
> Esta versão prévia é destinada apenas para uso não produtivo. Os SLAs (contratos de nível de serviço) de produção não estarão disponíveis até que a integração do Microsoft Azure Active Directory para Armazenamento do Microsoft Azure seja declarada geralmente disponível. Se a integração do Microsoft Azure Active Directory ainda não tiver suporte no seu cenário, continue usando os tokens de autorização ou a chave compartilhada nos aplicativos. Para obter informações adicionais sobre a versão prévia, consulte [Autenticar o acesso ao Armazenamento do Microsoft Azure usando o Azure Active Directory (versão prévia)](storage-auth-aad.md).
>
> Durante a versão prévia, as atribuições de função do RBAC podem levar até cinco minutos para serem propagadas.
>
> A integração do Microsoft Azure Active Directory ao Armazenamento do Microsoft Azure requer o uso de HTTPS para operações de Armazenamento do Microsoft Azure.

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

Para usar o Microsoft Azure PowerShell para entrar com uma identidade do Microsoft Azure Active Directory:

1. Verifique se tem a versão mais recente do PowerShellGet instalado. Execute o comando a seguir para o mais recente:
 
    ```powershell
    Install-Module -Name Azure.Storage -AllowPrerelease –AllowClobber -RequiredVersion "4.4.1-preview"
    ```

2. Desinstale as instalações anteriores do Microsoft Azure PowerShell.
3. Instalar o AzureRM:

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

4. Instale o módulo de versão prévia:

    ```powershell
    Install-Module -Name Azure.Storage -AllowPrerelease –AllowClobber 
    ```

5. Chame o cmdlet do [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) para criar um contexto e incluir o `-UseConnectedAccount` parâmetro. 
6. Para chamar um cmdlet com uma identidade do Microsoft Azure Active Directory, passe o contexto para o cmdlet.

O exemplo a seguir mostra como listar os blobs em um contêiner do Azure PowerShell usando a identidade do Microsoft Azure Active Directory: 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -UseConnectedAccount 
Get-AzureStorageBlob -Container $sample-container -Context $ctx 
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as funções RBAC para armazenamento do Azure, consulte [Gerenciar os direitos de acesso aos dados de armazenamento com RBAC (versão prévia)](storage-auth-aad-rbac.md).
- Para saber como usar a Identidade de Serviço Gerenciada com o Armazenamento do Microsoft Azure, consulte [Autenticar com Microsoft Azure Active Directory a partir de uma Identidade de Serviço Gerenciada do Azure (versão prévia)](storage-auth-aad-msi.md).
- Para saber como autorizar o acesso aos contêineres e filas de seus aplicativos de armazenamento, consulte [Use o Microsoft Azure Active Directory com aplicativos de armazenamento](storage-auth-aad-app.md).
- Para obter informações adicionais sobre a integração do Microsoft Azure Active Directory para Blobs e Filas do Azure, consulte a postagem de blog da equipe do Armazenamento do Microsoft Azure, [ Anunciando a Visualização da Autenticação do Azure AD para o Armazenamento do Azure ](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
