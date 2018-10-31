---
title: Autentique o acesso a blobs e filas do Azure usando o Active Directory do Azure (Visualizar) | Microsoft Docs
description: Autentique o acesso a blobs e filas do Azure usando o Azure Active Directory (Visualização).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: a918a44042df13c8b799d823656c4cd878f8f618
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426644"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory-preview"></a>Autenticar o acesso a blobs e filas do Azure usando o Active Directory do Azure (Visualizar)

Armazenamento do Azure dá suporte a autenticação e autorização com o Azure AD (Active Directory) para os serviços Blob e fila. Com o AD do Azure, você pode usar o controle de acesso baseado em função (RBAC) para conceder acesso a usuários, grupos ou entidades de serviço de aplicativo. 

Autenticar usuários ou aplicativos usando as credenciais do Azure AD oferece mais segurança e facilidade ao usar os meios de autorização. Enquanto você pode continuar a usar a autorização de chave compartilhada com seus aplicativos, usando o AD do Azure evita a necessidade de armazenar sua chave de acesso da conta com o seu código. Também é possível continuar a usar assinaturas de acesso compartilhado (SAS) para conceder acesso refinado a recursos em sua conta de armazenamento, mas o Azure AD oferece recursos semelhantes sem a necessidade de gerenciar tokens SAS ou se preocupar sobre revogar uma SAS comprometida. A Microsoft recomenda o uso da autenticação do Azure AD para seus aplicativos de Armazenamento do Azure, quando possível.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="about-the-preview"></a>Sobre a visualização

Tenha em mente os seguintes pontos sobre a pré-visualização:

- Integração do AD do Azure está disponível para os serviços Blob e fila somente na visualização.
- Integração do AD do Azure está disponível para contas de armazenamento de Blob, GPv2 e GPv1 em todas as regiões públicas. 
- Somente contas de armazenamento criadas com o modelo de implantação do Resource Manager são suportadas. 
- Suporte para informações de identidade do chamador no log de análise de armazenamento do Azure está disponível em breve.
- Autorização do AD do Azure de acesso a recursos em contas de armazenamento padrão é suportada atualmente. Autorização de acesso para blobs de página nas contas de armazenamento premium terão suporte em breve.
- O Armazenamento do Azure oferece suporte a funções do RBAC internas e personalizadas. Você pode atribuir funções com escopo à assinatura, ao grupo de recursos, à conta de armazenamento ou a um contêiner ou fila individual.
- As bibliotecas de cliente de armazenamento do Azure que atualmente suporte à integração do AD do Azure incluem:
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)
    - Python
        - [BLOB, fila e arquivos](https://github.com/Azure/azure-storage-python)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

## <a name="get-started-with-azure-ad-for-storage"></a>Comece a usar o Azure AD para armazenamento

A primeira etapa ao usar a integração do Azure AD com o Armazenamento do Azure é atribuir funções do RBAC para dados de armazenamento à sua entidade de serviço (um usuário, grupo ou entidade de serviço de aplicativo) ou identidades gerenciadas para recursos do Azure. Funções RBAC abrangem comuns conjuntos de permissões para contêineres e filas. Para saber mais sobre as funções do RBAC para Armazenamento do Azure, consulte [ Gerenciar os direitos de acesso aos dados de armazenamento com o RBAC (Visualização) ](storage-auth-aad-rbac.md).

Para usar o AD do Azure para autorizar o acesso aos recursos de armazenamento em seus aplicativos, você deve solicitar um token de acesso OAuth 2.0 no seu código. Para saber como solicitar um token de acesso e usá-la para autorizar solicitações de armazenamento do Azure, consulte [autenticar com o AD do Azure de um aplicativo de armazenamento do Azure (visualização)](storage-auth-aad-app.md). Se você estiver usando uma identidade gerenciada, consulte [autenticar o acesso aos blobs e filas com o Azure as identidades gerenciadas para recursos do Azure (visualização)](storage-auth-aad-msi.md).

CLI e do PowerShell do Azure agora oferecem suporte registro em log com uma identidade do AD do Azure. Depois de você fazer logon com uma identidade do AD do Azure, sua sessão é executado sob essa identidade. Para obter mais informações, consulte [usar uma identidade do AD do Azure para acessar o armazenamento do Azure com CLI ou o PowerShell (visualização)](storage-auth-aad-script.md).

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais sobre a integração do Microsoft Azure Active Directory para Blobs e Filas do Azure, consulte a postagem de blog da equipe do Armazenamento do Microsoft Azure, [ Anunciando a Visualização da Autenticação do Azure AD para o Armazenamento do Azure ](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
