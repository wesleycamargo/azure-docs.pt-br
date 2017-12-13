---
title: Monitorar APIs publicadas no Gerenciamento de API do Azure | Microsoft Docs
description: Siga as etapas deste tutorial para aprender a monitorar sua API no Gerenciamento de API do Azure.
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: bdca9d4968e9e68314f350787907f15e417821f7
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="monitor-published-apis"></a>Monitorar APIs publicadas

O Azure Monitor é um serviço do Azure que fornece uma única fonte para monitorar todos os recursos do Azure. Com o Azure Monitor, é possível visualizar, consultar, rotear, arquivar e executar ações nas métricas e nos logs provenientes dos recursos do Azure como o Gerenciamento de API. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Exibir logs de atividade
> * Exibir logs de diagnóstico
> * Exibir métricas da API 
> * Configurar uma regra de alerta quando a API recebe chamadas não autorizadas

O vídeo a seguir mostra como monitorar o Gerenciamento de API usando o Azure Monitor. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>

## <a name="prerequisites"></a>Pré-requisitos

+ Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Além disso, conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="diagnostic-logs"></a>Exibir logs de atividade

Os logs de atividades fornecem informações sobre as operações que foram realizadas em seus serviços de Gerenciamento de API. Usando logs de atividades, é possível determinar “o que, quem e quando” para quaisquer operações de gravação (PUT, POST, DELETE) realizadas em seus serviços de Gerenciamento de API. 

> [!NOTE]
> Os logs de atividades não incluem operações de leitura (GET) ou operações realizadas no Portal do Publicador clássico ou usando as APIs de gerenciamento original.

É possível acessar os logs de atividades em seu serviço de Gerenciamento de API ou acessar logs de todos os seus recursos do Azure no Azure Monitor. 

Para exibir logs de atividade:

1. Na sua instância do **Gerenciamento de API**, clique em **Log de atividades**.

## <a name="view-diagnostic-logs"></a>Exibir logs de diagnóstico

Os logs de diagnóstico fornecem informações avançadas sobre operações e erros importantes para auditoria, bem como para fins de solução de problemas. Os logs de diagnóstico são diferentes dos logs de atividades. Os logs de atividades fornecem informações sobre as operações realizadas em seus recursos do Azure. Os Logs de Diagnóstico fornecem informações em operações que o recurso realizou por conta própria.

Para acessar logs de diagnóstico:

1. Na sua instância do **Gerenciamento de API**, clique em **Log de diagnóstico**.

## <a name="view-metrics-of-your-apis"></a>Exibir métricas das APIs

O Gerenciamento de API emite métricas a cada minuto, permitindo uma visibilidade quase em tempo real do estado e da integridade de suas APIs. A seguir está um resumo de algumas das métricas disponíveis:

* Capacidade (versão prévia): ajuda a tomar decisões sobre seus atualizar/fazer downgrade dos seus serviços do APIM. A métrica é emitida por minuto e reflete a capacidade do gateway no momento da emissão dos relatórios. A métrica varia de 0 a 100 e é calculada com base nos recursos corretos do gateway, como a utilização da CPU e a memória.
* Total de solicitações de gateway: o número de solicitações de API no período. 
* Solicitações de gateway bem-sucedidas: o número de solicitações de API que recebeu códigos de resposta HTTP bem-sucedidos, incluindo 304, 307 e unidades menores que 301 (por exemplo, 200). 
* Solicitações de gateway com falha: o número de solicitações de API que recebeu códigos de resposta HTTP errôneos, incluindo 400 e qualquer coisa maior do que 500.
* Solicitações de gateway não autorizadas: o número de solicitações de API que recebeu códigos de resposta HTTP, incluindo 401, 403 e 429. 
* Outras solicitações de gateway: o número de solicitações de API que recebeu códigos de resposta HTTP que não pertencem a nenhuma das categorias acima (por exemplo, 418).

Para acessar as métricas:

1. Selecione **Métricas** no menu na parte inferior da página.
2. Na lista suspensa, selecione a métrica que interessa (você pode adicionar várias métricas). 
    
    Por exemplo, selecione **Total de Solicitações de Gateway** e **Solicitações de Gateway com Falha** na lista de métricas disponíveis.
3. O gráfico mostra o número total de chamadas à API. Ele também mostra o número de chamadas à API com falha. 

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Configurar uma regra de alerta para a solicitação não autorizada

É possível configurar para receber alertas com base em métricas e logs de atividades. O Azure Monitor permite configurar um alerta para que ele faça o seguinte quando for acionado:

* Enviar uma notificação por email
* Chamar um webhook
* Invocar um aplicativo lógico do Azure

Para configurar alertas:

1. Selecione **Regras de alerta** na barra de menus na parte inferior da página.
2. Selecione **Adicionar alerta de métrica**.
3. Insira um **Nome** para o alerta.
4. Selecione **Solicitações de Gateway Não Autorizadas** como a métrica a ser monitorada.
5. Selecione **Leitores, colaboradores e proprietários de email**.
6. Pressione **OK**.
7. Tente chamar nossa API de conferência sem uma chave de API. Como proprietário do serviço Gerenciamento de API, você receberá um alerta de email. 

    > [!TIP]
    > A regra de alerta também pode chamar um Webhook ou um Aplicativo Lógico do Azure quando acionado.

    ![set-up-alert](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Exibir logs de atividade
> * Exibir logs de diagnóstico
> * Exibir métricas da API 
> * Configurar uma regra de alerta quando a API recebe chamadas não autorizadas

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Rastrear chamadas](api-management-howto-api-inspector.md)