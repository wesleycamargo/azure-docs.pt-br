---
title: Autentique o acesso a blobs e filas do Azure usando o Active Directory do Azure (Visualizar) | Microsoft Docs
description: Autentique o acesso a blobs e filas do Azure usando o Azure Active Directory (Visualização).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/13/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: dcf7d237c8cfbf52a804e428d84fff0bb328c7c8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012110"
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
    - [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)
    - Python
        - [BLOB, fila e arquivos](https://github.com/Azure/azure-storage-python)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

## <a name="overview-of-azure-ad-for-storage"></a>Visão geral do AD do Azure para armazenamento

A primeira etapa ao usar a integração do Azure AD com o Armazenamento do Azure é atribuir funções do RBAC para dados de armazenamento à sua entidade de serviço (um usuário, grupo ou entidade de serviço de aplicativo) ou identidades gerenciadas para recursos do Azure. Funções RBAC abrangem comuns conjuntos de permissões para contêineres e filas. Para saber mais sobre como atribuir funções RBAC para o armazenamento do Azure, consulte [gerenciar direitos de acesso aos dados do armazenamento com RBAC (versão prévia)](storage-auth-aad-rbac.md).

Para usar o AD do Azure para autorizar o acesso aos recursos de armazenamento em seus aplicativos, você deve solicitar um token de acesso OAuth 2.0 no seu código. Para saber como solicitar um token de acesso e usá-la para autorizar solicitações de armazenamento do Azure, consulte [autenticar com o AD do Azure de um aplicativo de armazenamento do Azure (visualização)](storage-auth-aad-app.md). Se você estiver usando uma identidade gerenciada, consulte [autenticar o acesso aos blobs e filas com o Azure as identidades gerenciadas para recursos do Azure (visualização)](storage-auth-aad-msi.md).

CLI e do PowerShell do Azure agora oferecem suporte registro em log com uma identidade do AD do Azure. Depois de você fazer logon com uma identidade do AD do Azure, sua sessão é executado sob essa identidade. Para obter mais informações, consulte [usar uma identidade do AD do Azure para acessar o armazenamento do Azure com CLI ou o PowerShell (visualização)](storage-auth-aad-script.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>Funções RBAC para blobs e filas

Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md). O Armazenamento do Microsoft Azure define um conjunto de funções internas do RBAC que abrangem conjuntos comuns de permissões usados para acessar contêineres ou filas. 

Quando uma função RBAC é atribuída a uma entidade de segurança do Azure AD, Azure concede acesso a esses recursos para essa entidade de segurança. O escopo do acesso pode ser definido para o nível de assinatura, o grupo de recursos, a conta de armazenamento ou um contêiner ou fila individual. Uma entidade de segurança do Azure AD pode ser um usuário, grupo, uma entidade de serviço de aplicativo, ou um [identidade de recursos do Azure gerenciado](../../active-directory/managed-identities-azure-resources/overview.md). 

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Para saber como atribuir uma função interna no portal do Azure, consulte [conceder acesso aos contêineres do Azure e filas com o RBAC no portal do Azure (visualização)](storage-auth-aad-rbac.md).

### <a name="access-permissions-granted-by-rbac-roles"></a>Permissões de acesso concedidas pelas funções RBAC 

A tabela a seguir resume os direitos de acesso concedidos por funções internas para diferentes níveis de escopo:

|Escopo|Proprietário de Dados do Blob de Armazenamento|Colaborador de dados de blob|Leitor de dados de blob|Colaborador de dados de fila|Leitor de dados de fila|
|---|---|---|---|---|---|
|Nível de assinatura|Gerenciamento de controle para todos os contêineres e blobs na assinatura de acesso de acesso de leitura/gravação e POSIX|Acesso de leitura/gravação a todos os contêineres e blobs na assinatura| Acesso de leitura a todos os contêineres e blobs na assinatura|Acesso de leitura/gravação a todas as filas na assinatura|Acesso de leitura a todas as filas na assinatura|
|Nível do grupo de recursos|Acesso de leitura/gravação e POSIX para todos os contêineres e blobs no grupo de recursos de gerenciamento do controle de acesso|Acesso de leitura/gravação a todos os contêineres e blobs no grupo de recursos|Acesso de leitura para todos os contêineres e blobs no grupo de recursos|Acesso de leitura/gravação a todas as filas no grupo de recursos|Acesso de leitura a todas as filas no grupo de recursos|
|Nível da conta de armazenamento|Gerenciamento de controle de acesso à leitura/gravação e POSIX para todos os contêineres e blobs na conta de armazenamento|Acesso de leitura/gravação a todos os contêineres e blobs na conta de armazenamento|Acesso de leitura a todos os contêineres e blobs na conta de armazenamento|Acesso de leitura/gravação a todas as filas na conta de armazenamento|Acesso de leitura a todas as filas na conta de armazenamento|
|Nível de contêiner/fila|Gerenciamento de controle de acesso à leitura/gravação e POSIX no contêiner especificado e seus blobs.|Acesso de leitura/gravação ao contêiner especificado e seus blobs|Acesso de leitura ao contêiner especificado e seus blobs|Acesso de leitura/gravação à fila especificada|Acesso de leitura à fila especificada|

Para obter detalhes sobre as permissões necessárias para chamar operações de Armazenamento do Microsoft Azure, consulte [Permissões para chamar operações REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais sobre a integração do Microsoft Azure Active Directory para Blobs e Filas do Azure, consulte a postagem de blog da equipe do Armazenamento do Microsoft Azure, [ Anunciando a Visualização da Autenticação do Azure AD para o Armazenamento do Azure ](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
