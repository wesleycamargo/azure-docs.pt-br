---
title: Autenticar o acesso a blobs e filas com identidades gerenciadas do Azure Active Directory para recursos do Azure (versão prévia) – Armazenamento do Azure | Microsoft Docs
description: Os armazenamentos de blobs e de filas do Azure dão suporte à autenticação do Azure Active Directory com identidades gerenciadas para recursos do Azure. Você pode usar identidades gerenciadas para recursos do Azure para autenticar o acesso a blobs e filas de aplicativos em execução em máquinas virtuais do Azure, aplicativos de função, conjuntos de dimensionamento de máquinas virtuais e outros. Usando identidades gerenciadas para recursos do Azure e aproveitando a potência da autenticação do Azure AD, é possível evitar o armazenamento de credenciais em aplicativos em execução na nuvem.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 15c37be3f3b1b3f72c32865e095091fa10ee9750
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251683"
---
# <a name="authenticate-access-to-blobs-and-queues-with-managed-identities-for-azure-resources-preview"></a>Autenticar acesso a blobs e filas com identidades gerenciadas para recursos do Azure (visualização)

Os armazenamentos de blobs e de filas do Azure dão suporte à autenticação do Azure AD (Active Directory) com [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). Identidades gerenciadas para recursos do Azure podem autenticar o acesso a blobs e filas usando credenciais do Azure AD de aplicativos em execução em máquinas virtuais do Azure (VMs), aplicativos de função, conjuntos de escala de máquinas virtuais e outros. Usando identidades gerenciadas para recursos do Azure e aproveitando a potência da autenticação do Azure AD, é possível evitar o armazenamento de credenciais em aplicativos em execução na nuvem.  

Para conceder permissões para uma identidade gerenciada para uma fila ou um contêiner de blob, você atribui uma função de RBAC (controle de acesso baseado em função) para a identidade gerenciada que abrange as permissões para esse recurso no escopo apropriado. Para obter mais informações sobre as funções RBAC no armazenamento, consulte [Gerenciar direitos de acesso a dados de armazenamento com RBAC (versão prévia)](storage-auth-aad-rbac.md). 

Este artigo mostra como autenticar no Armazenamento de Blobs ou de Filas do Azure com uma identidade gerenciada para uma VM do Azure.  

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="enable-managed-identities-on-a-vm"></a>Habilitar identidades gerenciadas em uma VM

Antes que possa usar identidades gerenciadas para Recursos do Azure para autenticar o acesso a blobs e filas de sua VM, primeiro você precisa habilitar as identidades gerenciadas para Recursos do Azure na VM. Para saber como habilitar identidades gerenciadas para Recursos do Azure, confira um dos seguintes artigos:

- [Portal do Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [PowerShell do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo do Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [SDKs do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="assign-an-rbac-role-to-an-azure-ad-managed-identity"></a>Atribuir uma função RBAC a uma identidade gerenciada do AD do Azure

Para autenticar uma identidade gerenciada do seu aplicativo de Armazenamento do Azure, primeiro defina as configurações de controle de acesso baseado em função (RBAC) para essa identidade gerenciada. O Armazenamento do Microsoft Azure define funções RBAC que abrangem permissões para contêineres e filas. Quando a função RBAC é atribuída a uma identidade gerenciada, essa identidade gerenciada recebe acesso a esse recurso. Para obter mais informações, consulte [Gerenciar direitos de acesso para dados de Blob e fila do Azure com RBAC (visualização)](storage-auth-aad-rbac.md).

## <a name="get-a-managed-identity-access-token"></a>Obter token de acesso de identidade gerenciada

Para autenticar com uma identidade gerenciada, seu aplicativo ou script precisa adquirir um token de acesso de identidade gerenciada. Para saber como obter um token de acesso, confira [Como usar identidades gerenciadas para recursos do Azure em uma VM do Azure para adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Exemplo de código do .NET: criar um blob de blocos

O exemplo de código pressupõe que você tem um token de acesso de identidade gerenciada. O token de acesso é utilizado para autorizar a identidade gerenciada para criar um blob de blocos.

### <a name="add-references-and-using-statements"></a>Adicionar referências e usar instruções  

No Visual Studio, instale a versão prévia da biblioteca de clientes do Armazenamento do Microsoft Azure. No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet** e, em seguida, **Console do Gerenciador de Pacotes**. Digite o comando a seguir no console:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

Adicione o seguinte usando instruções para o código:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>Criar credenciais de token de acesso de identidade gerenciada

Para criar o blob de blocos, use a classe **TokenCredentials** fornecida pelo pacote de visualização. Construa uma nova instância de **TokenCredentials**, passando o token de acesso de identidade gerenciada obtido anteriormente:

```dotnet
// Create storage credentials from your managed identity access token.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> A integração do Microsoft Azure Active Directory ao Armazenamento do Microsoft Azure requer o uso de HTTPS para operações de Armazenamento do Microsoft Azure.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as funções RBAC para armazenamento do Azure, consulte [Gerenciar os direitos de acesso aos dados de armazenamento com RBAC (versão prévia)](storage-auth-aad-rbac.md).
- Para saber como autorizar o acesso aos contêineres e filas de seus aplicativos de armazenamento, consulte [Use o Azure AD com aplicativos de armazenamento](storage-auth-aad-app.md).
- Para saber como efetuar logon na CLI do Azure e no PowerShell com uma identidade do Azure AD, consulte [Use uma identidade do Azure AD para acessar o Armazenamento do Microsoft Azure com a CLI ou o PowerShell (versão prévia)](storage-auth-aad-script.md).
- Para obter informações adicionais sobre a integração do Azure AD para Filas e Blobs do Azure, consulte a postagem no blog da equipe do Armazenamento do Microsoft Azure, [Anunciando a versão prévia de autenticação do Azure AD para Armazenamento do Microsoft Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
