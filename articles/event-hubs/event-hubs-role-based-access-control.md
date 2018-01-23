---
title: "Versão prévia do RBAC (controle de acesso baseado em função) dos Hubs de Eventos do Azure | Microsoft Docs"
description: "Controle de acesso baseado em função de Hubs de Eventos do Azure"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 0d3a779eb2cccf242bcd42d82c1a90048b3512ab
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2017
---
# <a name="active-directory-role-based-access-control-preview"></a>Controle de acesso baseado em função do Active Directory (versão prévia)

O Microsoft Azure fornece gerenciamento de controle de acesso integrado para recursos e aplicativos com base no Azure Active Directory (Azure AD). Com o Azure AD, você pode gerenciar contas de usuário e aplicativos especificamente para aplicativos baseados no Azure ou federar sua infraestrutura existente do Active Directory com o Azure AD para logon único em toda a empresa, abrangendo também os recursos do Azure e os aplicativos hospedados do Azure. Em seguida, você pode atribuir essas identidades de usuário e de aplicativo do Azure AD para funções globais e específicas do serviço para permitir acesso aos recursos do Azure.

Para os Hubs de Eventos do Azure, o gerenciamento de namespaces e de todos os recursos relacionados por meio do portal do Azure e da API de gerenciamento de recursos do Azure já está protegido pelo modelo *RBAC (controle de acesso baseado em função)*. Agora, o recurso RBAC para operações de tempo de execução está na versão prévia pública. 

Um aplicativo que usa o RBAC do Azure AD não precisa manipular regras e chaves de SAS nem outros tokens de acesso específicos do Hubs de Eventos. O aplicativo cliente interage com o Azure AD para estabelecer um contexto de autenticação e adquire um token de acesso para os Hubs de Eventos. Com contas de usuário de domínio que exigem logon interativo, o aplicativo nunca lida diretamente com as credenciais.

## <a name="event-hubs-roles-and-permissions"></a>Permissões e funções dos Hubs de Eventos

Na versão prévia pública inicial, você pode adicionar contas e entidades de serviço do Azure AD somente para as funções de "Proprietário" ou "Colaborador" de um namespace dos Hubs de Eventos. Esta operação concede à identidade o controle total sobre todas as entidades no namespace. As operações de gerenciamento que alteram a topologia de namespace são inicialmente compatíveis apenas com o gerenciamento de recursos do Azure e não com a interface de gerenciamento REST nativa dos Hubs de Eventos. Esse suporte também significa que o cliente do objeto [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) do .NET Framework não pode ser usado com uma conta do Azure AD.  

## <a name="use-event-hubs-with-an-azure-ad-domain-user-account"></a>Usar Hubs de Eventos com uma conta de usuário de domínio do Azure AD

A seção a seguir descreve as etapas necessárias para criar e executar um aplicativo de exemplo que solicita o logon de um usuário interativo do Azure AD, como conceder acesso aos Hubs de Eventos para essa conta de usuário e como usar essa identidade para acessar os Hubs de Eventos. 

Esta introdução descreve um aplicativo de console simples, cujo [código está no Github](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Rbac/EventHubsSenderReceiverRbac/)

### <a name="create-an-active-directory-user-account"></a>Criar uma conta de usuário do Active Directory

A primeira etapa é opcional. Cada assinatura do Azure é emparelhada automaticamente com um locatário do Azure Active Directory e, se você tem acesso a uma assinatura do Azure, sua conta de usuário já está registrada. Isso significa que você já pode usar sua conta. 

Se você ainda desejar criar uma conta específica para este cenário, [siga estas etapas](../automation/automation-create-aduser-account.md). Você deve ter permissão para criar contas no locatário do Azure Active Directory, que pode não ser o caso para cenários de empresa maiores.

### <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos

Em seguida, [crie um namespace dos Hubs de Eventos](event-hubs-create.md) em uma das regiões do Azure compatíveis com a versão prévia dos Hubs de Eventos para RBAC: **Leste dos EUA**, **Leste dos EUA 2** ou **Europa Ocidental**. 

Depois que o namespace for criado, navegue até a página **Controle de Acesso (IAM)** no portal e, em seguida, clique em **Adicionar** para adicionar a conta de usuário do Azure AD à função Proprietário. Se você usar sua própria conta de usuário e já tiver criado o namespace, você já estará na função Proprietário. Para adicionar uma conta diferente à função, procure o nome do aplicativo Web no campo **Selecionar** do painel **Adicionar permissões** e, em seguida, clique na entrada. Em seguida, clique em **Salvar**.
 
![](./media/event-hubs-role-based-access-control/rbac1.PNG)

A conta de usuário agora tem acesso ao namespace dos Hubs de Eventos e ao Hub de Eventos criado anteriormente.
 
### <a name="register-the-application"></a>Registrar o aplicativo

Antes de executar o aplicativo de exemplo, registre-o no Azure AD e aprove a solicitação de consentimento que permite que o aplicativo acesse os Hubs de Eventos em seu nome. 

Como o aplicativo de exemplo é um aplicativo de console, você deve registrar um aplicativo nativo e adicionar as permissões de API para **Microsoft.EventHub** ao conjunto de "permissões necessárias". Os aplicativos nativos também precisam de um **URI de redirecionamento** no Azure AD que funcione como um identificador. O URI não precisa ser um destino de rede. Use `http://eventhubs.microsoft.com` para este exemplo, porque o exemplo de código já usa esse URI.

As etapas de registro detalhadas são explicadas [neste tutorial](../active-directory/develop/active-directory-integrating-applications.md). Siga as etapas para registrar um aplicativo **Nativo** e, em seguida, siga as instruções de atualização para adicionar a API **Microsoft.EventHub** às permissões necessárias. Ao seguir as etapas, anote o **TenantId** e o **ApplicationId**, pois você precisará desses valores para executar o aplicativo.

### <a name="run-the-app"></a>Execute o aplicativo

Antes de executar o exemplo, edite o arquivo App.config e, dependendo do cenário, defina os seguintes valores:

- `tenantId`: defina com o valor **TenantId**.
- `clientId`: defina com o valor **ApplicationId**. 
- `clientSecret`: se você quiser fazer logon usando o segredo do cliente, crie-o no Azure AD. Além disso, use um aplicativo Web ou uma API em vez de um aplicativo nativo. E também adicione o aplicativo no **Controle de Acesso (IAM)** no namespace que você criou anteriormente.
- `eventHubNamespaceFQDN`: defina o nome DNS completo do namespace dos Hubs de Eventos recém-criado, por exemplo, `example.servicebus.windows.net`.
- `eventHubName`: defina como o nome do hub de eventos que você criou.
- O URI de redirecionamento que você especificou no aplicativo nas etapas anteriores.
 
Ao executar o aplicativo de console, você precisará selecionar um cenário, clique em **Logon de Usuário Interativo** digitando seu número e pressione Enter. O aplicativo exibe uma janela de logon, solicita seu consentimento para acessar o Hubs de Eventos e, em seguida, usa o serviço para executar o cenário de envio/recebimento usando a identidade de logon.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Hubs de Eventos, acesse os seguintes links:

* Introdução com um [Tutorial de Hubs de Eventos](event-hubs-dotnet-standard-getstarted-send.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
* [Detalhes dos Preços de Hubs de Evento](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Aplicativos de exemplo que usam Hub de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)