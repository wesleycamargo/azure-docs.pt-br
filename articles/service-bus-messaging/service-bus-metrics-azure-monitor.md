---
title: Métricas do Barramento de Serviço no Azure Monitor (versão prévia) | Microsoft Docs
description: Use o Monitoramento do Azure para monitorar entidades de Barramento de Serviço
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 11/06/2018
ms.author: aschhab
ms.openlocfilehash: 175d5d5d4495986c29b75427a325088c14279e17
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60308514"
---
# <a name="azure-service-bus-metrics-in-azure-monitor-preview"></a>Métricas do Barramento de Serviço do Azure no Azure Monitor (versão prévia)

As métricas do Barramento de Serviço fornecem o estado dos recursos na sua assinatura do Azure. Com um amplo conjunto de dados de métricas, você pode avaliar a integridade geral dos seus recursos de Barramento de Serviço não apenas no nível de namespace, mas também no nível de entidade. Essas estatísticas podem ser importantes, pois elas ajudam você a monitorar o estado do Barramento de Serviço. As métricas também podem ajudar a solucionar problemas de causa raiz sem a necessidade de entrar em contato com o suporte do Azure.

O Azure Monitor fornece interfaces de usuário unificadas para monitoramento entre os diferentes serviços do Azure. Para obter mais informações, consulte [Monitoramento no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e o exemplo [Recuperar métricas do Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) no GitHub.

> [!IMPORTANT]
> Quando não houver nenhuma interação com uma entidade por 2 horas, as métricas começarão a mostrar "0" como um valor até que a entidade não esteja mais ociosa.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor fornece várias maneiras de acessar as métricas. Você pode acessar as métricas por meio de [portal do Azure](https://portal.azure.com), ou usar as APIs do Azure Monitor (REST e .NET) e soluções de análise como logs do Azure Monitor e Hubs de eventos. Para obter mais informações, consulte [métricas no Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

As métricas estão habilitadas por padrão e você pode acessar os dados dos últimos 30 dias. Se você precisar manter os dados por um período de tempo maior, você pode arquivar os dados de métrica em uma conta de Armazenamento do Azure. Esse valor pode ser configurado em [configurações de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) no Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Acessar as métricas no portal

É possível monitorar as métricas ao longo do tempo no [Portal do Azure](https://portal.azure.com). O exemplo a seguir mostra como exibir solicitações bem-sucedidas e solicitações de entrada no nível da conta:

![][1]

Você também pode acessar as métricas diretamente por meio do namespace. Para fazer isso, selecione seu namespace e clique em **Métricas (versão prévia)**. Para exibir as métricas filtradas para o escopo da entidade, selecione a entidade e clique em **Métricas (versão prévia)**.

![][2]

Para métricas com suporte para dimensões, você deve filtrar pelo valor da dimensão desejado.

## <a name="billing"></a>Cobrança

O uso de métricas no Azure Monitor é grátis durante a versão prévia. No entanto, se você usar outras soluções que ingerem dados de métricas, você poderá ser cobrado por essas soluções. Por exemplo, você será cobrado pelo Armazenamento do Azure se você arquivar dados de métrica para uma conta de Armazenamento do Azure. Você também será cobrado pelos logs do Azure Monitor se você transmitir dados de métricas para os logs do Azure Monitor para análise avançada.

As métricas a seguir oferecem uma visão geral da integridade do seu serviço. 

> [!NOTE]
> Diversas métricas estão sendo preteridas à medida que são transferidas para um nome diferente. Isso pode exigir que você atualize suas referências. Métricas marcadas com a palavra-chave "preterida" não terão suporte no futuro.

Todos os valores de métricas são enviados para o Azure Monitor a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo com suporte para todas as métricas do Barramento de Serviço é de 1 minuto.

## <a name="request-metrics"></a>Métricas de solicitação

Conta o número de solicitações de gerenciamento de dados e de operações.

| Nome da métrica | DESCRIÇÃO |
| ------------------- | ----------------- |
| Solicitações de entrada (versão prévia) | O número de solicitações feitas ao serviço de Barramento de Serviço durante um período específico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Solicitações bem-sucedidas (versão prévia)|O número de solicitações bem-sucedidas feitas ao serviço de Barramento de Serviço durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Erros do servidor (versão prévia)|O número de solicitações não processadas devido a um erro no serviço de Barramento de Serviço durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Erros do Usuário (versão prévia – veja a subseção a seguir)|O número de solicitações não processadas devido a erros do usuário durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Solicitações restritas (versão prévia)|O número de solicitações que foram restringidas porque o uso foi excedido.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

### <a name="user-errors"></a>Erros do usuário

Os dois tipos de erros a seguir são classificados como erros do usuário:

1. Erros do lado do cliente (em HTTP, seriam os erros 400).
2. Erros que ocorrem durante o processamento de mensagens, como [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Métricas de mensagens

| Nome da métrica | DESCRIÇÃO |
| ------------------- | ----------------- |
|Mensagens de entrada (versão prévia)|O número de eventos ou mensagens enviadas para o Barramento de Serviço durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Mensagens de saída (versão prévia)|O número de eventos ou mensagens recebidas do Barramento de Serviço durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
| Mensagens (versão prévia) | Contagem de mensagens em uma fila/tópico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Dimensão: EntityName |
| Mensagens ativas (versão prévia) | Contagem de mensagens ativas em uma fila/tópico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Dimensão: EntityName |

## <a name="connection-metrics"></a>Métricas de conexão

| Nome da métrica | DESCRIÇÃO |
| ------------------- | ----------------- |
|ActiveConnections (versão prévia)|O número de conexões ativas em um namespace, bem como em uma entidade.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Conexões abertas (versão prévia)|O número de conexões abertas.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Conexões fechadas (versão prévia)|O número de conexões fechadas.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |

## <a name="resource-usage-metrics"></a>Métricas de uso de recurso

> [!NOTE] 
> As métricas a seguir estão disponíveis apenas com a camada **Premium**. 

| Nome da métrica | DESCRIÇÃO |
| ------------------- | ----------------- |
|Uso da CPU por namespace (versão prévia)|A porcentagem de uso da CPU do namespace.<br/><br/> Unidade: Porcentagem <br/> Tipo de agregação: Máximo <br/> Dimensão: EntityName|
|Uso do tamanho da memória por namespace (versão prévia)|A porcentagem de uso de memória do namespace.<br/><br/> Unidade: Porcentagem <br/> Tipo de agregação: Máximo <br/> Dimensão: EntityName|

## <a name="metrics-dimensions"></a>Dimensões das métricas

O Barramento de Serviço do Azure dá suporte às seguintes dimensões para métricas no Azure Monitor. Adicionar dimensões às métricas é opcional. Se você não adicionar dimensões, as métricas serão especificadas no nível de namespace. 

|Nome da dimensão|DESCRIÇÃO|
| ------------------- | ----------------- |
|EntityName| O Barramento de Serviço dá suporte a entidades de mensagens no namespace.|

## <a name="set-up-alerts-on-metrics"></a>Configurar alertas em métricas

1. Na guia **Métrica** da página **Namespace do barramento de serviço**, selecione **Configurar alertas**. 

    ![Página de métrica – Configurar o menu de alertas](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Selecione **Selecionar destino** e realize as seguintes ações na página **Selecionar um recurso**: 
    1. Selecione **Namespaces de barramento de serviço** para o campo **Filtrar por tipo de recurso**. 
    2. Selecione a assinatura no campo **Filtrar por assinatura**.
    3. Selecione o **namespace de barramento de serviço** na lista. 
    4. Selecione **Concluído**. 
    
        ![Selecionar namespace](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Selecione **Adicionar critérios** e realize as seguintes ações na página **Configurar lógica de sinal**:
    1. Selecione **Métrica** em **Tipo de sinal**. 
    2. Selecione um sinal. Por exemplo:  **Erros de serviço (versão prévia)**. 

        ![Selecionar erros de servidor](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Selecione **Maior que** em **Condição**.
    2. Selecione **Total** em **Agregação de tempo**. 
    3. Insira **5** em **Limite**. 
    4. Selecione **Concluído**.    

        ![Especificar condição](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Na página **Criar regra**, expanda **Definir detalhes do alerta** e realize as seguintes ações:
    1. Insira um **nome** para o alerta. 
    2. Insira uma **descrição** para o alerta.
    3. Selecione a **gravidade** do alerta. 

        ![Detalhes do Alerta](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Na página **Criar regra**, expanda **Definir grupo de ações**, selecione **Novo grupo de ações** e realize as seguintes ações em **Adicionar página de grupo de ações**. 
    1. Insira um nome para o grupo de ações.
    2. Insira um nome curto para o grupo de ações. 
    3. Selecione sua assinatura. 
    4. Selecione um grupo de recursos. 
    5. Neste passo a passo, insira **Enviar email** em **NOME DA AÇÃO**.
    6. Selecione **Email/SMS/Push/Voz** em **TIPO DE AÇÃO**. 
    7. Selecione em **Editar detalhes**. 
    8. Na página **Email/SMS/Push/Voz**, realize as seguintes ações:
        1. Selecione **Email**. 
        2. Digite **o endereço de email**. 
        3. Selecione **OK**.

            ![Detalhes do Alerta](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Na página **Adicionar grupo de ações**, selecione **OK**. 
1. Na página **Criar regra**, selecione **Criar regra de alerta**. 

    ![Botão Criar regra de alerta](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Próximos passos

Consulte [Visão geral do Monitoramento do Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


