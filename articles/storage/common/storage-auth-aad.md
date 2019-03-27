---
title: Autenticar o acesso a blobs do Azure e filas usando o Azure Active Directory | Microsoft Docs
description: Autenticar o acesso a blobs do Azure e filas usando o Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 08fdc20df70e4a97dd0cb99468354ef2b5c51f2b
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447028"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Autenticar o acesso a blobs do Azure e filas usando o Azure Active Directory

Armazenamento do Azure dá suporte a autenticação e autorização com o Azure AD (Active Directory) para os serviços Blob e fila. Com o AD do Azure, você pode usar o controle de acesso baseado em função (RBAC) para conceder acesso a usuários, grupos ou entidades de serviço de aplicativo. 

Autenticar usuários ou aplicativos usando as credenciais do Azure AD oferece mais segurança e facilidade ao usar os meios de autorização. Enquanto você pode continuar a usar a autorização de chave compartilhada com seus aplicativos, usando o AD do Azure evita a necessidade de armazenar sua chave de acesso da conta com o seu código. Também é possível continuar a usar assinaturas de acesso compartilhado (SAS) para conceder acesso refinado a recursos em sua conta de armazenamento, mas o Azure AD oferece recursos semelhantes sem a necessidade de gerenciar tokens SAS ou se preocupar sobre revogar uma SAS comprometida. A Microsoft recomenda o uso da autenticação do Azure AD para seus aplicativos de Armazenamento do Azure, quando possível.

Autenticação e autorização com credenciais do Azure AD está disponível para todas as contas de armazenamento de Blob em todas as regiões públicas, uso geral v1 e v2 de uso geral. Somente contas de armazenamento criado com o suporte ao modelo de implantação do Azure Resource Manager autorização do Azure AD.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Visão geral do AD do Azure para blobs e filas

A primeira etapa ao usar a integração do Azure AD com o Armazenamento do Azure é atribuir funções do RBAC para dados de armazenamento à sua entidade de serviço (um usuário, grupo ou entidade de serviço de aplicativo) ou identidades gerenciadas para recursos do Azure. Funções RBAC abrangem comuns conjuntos de permissões para contêineres e filas. Para saber mais sobre como atribuir funções RBAC para o armazenamento do Azure, consulte [gerenciar direitos de acesso aos dados do armazenamento com RBAC](storage-auth-aad-rbac.md).

Para usar o AD do Azure para autorizar o acesso aos recursos de armazenamento em seus aplicativos, você deve solicitar um token de acesso OAuth 2.0 no seu código. Para saber como solicitar um token de acesso e usá-la para autorizar solicitações no armazenamento do Azure, consulte [autenticar com o Azure AD de um aplicativo de armazenamento do Azure](storage-auth-aad-app.md). Se você estiver usando uma identidade gerenciada, consulte [autenticar o acesso aos blobs e filas com o Azure as identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md).

CLI e do PowerShell do Azure agora oferecem suporte registro em log com uma identidade do AD do Azure. Depois que você entrar com uma identidade do AD do Azure, sua sessão é executado sob essa identidade. Para obter mais informações, consulte [usar uma identidade do AD do Azure para acessar o armazenamento do Azure com a CLI ou o PowerShell](storage-auth-aad-script.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções RBAC para blobs e filas

Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md). O Armazenamento do Microsoft Azure define um conjunto de funções internas do RBAC que abrangem conjuntos comuns de permissões usados para acessar contêineres ou filas. 

Quando uma função RBAC é atribuída a uma entidade de segurança do Azure AD, Azure concede acesso a esses recursos para essa entidade de segurança. O escopo do acesso pode ser definido para o nível de assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Uma entidade de segurança do Azure AD pode ser um usuário, grupo, uma entidade de serviço de aplicativo, ou um [identidade de recursos do Azure gerenciado](../../active-directory/managed-identities-azure-resources/overview.md).

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Para saber como atribuir um RBAC interno para recursos de armazenamento do Azure, consulte um dos seguintes tópicos:

- [Conceder acesso a dados do Azure blob e fila com o RBAC no portal do Azure](storage-auth-aad-rbac-portal.md)
- [Conceder acesso a dados de blob e fila do Azure com o RBAC usando a CLI do Azure](storage-auth-aad-rbac-cli.md)
- [Conceder acesso a dados de blob e fila do Azure com o RBAC, usando o PowerShell](storage-auth-aad-rbac-powershell.md)

### <a name="access-permissions-granted-by-rbac-roles"></a>Permissões de acesso concedidas pelas funções RBAC 

Para obter detalhes sobre as permissões necessárias para chamar operações de Armazenamento do Microsoft Azure, consulte [Permissões para chamar operações REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

## <a name="next-steps"></a>Próximas etapas

- [Autenticar com o Azure Active Directory em um aplicativo para ter acesso a blobs e filas](storage-auth-aad-app.md)
- [Autenticar o acesso a blobs e filas com identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md)
- Os arquivos do Azure dá suporte à autenticação com o AD do Azure no SMB ingressado no domínio somente às VMs (visualização). Para saber mais sobre como usar o Azure AD em SMB para arquivos do Azure, consulte [Visão geral da autenticação do Active Directory do Azure em SMB para arquivos do Azure (visualização)](../files/storage-files-active-directory-overview.md).