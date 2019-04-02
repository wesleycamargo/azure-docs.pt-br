---
title: Autenticar o acesso a blobs e filas com identidades do Active Directory do Azure gerenciado para recursos do Azure - armazenamento do Azure | Microsoft Docs
description: Os armazenamentos de blobs e de filas do Azure dão suporte à autenticação do Azure Active Directory com identidades gerenciadas para recursos do Azure. Você pode usar identidades gerenciadas para recursos do Azure para autenticar o acesso a blobs e filas de aplicativos em execução em máquinas virtuais do Azure, aplicativos de função, conjuntos de dimensionamento de máquinas virtuais e outros. Usando identidades gerenciadas para recursos do Azure e aproveitando a potência da autenticação do Azure AD, é possível evitar o armazenamento de credenciais em aplicativos em execução na nuvem.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: dfdb419a5c06dc50717c0a8a3bdaffb302db52d0
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793009"
---
# <a name="authenticate-access-to-blobs-and-queues-with-managed-identities-for-azure-resources"></a>Autenticar o acesso a blobs e filas com identidades gerenciadas para recursos do Azure

Os armazenamentos de blobs e de filas do Azure dão suporte à autenticação do Azure AD (Active Directory) com [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). Identidades gerenciadas para recursos do Azure podem autenticar o acesso a blobs e filas usando credenciais do Azure AD de aplicativos em execução em máquinas virtuais do Azure (VMs), aplicativos de função, conjuntos de escala de máquinas virtuais e outros. Usando identidades gerenciadas para recursos do Azure e aproveitando a potência da autenticação do Azure AD, é possível evitar o armazenamento de credenciais em aplicativos em execução na nuvem.  

Para conceder permissões para uma identidade gerenciada para uma fila ou um contêiner de blob, você atribui uma função de RBAC (controle de acesso baseado em função) para a identidade gerenciada que abrange as permissões para esse recurso no escopo apropriado. Para obter mais informações sobre as funções RBAC no armazenamento, consulte [gerenciar direitos de acesso aos dados do armazenamento com RBAC](storage-auth-aad-rbac.md). 

Este artigo mostra como autenticar no Armazenamento de Blobs ou de Filas do Azure com uma identidade gerenciada para uma VM do Azure.  

## <a name="enable-managed-identities-on-a-vm"></a>Habilitar identidades gerenciadas em uma VM

Antes que possa usar identidades gerenciadas para Recursos do Azure para autenticar o acesso a blobs e filas de sua VM, primeiro você precisa habilitar as identidades gerenciadas para Recursos do Azure na VM. Para saber como habilitar identidades gerenciadas para Recursos do Azure, confira um dos seguintes artigos:

- [Portal do Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [PowerShell do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo do Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [SDKs do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="assign-an-rbac-role-to-an-azure-ad-managed-identity"></a>Atribuir uma função RBAC a uma identidade gerenciada do AD do Azure

Para autenticar uma identidade gerenciada do seu aplicativo de Armazenamento do Azure, primeiro defina as configurações de controle de acesso baseado em função (RBAC) para essa identidade gerenciada. O Armazenamento do Microsoft Azure define funções RBAC que abrangem permissões para contêineres e filas. Quando a função RBAC é atribuída a uma identidade gerenciada, essa identidade gerenciada recebe acesso a esse recurso. Para obter mais informações, consulte [gerenciar direitos de acesso a dados do Azure Blob e fila com RBAC](storage-auth-aad-rbac.md).

## <a name="get-a-managed-identity-access-token"></a>Obter token de acesso de identidade gerenciada

Para autenticar com uma identidade gerenciada, seu aplicativo ou script precisa adquirir um token de acesso de identidade gerenciada. Para saber como obter um token de acesso, confira [Como usar identidades gerenciadas para recursos do Azure em uma VM do Azure para adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

Para autorizar operações de blob e fila com um token OAuth, você deve usar HTTPS.

## <a name="net-code-example-create-a-block-blob"></a>Exemplo de código do .NET: criar um blob de blocos

O exemplo de código pressupõe que você tem um token de acesso de identidade gerenciada. O token de acesso é utilizado para autorizar a identidade gerenciada para criar um blob de blocos.

### <a name="add-references-and-using-statements"></a>Adicionar referências e usar instruções  

No Visual Studio, instale a biblioteca de cliente de armazenamento do Azure. No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet** e, em seguida, **Console do Gerenciador de Pacotes**. Digite o comando a seguir no console:

```powershell
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

Adicione o seguinte usando instruções para o código:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>Criar credenciais de token de acesso de identidade gerenciada

Para criar o blob de blocos, use o **TokenCredentials** classe. Construa uma nova instância de **TokenCredentials**, passando o token de acesso de identidade gerenciada obtido anteriormente:

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

- Para saber mais sobre as funções RBAC para o armazenamento do Azure, consulte [gerenciar direitos de acesso aos dados do armazenamento com RBAC](storage-auth-aad-rbac.md).
- Para saber como autorizar o acesso aos contêineres e filas de seus aplicativos de armazenamento, consulte [Use o Microsoft Azure Active Directory com aplicativos de armazenamento](storage-auth-aad-app.md).
- Para saber como entrar CLI do Azure e o PowerShell com uma identidade do AD do Azure, consulte [usar uma identidade do AD do Azure para acessar o armazenamento do Azure com a CLI ou o PowerShell](storage-auth-aad-script.md).