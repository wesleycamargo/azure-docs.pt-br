---
title: "Versão prévia da Identidade de Serviço Gerenciada com Hubs de Eventos do Azure | Microsoft Docs"
description: "Usar Identidade de Serviço Gerenciada com Hubs de Eventos do Azure"
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
ms.date: 12/18/2017
ms.author: sethm
ms.openlocfilehash: dd50e4f6ebc5fdf5496a5127fde20bd052087b59
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2017
---
# <a name="managed-service-identity-preview"></a>Identidade de Serviço Gerenciada (versão prévia)

Uma MSI (Identidade de Serviço Gerenciada) é um recurso do Azure que permite criar uma identidade segura associada à implantação na qual o código do aplicativo é executado. Em seguida, você pode associar essa identidade a funções de controle de acesso que concedem permissões personalizadas para acessar os recursos do Azure específicos que seu aplicativo precisa. 

Com a MSI, a plataforma Azure gerencia essa identidade de tempo de execução. Você não precisa armazenar e proteger as chaves de acesso no código ou na configuração do aplicativo, seja para a identidade ou para os recursos que você precisa acessar. Um aplicativo cliente dos Hubs de Eventos em execução em um aplicativo do Serviço de Aplicativo do Azure ou em uma máquina virtual com o suporte à MSI habilitado não precisa manipular regras e chaves de SAS nem nenhum outro token de acesso. O aplicativo cliente precisa apenas do endereço do ponto de extremidade do namespace dos Hubs de Eventos. Quando o aplicativo se conecta, os Hubs de Eventos associam o contexto de MSI ao cliente em uma operação que será mostrada em um exemplo mais adiante neste artigo.

Quando um cliente dos Hubs de Eventos está associado a uma Identidade de Serviço Gerenciada, ele pode executar todas as operações autorizadas. A autorização é concedida ao associar uma MSI a funções dos Hubs de Eventos. 

## <a name="event-hubs-roles-and-permissions"></a>Permissões e funções dos Hubs de Eventos

Na versão prévia pública inicial, é possível adicionar apenas uma Identidade de Serviço Gerenciada nas funções "Proprietário" ou "Colaborador" de um namespace dos Hubs de Eventos, que concede controle total à identidade em todas as entidades no namespace. No entanto, as operações de gerenciamento que alteram a topologia do namespace são compatíveis inicialmente apenas com o Azure Resource Manager e não com a interface de gerenciamento REST nativa dos Hubs de Eventos. Isso também significa que não é possível usar o objeto cliente [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) do .NET Framework dentro de uma Identidade de Serviço Gerenciada. 
 
## <a name="use-event-hubs-with-a-managed-service-identity"></a>Usar os Hubs de Eventos com uma Identidade de Serviço Gerenciada

A seção a seguir descreve as etapas necessárias para criar e implantar um aplicativo de exemplo que é executado em uma Identidade de Serviço Gerenciada, como conceder a essa identidade o acesso a um namespace do sistema de mensagens dos Hubs de Eventos e como o aplicativo interage com as entidades dos Hubs de Eventos usando essa identidade.

Esta introdução descreve um aplicativo Web hospedado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/). As etapas necessárias para um aplicativo hospedado em VM são semelhantes.

### <a name="create-an-app-service-web-application"></a>Criar um aplicativo Web do Serviço de Aplicativo

A primeira etapa é criar um aplicativo ASP.NET do Serviço de Aplicativo. Se você não estiver familiarizado com a forma de fazer isso no Azure, siga [este guia de instruções](../app-service/app-service-web-get-started-dotnet-framework.md). No entanto, em vez de criar um aplicativo MVC, conforme é mostrado no tutorial, crie um aplicativo Web Forms.

### <a name="set-up-the-managed-service-identity"></a>Configurar a Identidade de Serviço Gerenciada

Depois de criar o aplicativo, navegue até o aplicativo Web recém-criado no portal do Azure (também mostrado nas instruções) e, em seguida, navegue até a página **Identidade de Serviço Gerenciada** e habilite o recurso: 

![](./media/event-hubs-managed-service-identity/msi1.png)
 
Depois de habilitar o recurso, uma nova identidade do serviço será criada no Azure Active Directory e configurada no host do Serviço de Aplicativo.

### <a name="create-a-new-event-hubs-namespace"></a>Criar um novo namespace dos Hubs de Eventos

Em seguida, [crie um namespace do sistema de mensagens dos Hubs de Eventos](event-hubs-create.md) em uma das regiões do Azure compatíveis com MSI em versão prévia: **Leste dos EUA**, **Leste dos EUA 2** ou **Europa Ocidental**. 

Navegue até a página **Controle de Acesso (IAM)** do namespace no portal e, em seguida, clique em **Adicionar** para adicionar a Identidade de Serviço Gerenciada à função **Proprietário**. Para fazer isso, procure o nome do aplicativo Web no campo **Selecionar** do painel **Adicionar permissões** e, em seguida, clique na entrada. Em seguida, clique em **Salvar**.

![](./media/event-hubs-managed-service-identity/msi2.png)
 
A Identidade de Serviço Gerenciada do aplicativo Web agora tem acesso ao namespace dos Hubs de Eventos e ao hub de evento criado anteriormente. 

### <a name="run-the-app"></a>Execute o aplicativo

Agora, modifique a página padrão do aplicativo ASP.NET que você criou. Você também pode usar o código do aplicativo Web [deste repositório GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

Depois de iniciar o aplicativo, aponte o navegador para EventHubsMSIDemo.aspx. Como alternativa, defina-o como sua página inicial. O código pode ser encontrado no arquivo EventHubsMSIDemo.aspx.cs. O resultado é um aplicativo Web mínimo com alguns campos de entrada e os botões **enviar** e **receber** que se conectam aos Hubs de Eventos para enviar ou receber mensagens. 

Observe como o objeto [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inicializado. Em vez de usar o provedor de token SAS (Token de Acesso Compartilhado), o código cria um provedor de token para a Identidade de Serviço Gerenciada com a chamada `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)`. Dessa forma, não há nenhum segredo a ser retido e uso. O fluxo do contexto da Identidade de Serviço Gerenciada para o Hubs de Eventos e o handshake de autorização são manipulados automaticamente pelo provedor de token, que é um modelo mais simples do que o uso do SAS.

Depois de fazer essas alterações, publique e execute o aplicativo. Uma maneira fácil de obter os dados de publicação correto é baixar e, em seguida, importar um perfil de publicação no Visual Studio:

![](./media/event-hubs-managed-service-identity/msi3.png)
 
Para enviar ou receber mensagens, insira o nome do namespace e o nome da entidade que você criou e, em seguida, clique em **enviar** ou **receber**. 
 
Observe que a Identidade de Serviço Gerenciada só funciona dentro do ambiente do Azure e somente na implantação do Serviço de Aplicativo em que você a configurou. Observe também que as Identidades de Serviço Gerenciadas não funcionam com slots de implantação do Serviço de Aplicativo no momento.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Hubs de Eventos, acesse os seguintes links:

* Introdução com um [Tutorial de Hubs de Eventos](event-hubs-dotnet-standard-getstarted-send.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
* [Detalhes dos Preços de Hubs de Evento](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Aplicativos de exemplo que usam Hub de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)