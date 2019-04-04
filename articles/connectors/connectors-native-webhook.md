---
title: Criar ações ou fluxos de trabalho baseados em eventos – Aplicativos Lógicos do Azure | Microsoft Docs
description: Automatizar ações ou fluxos de trabalho baseados em evento usando webhooks e os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.topic: article
tags: connectors
ms.date: 07/21/2016
ms.openlocfilehash: c3047000843e054e71ec1a80313118a25e7c4905
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895572"
---
# <a name="create-event-based-workflows-or-actions-by-using-webhooks-and-azure-logic-apps"></a>Criar ações ou fluxos de trabalho baseados em evento usando webhooks e os Aplicativos Lógicos do Azure

Com a ação e o gatilho do webhook, você pode disparar, pausar e retomar fluxos para realizar estas tarefas:

* Disparar de um [Hub de Eventos do Azure](https://github.com/logicappsio/EventHubAPI) assim que um item é recebido
* Aguardar uma aprovação antes de continuar um fluxo de trabalho

Saiba mais sobre [como criar APIs personalizadas que dão suporte a um webhook](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>Usar o gatilho de webhook

Um [*gatilho*](../connectors/apis-list.md) é um evento que inicia um fluxo de trabalho do aplicativo lógico. O gatilho de webhook é baseado em evento, que não depende de sondagem para novos itens. Quando você salvar seu aplicativo lógico com um gatilho de webhook ou quando você altera seu aplicativo lógico de desabilitado para habilitado, o gatilho de webhook *assina* para o serviço especificado ou o ponto de extremidade, registrando um *deURLderetornodechamada* com o serviço ou ponto de extremidade. O gatilho, em seguida, usa essa URL para executar o aplicativo lógico conforme necessário. Como o [gatilho de solicitação](connectors-native-reqres.md), o aplicativo lógico é acionado imediatamente quando o evento esperado acontece. O disparador *cancela a assinatura* se você remover o gatilho e salva seu aplicativo lógico, ou quando você altera seu aplicativo lógico do habilitado para desabilitado.

Veja um exemplo que mostra como configurar um gatilho HTTP no Designer de Aplicativo Lógico. Essas etapas pressupõem que você já implantou ou está acessando uma API que segue o [padrão de assinatura e cancelamento de assinatura de webhook encontrado nos aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). 

**Para adicionar o gatilho de webhook**

1. Adicione o gatilho de **Webhook HTTP** como a primeira etapa em um aplicativo lógico.
2. Preencha os parâmetros das chamadas à assinatura e ao cancelamento da assinatura do webhook.

   Isso segue o mesmo padrão que o formato da [ação HTTP](connectors-native-http.md).

     ![Gatilho de HTTP](./media/connectors-native-webhook/using-trigger.png)

3. Adicione pelo menos uma ação.
4. Clique em **Salvar** para publicar o aplicativo lógico. Essa etapa chama o ponto de extremidade de assinatura com a URL de retorno de chamada necessário para disparar esse aplicativo lógico.
5. Sempre que o serviço fizer um `HTTP POST` para a URL de retorno de chamada, o aplicativo lógico será disparado (e incluirá todos os dados passados na solicitação).

## <a name="use-the-webhook-action"></a>Usar a ação de webhook

Uma [ *ação* ](../connectors/apis-list.md) é uma operação que é definida e o execute por fluxo de trabalho do aplicativo lógico. Quando um aplicativo lógico executa uma ação de webhook, essa ação *assina* para o serviço especificado ou o ponto de extremidade, registrando um *URL de retorno de chamada* com o serviço ou ponto de extremidade. A ação de webhook, em seguida, aguarda até que a URL antes da aplicativo lógico retoma a execução de chamadas de serviço. O aplicativo lógico cancela a assinatura do serviço ou ponto de extremidade nesses casos: 

* Quando a ação de webhook foi concluída com êxito
* Se a execução do aplicativo lógico for cancelado enquanto aguarda uma resposta
* Antes da lógica de aplicativo atinge o tempo limite

Por exemplo, o [ **enviar email de aprovação** ](connectors-create-api-office365-outlook.md) ação é um exemplo de ação de webhook que segue este padrão. Você pode estender esse padrão para qualquer serviço por meio de ação de webhook. 

Veja um exemplo que mostra como configurar uma ação de webhook no Designer de Aplicativo Lógico. Essas etapas pressupõem que você já implantou ou está acessando uma API que segue o [padrão de assinatura e cancelamento de assinatura de webhook usado em aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#webhook-actions). 

**Para adicionar uma ação de webhook**

1. Escolha **Nova Etapa** > **Adicionar uma ação**.

2. Na caixa de pesquisa, digite "webhook" para localizar a ação **Webhook HTTP**.

    ![Selecionar ação de consulta](./media/connectors-native-webhook/using-action-1.png)

3. Preencha os parâmetros das chamadas à assinatura e ao cancelamento da assinatura do webhook

   Isso segue o mesmo padrão que o formato da [ação HTTP](connectors-native-http.md).

     ![Concluir ação de consulta](./media/connectors-native-webhook/using-action-2.png)
   
   Em tempo de execução, o aplicativo lógico chama o ponto de extremidade de assinatura depois de atingir essa etapa.

4. Clique em **Salvar** para publicar o aplicativo lógico.

## <a name="technical-details"></a>Detalhes técnicos

Aqui estão mais detalhes sobre os gatilhos e ações aos quais o webhook dá suporte.

## <a name="webhook-triggers"></a>Gatilhos de Webhook

| Ação | DESCRIÇÃO |
| --- | --- |
| Webhook HTTP |Assine uma URL de retorno de chamada para um serviço que possa chamar a URL, de modo a acionar o aplicativo lógico conforme a necessidade. |

### <a name="trigger-details"></a>Detalhes do gatilho

#### <a name="http-webhook"></a>Webhook HTTP

Assine uma URL de retorno de chamada para um serviço que possa chamar a URL, de modo a acionar o aplicativo lógico conforme a necessidade.
Um * significa que o campo é obrigatório.

| Nome de exibição | Nome da Propriedade | DESCRIÇÃO |
| --- | --- | --- |
| Método de Assinatura* |estático |Método HTTP a ser usado para solicitação de assinatura |
| URI da Assinatura* |uri |URI do HTTP a ser usado para solicitação de assinatura |
| Método de Cancelamento da Assinatura* |estático |Método HTTP a ser usado para solicitação de cancelamento da assinatura |
| URI do Cancelamento da Assinatura* |uri |URI do HTTP a ser usado para solicitação de cancelamento da assinatura |
| Corpo da Assinatura |corpo |Corpo da solicitação HTTP da assinatura |
| Cabeçalhos da Assinatura |headers |Cabeçalhos da solicitação HTTP da assinatura |
| Autenticação da Assinatura |Autenticação |Autenticação HTTP a ser usada para assinatura. [Confira o artigo sobre o conector HTTP](connectors-native-http.md#authentication) para obter detalhes |
| Corpo do Cancelamento da Assinatura |body |Corpo da solicitação HTTP do cancelamento da assinatura |
| Cabeçalhos do Cancelamento da Assinatura |headers |Cabeçalhos da solicitação HTTP do cancelamento da assinatura |
| Autenticação do Cancelamento da Assinatura |autenticação |Autenticação HTTP a ser usada para cancelamento da assinatura. [Consulte o conector HTTP](connectors-native-http.md#authentication) para obter detalhes |

**Detalhes da saída**

Solicitação de webhook

| Nome da Propriedade | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| headers |objeto |Cabeçalhos da solicitação de webhook |
| Corpo |objeto |Objeto da solicitação de webhook |
| Código de status |int |Código de status da solicitação de webhook |

## <a name="webhook-actions"></a>Ações de Webhook

| Ação | DESCRIÇÃO |
| --- | --- |
| Webhook HTTP |Assine uma URL de retorno de chamada para um serviço que possa chamar a URL, de modo a retomar uma etapa do fluxo de trabalho conforme a necessidade. |

### <a name="action-details"></a>Detalhes da ação

#### <a name="http-webhook"></a>Webhook HTTP

Assine uma URL de retorno de chamada para um serviço que possa chamar a URL, de modo a retomar uma etapa do fluxo de trabalho conforme a necessidade.
Um * significa que o campo é obrigatório.

| Nome de exibição | Nome da Propriedade | DESCRIÇÃO |
| --- | --- | --- |
| Método de Assinatura* |estático |Método HTTP a ser usado para solicitação de assinatura |
| URI da Assinatura* |uri |URI do HTTP a ser usado para solicitação de assinatura |
| Método de Cancelamento da Assinatura* |estático |Método HTTP a ser usado para solicitação de cancelamento da assinatura |
| URI do Cancelamento da Assinatura* |uri |URI do HTTP a ser usado para solicitação de cancelamento da assinatura |
| Corpo da Assinatura |corpo |Corpo da solicitação HTTP da assinatura |
| Cabeçalhos da Assinatura |headers |Cabeçalhos da solicitação HTTP da assinatura |
| Autenticação da Assinatura |Autenticação |Autenticação HTTP a ser usada para assinatura. [Confira o artigo sobre o conector HTTP](connectors-native-http.md#authentication) para obter detalhes |
| Corpo do Cancelamento da Assinatura |body |Corpo da solicitação HTTP do cancelamento da assinatura |
| Cabeçalhos do Cancelamento da Assinatura |headers |Cabeçalhos da solicitação HTTP do cancelamento da assinatura |
| Autenticação do Cancelamento da Assinatura |autenticação |Autenticação HTTP a ser usada para cancelamento da assinatura. [Consulte o conector HTTP](connectors-native-http.md#authentication) para obter detalhes |

**Detalhes da saída**

Solicitação de webhook

| Nome da Propriedade | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| headers |objeto |Cabeçalhos da solicitação de webhook |
| Corpo |objeto |Objeto da solicitação de webhook |
| Código de status |int |Código de status da solicitação de webhook |

## <a name="next-steps"></a>Próximas etapas

* [Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Localizar outros conectores](apis-list.md)
