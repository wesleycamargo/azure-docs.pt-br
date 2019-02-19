---
title: Autenticação com os Azure Mapas | Microsoft Docs
description: Autenticação para uso dos serviços dos Azure Mapas.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b97c303433eb8fadcda51257d37447f052ce4a3b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118779"
---
# <a name="authentication-with-azure-maps"></a>Autenticação com Azure Mapas

Os Azure Mapas dão suporte a duas maneiras de autenticar solicitações. A Chave Compartilhada ou o Azure AD (Azure Active Directory) oferecem métodos distintos para autorizar cada solicitação enviada para os Azure Mapas. A finalidade deste artigo é explicar os dois métodos de autenticação para ajudar a orientar a implementação de autenticação.

## <a name="shared-key-authentication"></a>Autenticação de Chave Compartilhada

A autenticação de Chave Compartilhada baseia-se na passagem das chaves geradas pela conta dos Azure Mapas com cada solicitação aos Azure Mapas.  Duas chaves são geradas quando sua conta dos Azure Mapas é criada.  Cada solicitação aos serviços dos Azure Mapas exige que a chave de assinatura seja adicionada como um parâmetro à URL.

> [!Tip]
> Recomendamos a regeneração frequente das chaves. Você recebe duas chaves, de modo que possa manter conexões usando uma chave enquanto regenera a outra. Ao regenerar suas chaves, você precisará atualizar os aplicativos que acessam essa conta para que usem as novas chaves.

Para exibir suas chaves, confira [Detalhes de autenticação](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Autenticação com o Azure Active Directory (versão prévia)

Os Azure Mapas agora oferecem a integração do [Azure AD (Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) para a autenticação de solicitações de serviços dos Azure Mapas.  O Azure AD fornece autenticação baseada em identificação, incluindo o [RBAC (controle de acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/overview) para permitir acesso no nível do usuário ou do aplicativo aos recursos dos Azure Mapas. A finalidade deste artigo é ajudá-lo a entender os conceitos e os componentes da integração entre o Azure AD e os Azure Mapas.

## <a name="authentication-with-oauth-access-tokens"></a>Autenticação com tokens de acesso OAuth

Os Azure Mapas aceitam tokens de acesso **OAuth 2.0** para locatários do Azure AD associados à assinatura do Azure que contém uma conta dos Azure Mapas.  Os Azure Mapas aceitam tokens para:

* Usuários do Azure AD 
* Aplicativos de terceiros que usam permissões delegadas pelos usuários
* Identidades gerenciadas dos recursos do Azure

Os Azure Mapas geram uma `unique identifier (client ID)` para cada conta dos Azure Mapas.  Quando a ID do cliente é combinada com parâmetros adicionais, tokens podem ser solicitados do Azure AD pela especificação do valor abaixo:

```
https://login.microsoftonline.com
```
Para obter mais informações sobre como configurar o Azure AD e solicitar tokens para os Azure Mapas, confira [Como gerenciar a autenticação](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Para obter informações gerais sobre como solicitar tokens do Azure AD, confira [Noções básicas de autenticação no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="requesting-azure-map-resources-with-oauth-tokens"></a>Solicitando recursos dos Azure Mapas com tokens OAuth

Depois que um token é adquirido do Azure AD, uma solicitação pode ser enviada para os Azure Mapas com os dois seguintes cabeçalhos de solicitação obrigatórios definidos:

| Cabeçalho da Solicitação    |    Valor    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Autorização     | Portador eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` é o GUID baseado na conta dos Azure Mapas exibido na página de autenticação dos Azure Mapas

Veja abaixo um exemplo de solicitação de rota dos Azure Mapas usando o token OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Para exibir sua ID do cliente, confira [Detalhes de autenticação](https://aka.ms/amauthdetails).

## <a name="control-access-with-role-based-access-control-rbac"></a>Controlar o acesso com o RBAC (controle de acesso baseado em função)

Um recurso essencial do Azure AD é o controle do acesso a recursos protegidos por meio do RBAC. Depois que a sua conta dos Azure Mapas for criada e você registrar o aplicativo do Azure AD e do Azure Mapas em seu locatário do Azure AD, você poderá configurar o RBAC para um usuário, um aplicativo ou um recurso do Azure na página do portal de contas dos Azure Mapas. 

Atualmente, os Azure Mapas dão suporte ao controle de acesso de leitura para usuários individuais do Azure AD, aplicativos ou serviços do Azure por meio de identidades gerenciadas para recursos do Azure.

![conceito](./media/azure-maps-authentication/concept.png)

Para exibir as configurações do RBAC, confira [Como configurar o RBAC para os Azure Mapas](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identidades gerenciadas para recursos do Azure e os Azure Mapas

As [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) fornecem serviços do Azure (Serviço de Aplicativo do Azure, Azure Functions, Máquinas Virtuais etc.) com uma identidade gerenciada automaticamente que pode ser autorizada para acesso aos serviços dos Azure Mapas.  

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como autenticar um aplicativo com o Azure AD e os Azure Mapas, confira [Como gerenciar a autenticação](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Para saber mais sobre como autenticar os Azure Mapas, o Controle de Mapeamento e o Azure AD, confira [Azure AD e Controle de Mapeamento dos Azure Mapas](https://aka.ms/amaadmc).