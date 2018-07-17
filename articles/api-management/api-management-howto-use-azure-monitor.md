---
title: Monitorar APIs publicadas no Gerenciamento de API do Azure | Microsoft Docs
description: Siga as etapas deste tutorial para aprender a monitorar sua API no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: b4c885758f572851f058edb6e7851d650faed9f9
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972991"
---
# <a name="monitor-published-apis"></a>Monitorar APIs publicadas

Com o Azure Monitor, você pode visualizar, consultar, rotear, arquivar e tomar ações nas métricas ou nos logs provenientes dos recursos do Azure.

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

## <a name="prerequisites"></a>pré-requisitos

+ Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Além disso, conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).

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

    ![Métricas](./media/api-management-azure-monitor/api-management-metrics-blade.png)

2. Na lista suspensa, selecione a métrica que interessa (você pode adicionar várias métricas).  
    Por exemplo, selecione **Total de Solicitações de Gateway** e **Solicitações de Gateway com Falha** na lista de métricas disponíveis.
3. O gráfico mostra o número total de chamadas à API. Ele também mostra o número de chamadas à API com falha.

    ![gráfico de métricas](./media/api-management-azure-monitor/apim-monitor-metrics.png)

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Configurar uma regra de alerta para a solicitação não autorizada

É possível configurar para receber alertas com base em métricas e logs de atividades. O Azure Monitor permite configurar um alerta para que ele faça o seguinte quando for acionado:

* Enviar uma notificação por email
* Chamar um webhook
* Invocar um aplicativo lógico do Azure

Para configurar alertas:

1. Selecione **Alertas (clássicos)** na barra de menus na parte inferior da página.

    ![alertas](./media/api-management-azure-monitor/api-management-alert-rules-blade.png)

