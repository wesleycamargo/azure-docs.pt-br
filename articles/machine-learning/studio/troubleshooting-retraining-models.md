---
title: "Solução de Problemas de readaptação de um serviço Web clássico do Azure Machine Learning | Microsoft Docs"
description: "Identifique e corrija os problemas comuns encontrados quando você está readaptando o modelo para um serviço Web do Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondl
editor: 
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 011/01/2017
ms.author: garye
ms.openlocfilehash: 1e5327ad135d9bc8881354679dc3f1b8a472cad3
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2017
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Solução de problemas de readaptação de um serviço Web Clássico de Azure Machine Learning
## <a name="retraining-overview"></a>Visão geral da readaptação
Quando você implanta um experimento de previsão como um serviço Web de pontuação, ele é um modelo estático. Conforme novos dados ficam disponíveis ou quando o consumidor da API tem seus próprios dados, o modelo precisa ser readaptado. 

Para obter uma explicação completa do processo de readaptação de um serviço Web Clássico, consulte [Readaptar os modelos de Machine Learning de maneira programática](retrain-models-programmatically.md).

## <a name="retraining-process"></a>Processo de readaptação
Quando você precisa readaptar o serviço Web, deve adicionar algumas partes extras:

* Um serviço Web implantado usando o Teste de Treinamento. O teste deve ter um módulo de **Saída do serviço Web** anexado à saída do módulo do **Modelo de treinamento**.  
  
    ![Anexe a saída do serviço Web ao modelo de treinamento.][image1]
* Um novo ponto de extremidade adicionado ao seu serviço Web de pontuação.  Você pode adicionar o ponto de extremidade programaticamente usando o código de exemplo referenciado no tópico Readaptar os modelos de Machine Learning de maneira programática ou por meio do portal de Serviços Web do Azure Machine Learning.

Em seguida, pode usar o código C# de exemplo na página de ajuda da API do Serviço Web de Treinamento para readaptar o modelo. Depois de ter avaliado os resultados e ficar satisfeito com eles, você atualizará o serviço Web de pontuação do modelo treinado usando o novo ponto de extremidade adicionado.

Com todas as peças no lugar, as principais etapas necessárias para readaptar o modelo são as seguintes:

1. Chame o serviço Web de treinamento: a chamada é para o BES (Serviço de Execução em Lote), não o RRS (Serviço de Resposta a Solicitação). Você pode usar o Código c# de exemplo na página de ajuda da API para fazer a chamada. 
2. Encontre os valores para *BaseLocation*, *RelativeLocation* e *SasBlobToken*: esses valores são retornados na saída de sua chamada para o Serviço Web de treinamento. 
   ![exibindo a saída do exemplo de readaptação e os valores BaseLocation, RelativeLocation e SasBlobToken.][image6]
3. Atualize o ponto de extremidade adicionado do serviço Web de pontuação com o novo modelo treinado: usando o código de exemplo fornecido em Readaptar os modelos de Machine Learning de forma programática, atualize o novo ponto de extremidade adicionado ao modelo de pontuação com o modelo treinado recentemente do Serviço Web de Treinamento.

## <a name="common-obstacles"></a>Obstáculos comuns
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Verifique se você tem a URL correta do PATCH
A URL do PATCH que você está usando deve ser a associada ao novo ponto de extremidade de pontuação adicionado ao serviço Web de pontuação. Há várias maneiras de obter a URL do PATCH:

**Opção 1: use um programa**

Para obter a URL correta do PATCH:

1. Execute o código de exemplo [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .
2. Na saída do AddEndpoint, encontre o valor *HelpLocation* e copie a URL.
   
   ![HelpLocation na saída do exemplo addEndpoint.][image2]
3. Cole a URL em um navegador para navegar até uma página que fornece links de ajuda para o serviço Web.
4. Clique no link **Atualizar Recurso** para abrir a página de ajuda do patch.

**Opção 2: usar o portal de Serviços Web do Azure Machine Learning**

1. Entre no portal [Serviços Web do Azure Machine Learning](https://services.azureml.net/).
2. Clique em **Serviços Web** ou **Serviços Web Clássicos** na parte superior.
4. Clique no serviço Web de pontuação com o qual você está trabalhando (se você não modificar o nome padrão do serviço Web, ele terminará em "[Pontuação Exp.]").
5. Clique em **+NOVO**.
6. Depois do ponto de extremidade ser adicionado, clique no nome dele.
7. No URL do **Patch**, clique em **Ajuda da API** para abrir a página de ajuda de aplicação de patch.

> [!NOTE]
> Caso tenha adicionado o ponto de extremidade ao Serviço Web de Treinamento, em vez do Serviço Web de Previsão, você receberá o seguinte erro ao clicar no link **Atualizar Recurso**: “Desculpe, mas esse recurso não é compatível nem está disponível neste contexto. Este serviço Web não tem recursos atualizáveis. Pedimos desculpas pelo inconveniente e estamos trabalhando para melhorar esse fluxo de trabalho.”
> 
> 

A página de ajuda do PATCH contém a URL do PATCH que você deve usar e fornece o código de exemplo que você pode usar para chamar.

![URL de patch.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Verifique se você está atualizando o ponto de extremidade correto de pontuação
* Não corrija o serviço Web de treinamento: a operação do patch deve ser executada no serviço Web de pontuação.
* Não corrija o ponto de extremidade padrão no serviço Web: a operação do patch deve ser executada no novo ponto de extremidade de serviço Web de pontuação que você adicionou.

Você pode verificar em qual serviço Web está o ponto de extremidade acessando o portal de Serviços Web. 

> [!NOTE]
> Certifique-se de que você está adicionando o ponto de extremidade ao serviço Web de previsão e não ao serviço da Web de treinamento. Se você tiver implantado corretamente um serviço Web de previsão e um serviço da Web de treinamento, você verá dois serviços Web separados listados. O serviço Web de previsão deve terminar com "[predictive exp.]".
> 
> 

1. Entre no portal [Serviços Web do Azure Machine Learning](https://services.azureml.net/).
2. Clique em **Serviços Web** ou **Classic Web Services (Serviços Web clássicos)**.
3. Selecione o Serviço Web de previsão.
4. Verifique se o novo ponto de extremidade foi adicionado ao serviço Web.

### <a name="check-that-your-workspace-is-in-the-same-region-as-the-web-service"></a>Verifique se o seu espaço de trabalho está na mesma região que o serviço Web
1. Entre no [Machine Learning Studio](https://studio.azureml.net/).
2. Na parte superior, clique na lista suspensa dos seus espaços de trabalho.

   ![IU da região do aprendizado de máquina.][image4]

3. Verifique a região em que está seu espaço de trabalho.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/check-workspace-region.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
