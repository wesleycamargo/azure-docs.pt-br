---
title: API do Graph do Azure Active Directory | Microsoft Docs
description: Uma visão geral e guia de início rápido para a API do Microsoft Azure AD Graph que permite acesso programático ao Microsoft Azure AD por meio de pontos de extremidade da API REST.
services: active-directory
documentationcenter: ''
author: mtillman
manager: mtillman
editor: mbaldwin
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: 4b4f698042f6688e3db484f7d96ccfb06c5cdd4f
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158006"
---
# <a name="azure-active-directory-graph-api"></a>API do Graph do Active Directory do Azure
> [!IMPORTANT]
> É altamente recomendável usar o [Microsoft Graph](https://graph.microsoft.io/) em vez da API do Azure AD Graph para acessar recursos do Azure Active Directory. Nossos esforços de desenvolvimento agora estão concentrados no Microsoft Graph e não há planejamento de novas melhorias para a API do Azure AD Graph. Há um número muito limitado de cenários para os quais a API do Azure AD Graph ainda pode ser apropriada; para obter mais informações, consulte a postagem no blog [Microsoft Graph ou o Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) no Centro de Desenvolvimento do Office.
> 
> 

A API do Graph do Active Directory do Azure fornece acesso programático ao AD do Azure por meio de pontos de extremidade da API REST. Aplicativos podem usar a API do Microsoft Azure AD Graph para realizar operações CRUD (criar, ler, atualizar e excluir) nos objetos e dados do diretório. Por exemplo, a API do Microsoft Azure AD Graph fornece suporte às seguintes operações comuns para um objeto de usuário:

* Criar um novo usuário em um diretório
* Obter propriedades detalhadas do usuário, como seus grupos
* Atualizar as propriedades do usuário, como sua localização e o número de telefone, ou alterar sua senha
* Verificar a associação de grupo do usuário para acesso baseado em função
* Desabilitar uma conta de usuário ou excluí-la totalmente

Além disso, você pode executar operações semelhantes em outros objetos, como grupos e aplicativos. Para chamar a API do Microsoft Azure AD Graph em um diretório, o aplicativo deve estar registrado no Microsoft Azure AD. O aplicativo também deve ter acesso concedido para API do Microsoft Azure AD Graph. Esse acesso é normalmente obtido por meio de um fluxo de consentimento do usuário ou administrador.

Para começar a usar a API do Graph do Azure Active Directory, consulte o [Guia de início rápido da API do Graph](active-directory-graph-api-quickstart.md) ou a [documentação de referência da API do Graph interativa](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Recursos
API do Microsoft Azure AD Graph fornece os seguintes recursos:

* **Pontos de extremidade da API REST**: a API do Microsoft Azure AD Graph é um serviço RESTful composto por pontos de extremidade acessados por meio de solicitações HTTP padrão. A API do Microsoft Azure AD Graph fornece suporte a tipos de conteúdo XML ou JavaScript Object Notation (JSON) para solicitações e respostas. Para saber mais, consulte [Referência da API do Graph REST do AD do Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Autenticação com Microsoft Azure AD**: cada solicitação à API do Microsoft Azure AD Graph deve ser autenticada com o anexo de um JSON Web Token (JWT) no cabeçalho de Autorização da solicitação. Esse token é adquirido ao fazer uma solicitação ao ponto de extremidade do AD do Azure e fornecer credenciais válidas. Você pode usar o fluxo de credenciais de cliente OAuth 2.0 ou o fluxo de concessão do código de autorização para adquirir um token para chamar o Graph. Para obter mais informações, veja [OAuth 2.0 no AD do Azure](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **RBAC (Autorização baseada em função)**: grupos de segurança são usados para executar a RBAC na API do Microsoft Azure AD Graph. Por exemplo, se você quiser determinar se um usuário tem acesso a um recurso específico, o aplicativo pode chamar a operação [Verificar associação de grupo (transitiva)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) que retorna verdadeiro ou falso.
* **Consulta diferencial**: consulta diferencial permite controlar as alterações em um diretório entre dois períodos de tempo sem precisar fazer consultas frequentes à API do Microsoft Azure AD Graph. Este tipo de solicitação retornará apenas as alterações feitas entre a solicitação de consulta diferencial anterior e a solicitação atual. Para saber mais, leia [Consulta diferencial da API do Graph do AD do Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Extensões de diretório**: é possível adicionar propriedades personalizadas a objetos de diretório sem exigir um armazenamento de dados externo. Por exemplo, se o aplicativo exigir uma propriedade de ID do Skype para cada usuário, você poderá registrar a nova propriedade no diretório e estará disponível para uso em cada objeto de usuário. Para obter mais informações, veja [Extensões de esquema do diretório da API do Graph do AD do Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Protegido pelos escopos de permissão**: a API do Microsoft Azure AD Graph expõe escopos de permissão que permitem acesso seguro aos dados do Microsoft Azure AD usando o OAuth 2.0. Isso fornece suporta a uma variedade de tipos de aplicativo cliente, incluindo:
  
  * interfaces de usuário que recebem acesso delegado aos dados por meio de autorização do usuário conectado (delegado)
  * aplicativos de serviço/daemon que funcionam em segundo plano sem que um usuário conectado esteja presente e use o controle de acesso baseado em função definido pelo aplicativo
    
    Ambas as permissões delegadas e de aplicativo representam um privilégio exposto pela API do Microsoft Azure AD Graph e podem ser solicitadas pelos aplicativos clientes por meio dos recursos de permissões de registro do aplicativo no [Portal do Azure](https://portal.azure.com). [Escopos de permissão da API do Microsoft Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) fornecem informações sobre o que está disponível para uso pelo aplicativo cliente.

## <a name="scenarios"></a>Cenários
A API do Microsoft Azure AD Graph habilita muitos cenários de aplicativos. A seguir, os cenários mais comuns:

* **Aplicativo de linha de negócios (único locatário)**: neste cenário, um desenvolvedor corporativo trabalha para uma organização que tem uma assinatura do Office 365. O desenvolvedor está criando um aplicativo Web que interage com o Azure AD para executar tarefas como a atribuição de uma licença para um usuário. Esta tarefa exige acesso à API do Microsoft Azure AD Graph para que o desenvolvedor registre o aplicativo de único locatário no Microsoft Azure AD e configure as permissões de leitura e gravação para a API do Microsoft Azure AD Graph. Em seguida, o aplicativo é configurado para usar suas próprias credenciais ou aquelas do usuário conectado no momento para adquirir um token para chamar a API do Microsoft Azure AD Graph.
* **Aplicativo de software como serviço (multilocatário)**: neste cenário, um fornecedor de software independente (ISV) está desenvolvendo um aplicativo Web multilocatário hospedado que fornece recursos de gerenciamento de usuário para outras organizações que usam o AD do Azure. Esses recursos exigem acesso a objetos de diretório, por isso o aplicativo precisa chamar a API do Microsoft Azure AD Graph. O desenvolvedor registra o aplicativo no Microsoft Azure AD, configura para exigir as permissões de leitura e gravação para a API do Microsoft Azure AD Graph e habilita o acesso externo para que outras organizações possam concordar com o uso do aplicativo em seu diretório. Quando um usuário de outra organização autentica o aplicativo pela primeira vez, é mostrada uma caixa de diálogo de consentimento com as permissões que o aplicativo está solicitando. Conceder consentimento dará o aplicativo às permissões solicitadas para a API do Microsoft Azure AD Graph no diretório do usuário. Para saber mais sobre a estrutura de consentimento, consulte [Visão geral da estrutura de consentimento](active-directory-integrating-applications.md).

## <a name="see-also"></a>Veja também
[Guia de início rápido para a API do Graph do AD do Azure](active-directory-graph-api-quickstart.md)

[Documentação do REST para a Microsoft Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md)

