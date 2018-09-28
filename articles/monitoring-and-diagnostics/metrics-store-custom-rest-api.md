---
title: Enviar métricas personalizadas de um recurso do Azure para o repositório de métricas do Azure Monitor usando uma API REST
description: Enviar métricas personalizadas de um recurso do Azure para o repositório de métricas do Azure Monitor usando uma API REST
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: c01440437eae3cb076627ab9f2221e33b833f472
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977223"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-using-a-rest-api"></a>Enviar métricas personalizadas de um recurso do Azure para o repositório de métricas do Azure Monitor usando uma API REST

Este artigo mostra como enviar métricas personalizadas de recursos do Azure para o repositório de métricas do Azure Monitor por meio da API REST.  Depois que as métricas estiverem no Azure Monitor, você poderá fazer tudo o que já faz com as métricas padrão, como criar gráficos, gerar alertas, encaminhá-las para outras ferramentas externas, etc.  

>[!NOTE] 
>A API REST só permite o envio de métricas personalizadas de recursos do Azure. Para enviar métricas de recursos em ambientes diferentes ou locais, use o [Application Insights](../application-insights/app-insights-api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Criar e autorizar a entidade de serviço para emitir métricas 

Crie uma entidade de serviço no seu locatário do Azure Active Directory usando as instruções em [Criar uma entidade de serviço](../azure-resource-manager/resource-group-create-service-principal-portal.md). 

Observe o seguinte ao percorrer este processo: 

- Você pode colocar qualquer URL para a URL de logon.  
- Criar novo segredo do cliente para este aplicativo  
- Salve a chave e a ID do cliente para serem usadas em etapas posteriores.  

Dê ao aplicativo criado na etapa 1 as permissões de "Editor de métricas de monitoramento" para o recurso do qual deseja emitir métricas. Se você planeja usar o aplicativo para emitir métricas personalizadas de vários recursos, conceda essas permissões no nível da assinatura ou do grupo de recursos. 

## <a name="get-an-authorization-token"></a>Use um token de autorização
Abra um prompt de comando e execute o seguinte comando
```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step> " -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Salve o token de acesso da resposta

![Token de Acesso](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Emitir a métrica por meio da API REST 

1. Cole o JSON a seguir em um arquivo e salve-o como custommetric.json no computador local. Atualize o parâmetro de tempo no arquivo JSON. 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20”, 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. Na janela do prompt de comando, poste os dados de métrica 
    - Região do Azure – deve corresponder à região de implantação do recurso do qual você está emitindo métricas. 
    - ResourceID – ID do recurso do Azure cuja métrica você está acompanhando.  
    - Token de acesso – cole o token já adquirido

    ```Shell 
    curl -X POST curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId> /metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
    ```
1. Altere o carimbo de data/hora e os valores no arquivo JSON. 
1. Repita as duas etapas anteriores algumas vezes para que você tenha dados de vários minutos.

## <a name="troubleshooting"></a>solução de problemas 
Se você receber um erro em alguma parte do processo, considerando o seguinte

1. Você não consegue emitir métrica em relação a uma assinatura ou um grupo de recursos como seu recurso do Azure. 
1. Você não consegue colocar no repositório uma métrica com mais de 20 minutos. O repositório de métricas é otimizado para alertas e criação de gráficos em tempo real. 
2. O número de nomes de dimensão deve corresponder aos valores e vice-versa. Verifique os valores. 
2. Você pode estar emitindo métrica em relação a uma região que não dá suporte a métricas personalizadas. Confira [regiões de métrica personalizada (versão prévia) com suporte](metrics-custom-overview.md#supported-regions) 



## <a name="view-your-metrics"></a>Exibir as métricas 

1. Faça logon no Portal do Azure 

1. No menu à esquerda, clique em **Monitorar** 

1. Na página Monitorar, clique em **Métricas**. 

   ![Token de Acesso](./media/metrics-store-custom-rest-api/metrics.png) 

1. Altere o período de agregação para **Últimos 30 minutos**.  

1. Na lista suspensa de *recursos*, selecione o recurso para o qual você emitiu a métrica.  

1. Na lista suspensa de *namespaces*, selecione **QueueProcessing** 

1. Na lista suspensa de *métricas*, selecione **QueueDepth**.  

 
## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as [métricas personalizadas](metrics-custom-overview.md).