2. Selecione **Adicionar alerta de métrica**.
3. Insira um **Nome** para o alerta.
4. Selecione **Solicitações de Gateway Não Autorizadas** como a métrica a ser monitorada.
5. Selecione **Leitores, colaboradores e proprietários de email**.
6. Pressione **OK**.
7. Tente chamar a API de conferência sem uma chave de API. Como proprietário do serviço Gerenciamento de API, você receberá um alerta de email. 

    > [!TIP]
    > A regra de alerta também pode chamar um Webhook ou um Aplicativo Lógico do Azure quando acionado.

    ![set-up-alert](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="activity-logs"></a>Logs de atividade

Os logs de atividades fornecem informações sobre as operações que foram realizadas em seus serviços de Gerenciamento de API. Usando logs de atividades, é possível determinar “o que, quem e quando” para quaisquer operações de gravação (PUT, POST, DELETE) realizadas em seus serviços de Gerenciamento de API.

> [!NOTE]
> Os logs de atividades não incluem operações de leitura (GET) ou operações realizadas no portal do Azure ou usando as APIs de gerenciamento original.

É possível acessar os logs de atividades em seu serviço de Gerenciamento de API ou acessar logs de todos os seus recursos do Azure no Azure Monitor. 

Para exibir logs de atividade:

1. Selecione a instância de serviço do APIM.
2. Clique em **Log de atividades**.

    ![log de atividades](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. Selecione o escopo de filtragem desejado e clique em **Aplicar**.

    ![logs de atividade](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

## <a name="diagnostic-logs"></a>Logs de Diagnóstico

Os logs de diagnóstico fornecem informações avançadas sobre operações e erros importantes para auditoria, bem como para fins de solução de problemas. Os logs de diagnóstico são diferentes dos logs de atividades. Os logs de atividades fornecem informações sobre as operações realizadas em seus recursos do Azure. Os Logs de Diagnóstico fornecem informações em operações que o recurso realizou.

Para configurar logs de diagnóstico:

1. Selecione a instância de serviço do APIM.
2. Clique em **Logs de diagnóstico**.

    ![logs de diagnóstico](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. Clique em **Ativar diagnóstico**. Você pode arquivar os logs de diagnóstico junto com a métrica em uma conta de armazenamento, transmiti-los para um Hub de Eventos ou enviá-los para o Log Analytics. 

No momento, o Gerenciamento de API oferece logs de diagnóstico (agrupados por hora) sobre solicitações de API individuais em que cada entrada tem o seguinte esquema:

```json
{  
    "isRequestSuccess" : "",
    "time": "",
    "operationName": "",
    "category": "",
    "durationMs": ,
    "callerIpAddress": "",
    "correlationId": "",
    "location": "",
    "httpStatusCodeCategory": "",
    "resourceId": "",
    "properties": {   
        "method": "", 
        "url": "", 
        "clientProtocol": "", 
        "responseCode": , 
        "backendMethod": "", 
        "backendUrl": "", 
        "backendResponseCode": ,
        "backendProtocol": "",  
        "requestSize": , 
        "responseSize": , 
        "cache": "", 
        "cacheTime": "", 
        "backendTime": , 
        "clientTime": , 
        "apiId": "",
        "operationId": "", 
        "productId": "", 
        "userId": "", 
        "apimSubscriptionId": "", 
        "backendId": "",
        "lastError": { 
            "elapsed" : "", 
            "source" : "", 
            "scope" : "", 
            "section" : "" ,
            "reason" : "", 
            "message" : ""
        } 
    }      
}  
```

| Propriedade  | Tipo | DESCRIÇÃO |
| ------------- | ------------- | ------------- |
| isRequestSuccess | booleano | True se a solicitação HTTP foi concluída com código de status de resposta dentro do intervalo 2xx a 3xx |
| tempo real | date-time | Carimbo de data/hora de recebimento da solicitação HTTP pelo gateway |
| operationName | string | Valor de constante 'Microsoft.ApiManagement/GatewayLogs' |
| categoria | string | Valor da constante 'GatewayLogs' |
| durationMs | inteiro | Número de milissegundos do momento em que o gateway recebeu a solicitação até o momento em que a resposta foi enviada por completo |
| callerIpAddress | string | Endereço IP do chamador imediato do Gateway (pode ser um intermediário) |
| correlationId | string | Identificador da solicitação HTTP exclusivo atribuído pelo Gerenciamento de API |
| location | string | Nome da região do Azure em que o gateway que processou a solicitação está localizado |
| httpStatusCodeCategory | string | Categoria do código de status de resposta HTTP: Bem-sucedida (301 ou menos ou 304 ou 307), Não autorizada (401, 403, 429), Com erro (400, entre 500 e 600), outros |
| ResourceId | string | "ID do recurso Gerenciamento de API /SUBSCRIPTIONS/<subscription>/RESOURCEGROUPS/<grupo-de-recursos>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/<name> |
| propriedades | objeto | Propriedades da solicitação atual |
| estático | string | Método HTTP da solicitação de entrada |
| url | string | URL da solicitação de entrada |
| clientProtocol | string | Versão do protocolo HTTP da solicitação de entrada |
| responseCode | inteiro | Código de status de resposta HTTP enviada a um cliente |
| backendMethod | string | Método HTTP da solicitação enviada a um back-end |
| backendUrl | string | URL da solicitação enviada a um back-end |
| backendResponseCode | inteiro | Código de resposta HTTP recebida de um back-end |
| backendProtocol | string | Versão do protocolo HTTP da solicitação enviada a um back-end | 
| requestSize | inteiro | Número de bytes recebidos de um cliente durante o processamento da solicitação | 
| responseSize | inteiro | Número de bytes enviados a um cliente durante o processamento da solicitação | 
| cache | string | Status de envolvimento do cache do Gerenciamento de API no processamento da solicitação (ou seja, ocorrências, ignorado, nenhum) | 
| cacheTime | inteiro | Número de milissegundos gastos na E/S de cache do Gerenciamento de API no total (conectando, enviando e recebendo bytes) | 
| backendTime | inteiro | Número de milissegundos gastos na E/S do back-end no total (conectando, enviando e recebendo bytes) | 
| clientTime | inteiro | Número de milissegundos gastos na E/S geral do cliente (conectando, enviando e recebendo bytes) | 
| apiId | string | Identificador da entidade de API para a solicitação atual | 
| operationId | string | Identificador da entidade de operação para a solicitação atual | 
| productId | string | Identificador da entidade de produto para a solicitação atual | 
| userId | string | Identificador da entidade de usuário para a solicitação atual | 
| apimSubscriptionId | string | Identificador da entidade de assinatura para a solicitação atual | 
| backendId | string | Identificador da entidade de back-end para a solicitação atual | 
| LastError | objeto | Erro de processamento da última solicitação | 
| decorrido | inteiro | Número de milissegundos decorridos desde que o gateway recebeu a solicitação até o momento em que ocorreu o erro | 
| fonte | string | Nome da política ou do manipulador interno de processamento que causou o erro | 
| scope | string | Escopo do documento de política que contém a política que causou o erro | 
| seção | string | Seção do documento de política que contém a política que causou o erro | 
| reason | string | Motivo do erro | 
| Message | string | Mensagem de erro | 

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
