---
title: Habilite o Application Insights para o serviço de aprendizado de máquina do Azure em produção
description: Saiba como definir o Application Insights para o serviço de Aprendizado de Máquina do Azure para implantação no Serviço do Azure Kubernetes
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: fa425a5ecd8cf8f4c7b3516534b4c4f0f4257850
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085335"
---
# <a name="monitor-your-azure-machine-learning-models-in-production-with-application-insights"></a>Monitore seus modelos de aprendizado de máquina do Azure em produção com o Application Insights

Neste artigo, você aprenderá como configurar o Insight do Aplicativo do Azure para o seu serviço de Aprendizado de Máquina do Azure. O Application Insights lhe dá a oportunidade de monitorar:
* Solicitar taxas, tempos de resposta e taxas de falha.
* Taxas de dependência, tempos de resposta e taxas de falha.
* Exceções.

[Saiba mais sobre o Application Insights](../../application-insights/app-insights-overview.md). 

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Um espaço de trabalho do Azure Machine Learning, um diretório local que contém seus scripts e o SDK de Aprendizado de Máquina do Azure para Python instalado. Para saber como obter esses pré-requisitos, consulte [como configurar um ambiente de desenvolvimento](how-to-configure-environment.md).
* Um modelo de aprendizado de máquina treinado para ser implantado para o serviço de Kubernetes do Azure (AKS). Se você não tiver um, consulte o tutorial [Modelo de classificação de imagem de trem](tutorial-train-models-with-aml.md).
* Uma [cluster do AKS](how-to-deploy-to-aks.md).

## <a name="enable-and-disable-in-the-portal"></a>Ativar e desativar no portal

Você pode ativar e desativar o Application Insights no portal do Azure.

### <a name="enable"></a>Habilitar

1. No [portal do Azure](https://portal.azure.com), abra o espaço de trabalho.

1. Na guia **Implantações**, selecione o serviço no qual você deseja ativar o Application Insights.

   [![Lista de serviços na guia implantações](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Selecione **Editar**.

   [![Botão Editar](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. Em **Configurações avançadas**, marque a caixa de seleção **Ativar diagnósticos do AppInsights**.

   [![Selecionada caixa de seleção para habilitar diagnósticos](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Selecione **Atualizar** na parte inferior da tela para aplicar as alterações. 

### <a name="disable"></a>Desabilitar
1. No [portal do Azure](https://portal.azure.com), abra o espaço de trabalho.
1. Selecione **implantações**, selecione o serviço e selecione **editar**.

   [![Botão Editar](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. Na **configurações avançadas**, desmarque as **AppInsights habilitar diagnóstico** caixa de seleção. 

   [![Caixa de seleção desmarcada para habilitar o diagnóstico](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Selecione **Atualizar** na parte inferior da tela para aplicar as alterações. 

## <a name="enable-and-disable-from-the-sdk"></a>Ativar e desativar o SDK

### <a name="update-a-deployed-service"></a>Atualizar um serviço implantado
1. Identificar o serviço no seu espaço de trabalho. O valor de `ws` é o nome do seu espaço de trabalho.

    ```python
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Atualizar seu serviço e habilitar o Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Rastreamentos de log personalizados em seu serviço
Se você deseja registrar rastreamentos personalizados, siga o [processo de implantação padrão para o AKS](how-to-deploy-to-aks.md). Em seguida:

1. Atualize o arquivo de pontuação adicionando instruções de impressão.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Atualize a configuração do AKS.
    
    ```python
    aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)
    ```

3. [Criar a imagem e implantá-lo](how-to-deploy-to-aks.md).  

### <a name="disable-tracking-in-python"></a>Desabilitar o controle em Python

Para desabilitar o Application Insights, use o seguinte código:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    

## <a name="evaluate-data"></a>Avaliar dados
Os dados do seu serviço são armazenados em sua conta do Application Insights, dentro do mesmo grupo de recursos que o serviço do Azure Machine Learning.
Para exibi-lo:
1. Vá para o seu grupo de recursos no [Portal do Azure](https://portal.azure.com) e navegue até o recurso Application Insights. 
2. A guia **Visão geral** mostra o conjunto básico de métricas para seu serviço.

   [![Visão geral](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Para examinar os rastreamentos personalizados, selecione **Analytics**.
4. Na seção de esquema, selecione **Traces**. Em seguida, selecione **executar** para executar sua consulta. Os dados devem aparecer em um formato de tabela e devem ser mapeados para suas chamadas personalizadas em seu arquivo de pontuação. 

   [![Rastreamentos personalizados](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Para saber mais sobre como usar o Application Insights, consulte [o que é o Application Insights?](../../application-insights/app-insights-overview.md).
    

## <a name="example-notebook"></a>Caderno de exemplo

O bloco de anotações [00.Introdução / 13.enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) demonstra conceitos neste artigo.  Veja este caderno:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas
Você também pode coletar dados em seus modelos em produção. Leia o artigo [coletar dados para modelos em produção](how-to-enable-data-collection.md). 
