---
title: Autentique o acesso ao Armazenamento do Azure usando o Active Directory do Azure (Visualizar) | Microsoft Docs
description: Autentique o acesso ao Armazenamento do Azure usando o Active Directory do Azure (Visualização).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 06/01/2018
ms.author: tamram
ms.openlocfilehash: 9a0782b96b45d27c9b7e603959ecadf5b2632064
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737639"
---
# <a name="authenticate-access-to-azure-storage-using-azure-active-directory-preview"></a>Autentique o acesso ao Armazenamento do Azure usando o Active Directory do Azure (Visualização)

Armazenamento do Azure dá suporte a autenticação e autorização com o Azure AD (Active Directory) para os serviços Blob e fila. Com o AD do Azure, você pode usar o controle de acesso baseado em função (RBAC) para conceder acesso a usuários, grupos ou entidades de serviço de aplicativo. 

Autorização de aplicativos que acessam o armazenamento do Azure usando o Azure AD fornece segurança superior e facilidade de uso sobre outras opções de autorização. Enquanto você pode continuar a usar a autorização de chave compartilhada com seus aplicativos, usando o AD do Azure evita a necessidade de armazenar sua chave de acesso da conta com o seu código. Da mesma forma, você pode continuar a usar assinaturas de acesso compartilhado (SAS) para conceder acesso refinado para recursos em sua conta de armazenamento, mas o AD do Azure oferece recursos semelhantes sem a necessidade de gerenciar tokens SAS ou se preocupar sobre revogar uma SAS comprometida.

## <a name="about-the-preview"></a>Sobre a visualização

Tenha em mente os seguintes pontos sobre a pré-visualização:

- Integração do AD do Azure está disponível para os serviços Blob e fila somente na visualização.
- Integração do AD do Azure está disponível para contas de armazenamento de Blob, GPv2 e GPv1 em todas as regiões públicas. 
- Somente contas de armazenamento criadas com o modelo de implantação do Resource Manager são suportadas. 
- Suporte para informações de identidade do chamador no log de análise de armazenamento do Azure está disponível em breve.
- Autorização do AD do Azure de acesso a recursos em contas de armazenamento padrão é suportada atualmente. Autorização de acesso para blobs de página nas contas de armazenamento premium terão suporte em breve.
- O Armazenamento do Azure oferece suporte a funções do RBAC internas e personalizadas. Você pode atribuir funções com escopo à assinatura, ao grupo de recursos, à conta de armazenamento ou a um contêiner ou fila individual.
- As bibliotecas de cliente de armazenamento do Azure que atualmente suporte à integração do AD do Azure incluem:
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) (use Visualizar-7.1.x)
    - Python
        - [Blob](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-blob)
        - [Fila](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-queue)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs))

> [!IMPORTANT]
> Esta pré-visualização é destinada apenas para uso não produtivo. Os SLAs (contratos de nível de serviço) de produção não estarão disponíveis até que a integração do Azure AD para Armazenamento do Microsoft Azure seja declarada geralmente disponível. Se a integração do Azure AD ainda não for suportada em seu cenário, continue usando os tokens de autorização ou chave compartilhada em seus aplicativos.
>
> Durante a versão prévia, as atribuições de função do RBAC podem levar até cinco minutos para serem propagadas.
>
> A integração do Azure AD ao Armazenamento do Microsoft Azure requer o uso de HTTPS para operações de Armazenamento do Microsoft Azure.

## <a name="get-started-with-azure-ad-for-storage"></a>Comece a usar o Azure AD para armazenamento

A primeira etapa do uso da integração do Azure AD com o Armazenamento do Azure é atribuir funções do RBAC a dados de armazenamento à sua entidade de serviço (um usuário, grupo ou entidade de serviço de aplicativo) ou Managed Service Identity (MSI). Funções RBAC abrangem comuns conjuntos de permissões para contêineres e filas. Para saber mais sobre as funções do RBAC para Armazenamento do Azure, consulte [ Gerenciar os direitos de acesso aos dados de armazenamento com o RBAC (Visualização) ](storage-auth-aad-rbac.md).

Para usar o AD do Azure para autorizar o acesso aos recursos de armazenamento em seus aplicativos, você deve solicitar um token de acesso OAuth 2.0 no seu código. Para saber como solicitar um token de acesso e usá-la para autorizar solicitações de armazenamento do Azure, consulte [autenticar com o AD do Azure de um aplicativo de armazenamento do Azure (visualização)](storage-auth-aad-app.md). Se você estiver usando uma MSI (Identidade de Serviço Gerenciado do Azure), consulte [ Autenticar com o Azure AD a partir de uma identidade de serviço gerenciado de VM do Azure (visualização) ](storage-auth-aad-msi.md).

CLI e do PowerShell do Azure agora oferecem suporte registro em log com uma identidade do AD do Azure. Depois de você fazer logon com uma identidade do AD do Azure, sua sessão é executado sob essa identidade. Para obter mais informações, consulte [usar uma identidade do AD do Azure para acessar o armazenamento do Azure com CLI ou o PowerShell (visualização)](storage-auth-aad-script.md).

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais sobre a integração do Azure AD para Blobs e Filas do Azure, consulte a postagem de blog da equipe do Armazenamento do Azure, [ Anunciando a Visualização da Autenticação do Azure AD para o Armazenamento do Azure ](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
