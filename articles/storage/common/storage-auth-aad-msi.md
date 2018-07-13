---
title: Autenticar com Azure AD a partir de uma Identidade de Serviço Gerenciada de VM do Azure (versão prévia) | Microsoft Docs
description: Autentique com Azure AD a partir de uma Identidade de Serviço Gerenciada de VM do Azure (versão prévia).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 080cb3ee536227e5ddce3fac856de79b2b061dcf
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970761"
---
# <a name="authenticate-with-azure-ad-from-an-azure-managed-service-identity-preview"></a>Autenticar com Azure AD a partir de uma Identidade de Serviço Gerenciada do Azure (versão prévia)

O Armazenamento do Microsoft Azure dá suporte para autenticação do Azure AD (Azure Active Directory) com [Identidade de Serviço Gerenciada](../../active-directory/managed-service-identity/overview.md). A MSI (Identidade de Serviço Gerenciada) fornece uma identidade gerenciada automaticamente no Azure AD (Azure Active Directory). É possível usar a MSI para autenticar no Armazenamento do Microsoft Azure a partir de aplicativos executando em máquinas virtuais do Azure, aplicativos de funções, conjuntos de dimensionamento de máquinas virtuais e outros. Usando a MSI e aproveitando a potência da autenticação do Azure AD, é possível evitar o armazenamento de credenciais com aplicativos em execução na nuvem.  

Para conceder permissões a uma identidade de serviço gerenciada para contêineres ou filas de armazenamento, você atribui uma função RBAC que inclui permissões de armazenamento para MSI. Para obter mais informações sobre as funções RBAC no armazenamento, consulte [Gerenciar direitos de acesso a dados de armazenamento com RBAC (versão prévia)](storage-auth-aad-rbac.md). 

> [!IMPORTANT]
> Essa versão prévia é destinada apenas para uso não produtivo. Os SLAs (contratos de nível de serviço) de produção não estarão disponíveis até que a integração do Microsoft Azure Active Directory para Armazenamento do Microsoft Azure seja declarada geralmente disponível. Se a integração do Microsoft Azure Active Directory ainda não tiver suporte no seu cenário, continue usando os tokens de autorização ou a chave compartilhada nos aplicativos. Para obter informações adicionais sobre a versão prévia, consulte [Autenticar o acesso ao Armazenamento do Microsoft Azure usando o Azure Active Directory (versão prévia)](storage-auth-aad.md).
>
> Durante a versão prévia, as atribuições de função do RBAC podem levar até cinco minutos para serem propagadas.

Este artigo mostra como autenticar no Armazenamento do Microsoft Azure com MSI a partir de uma VM do Azure.  

## <a name="enable-msi-on-the-vm"></a>Habilitar MSI na VM

Antes de poder usar a MSI para autenticar no Armazenamento do Microsoft Azure a partir da VM, primeiro será necessário habilitar a MSI na VM. Para saber mais como habilitar a MSI, consulte um destes artigos:

- [Portal do Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [PowerShell do Azure](../../active-directory/managed-service-identity/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../../active-directory/managed-service-identity/qs-configure-cli-windows-vm.md)
- [Modelo do Azure Resource Manager](../../active-directory/managed-service-identity/qs-configure-template-windows-vm.md)
- [SDKs do Azure](../../active-directory/managed-service-identity/qs-configure-sdk-windows-vm.md)

## <a name="get-an-msi-access-token"></a>Obter um token de acesso MSI

Para autenticar com MSI, o aplicativo ou script deve adquirir um token de acesso MSI. Para saber como adquirir um token de acesso, consulte [Como usar uma MSI (Identidade de Serviço Gerenciada) da VM do Azure para aquisição de token](../../active-directory/managed-service-identity/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Exemplo de código .NET: crie um blob de blocos

O exemplo de código pressupõe que você tem um token de acesso MSI. O token de acesso é utilizado para autorizar a identidade do serviço gerenciada para criar um blob de blocos.

### <a name="add-references-and-using-statements"></a>Adicionar referências e usar instruções  

No Visual Studio, instale a versão prévia da biblioteca de clientes do Armazenamento do Microsoft Azure. No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet** e, em seguida, **Console do Gerenciador de Pacotes**. Digite o comando a seguir no console:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

Adicione o seguinte usando instruções para o código:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-msi-access-token"></a>Criar credenciais do token de acesso MSI

Para criar o blob de blocos, use a classe **TokenCredentials** fornecida pelo pacote de visualização. Construa uma nova instância de **TokenCredentials**, passando o token de acesso MSI obtido anteriormente:

```dotnet
// Create storage credentials from your MSI access token.
TokenCredential tokenCredential = new TokenCredential(msiAccessToken);
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
