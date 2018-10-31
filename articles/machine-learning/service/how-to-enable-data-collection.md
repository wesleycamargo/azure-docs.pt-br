---
title: Ativar coleta de dados para modelos em produção - Aprendizado de Máquina do Azure
description: Aprenda a coletar dados do modelo de entrada do Aprendizado no Azure Machine em um armazenamento de Blobs do Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 09/24/2018
ms.openlocfilehash: 70c023fc8fe996060d3eff3d5a700b5f910097b4
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49113624"
---
# <a name="collect-data-for-models-in-production"></a>Coletar dados para modelos em produção

Neste artigo, você pode aprender como coletar dados de modelo de entrada dos serviços de Aprendizado de Máquina do Azure que você implantou no Azure Kubernetes Cluster (AKS) em um armazenamento de Blob do Azure. 

Depois de ativado, esses dados coletados ajudam você a:
* Monitora os desvios de dados à medida que os dados de produção entram no seu modelo

* Tome melhores decisões sobre quando treinar ou otimizar seu modelo

* Treine novamente seu modelo com os dados coletados

## <a name="what-is-collected-and-where-does-it-go"></a>O que é coletado e para onde vai?

Os dados a seguir podem ser coletados:
* Dados de **entrada** do modelo  de serviços da Web implantados no Azure Kubernetes Cluster (AKS) (voz, imagens e vídeo **não** coletadas) 
  
* Predições de modelo usando dados de entrada de produção.

> [!Note]
> Pré-agregação ou pré-cálculos sobre esses dados não fazem parte do serviço no momento.   

A saída é salva em um Blob do Azure. Como os dados são adicionados a um Blob do Azure, você pode escolher sua ferramenta favorita para executar a análise. 

O caminho para os dados de saída no blob segue esta sintaxe:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Um workspace do serviço do Azure Machine Learning, um diretório local que contém seus scripts, e o SDK do Azure Machine Learning para Python instalado. Aprenda como obter esses pré-requisitos usando o documento [Como configurar um ambiente de desenvolvimento](how-to-configure-environment.md).

- Um modelo de aprendizado de máquina treinado para ser implantado para o serviço de Kubernetes do Azure (AKS). Se você não tiver um, consulte o tutorial [modelo de classificação de imagens de trem](tutorial-train-models-with-aml.md).

- Uma [cluster do AKS](how-to-deploy-to-aks.md).

- As seguintes dependências e módulo instalado [em seu ambiente](how-to-configure-environment.md):
  + No Linux:
    ```shell
    sudo apt-get install libxml++2.6-2v5
    pip install azureml-monitoring
    ```

  + No Windows:
    ```shell
    pip install azureml-monitoring
    ```

## <a name="enable-data-collection"></a>Habilitar coleta de dados
A coleta de dados pode ser ativada independentemente do modelo que está sendo implantado por meio do Serviço de Aprendizado de Máquina do Azure ou de outras ferramentas. 

Para habilitá-lo, você precisa:

1. Abra o arquivo de pontuação. 

1. Adicione o seguinte código na parte superior do arquivo:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Declare suas variáveis de coleta de dados na sua função `init()`:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* é um parâmetro opcional, você não precisa configurá-lo se seu modelo não exigir isso. Ter uma correlação em vigor ajuda você a mapear com mais facilidade outros dados. (Exemplos incluem: LoanNumber, CustomerId, etc.)
    
    *Identificador* é usado mais tarde para construir a estrutura de pastas no seu Blob, ele pode ser usado para dividir dados "brutos" versus "processados".

3.  Adicione as seguintes linhas de código à função `run(input_df)`:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. A coleta de dados é **não** automaticamente definida como **verdadeiro** quando você implanta um serviço no AKS, portanto, você deve atualizar seu arquivo de configuração, como: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    O AppInsights para monitoramento de serviço também pode ser ativado alterando essa configuração:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. [Criar nova imagem e implantar seu serviço.](how-to-deploy-to-aks.md) 


Se você já tiver um serviço com as dependências instaladas no **arquivo de ambiente** e no **arquivo de pontuação**, ative a coleta de dados:

1. Vá para [Portal do Azure](https://portal.azure.com).

1. Abra seu workspace.

1. Vá para **Implantações** -> **Selecione o serviço** -> **Edite**.

   ![Editar serviço](media/how-to-enable-data-collection/EditService.png)

1. Em **Configurações avançadas**, desmarque **Ativar coleta de dados do modelo**. 

   ![Desmarque a opção de coleta de dados](media/how-to-enable-data-collection/CheckDataCollection.png)

   Nesta janela, você também pode escolher "Ativar diagnósticos de Appinsights" para rastrear a integridade de seu serviço.  

1. Selecione **atualização** para aplicar a alteração.


## <a name="disable-data-collection"></a>Desativar coleta de dados
Você pode parar de coletar dados a qualquer momento. Use o código Python ou o portal do Azure para desabilitar a coleta de dados.

+ Opção 1 - desabilitar no portal do Azure: 
  1. Entre no [Portal do Azure](https://portal.azure.com).

  1. Abra seu workspace.

  1. Vá para **Implantações** -> **Selecione o serviço** -> **Edite**.

     ![Editar serviço](media/how-to-enable-data-collection/EditService.png)

  1. Em **Configurações avançadas**, desmarque **Ativar coleta de dados do modelo**. 

     ![Desmarque a coleta de dados](media/how-to-enable-data-collection/UncheckDataCollection.png) 

  1. Selecione **Atualizar** para aplicar a alteração.

* Opção 2 - Use o Python para desabilitar a coleta de dados:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="example-notebook"></a>Caderno de exemplo

O bloco de anotações [00.Getting Started/12.enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/12.enable-data-collection-for-models-in-aks) demonstra conceitos deste artigo.  

Veja este caderno:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]