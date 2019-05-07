---
title: 'Tutorial: Implantar um modelo de machine learning com a interface visual'
titleSuffix: Azure Machine Learning service
description: Saiba como criar uma solução de análise preditiva na interface visual do Serviço do Azure Machine Learning. Treine, pontue e implante um modelo de machine learning usando módulos do tipo "arrastar e soltar". Este tutorial é a segunda parte de uma série de duas partes sobre a previsão de preços de automóveis usando a regressão linear.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 5f29e3820416686b42167fa278c4b7d0f9a58f1f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190911"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Tutorial: Implantar um modelo de machine learning com a interface visual

Neste tutorial, você analisará de maneira aprofundada o desenvolvimento de uma solução preditiva na interface visual do Serviço do Azure Machine Learning. Este tutorial é **parte dois de uma série de tutoriais de duas partes**. Na [primeira parte do tutorial](ui-tutorial-automobile-price-train-score.md), você treinou, pontuou e avaliou um modelo para prever preços de carros. Nesta parte do tutorial, você:

> [!div class="checklist"]
> * Preparará o modelo para implantação
> * Implantar um serviço Web
> * Testará um serviço Web
> * Gerenciará um serviço Web
> * Consumir o serviço Web

## <a name="prerequisites"></a>Pré-requisitos

Conclusão da [primeira parte do tutorial](ui-tutorial-automobile-price-train-score.md).

## <a name="prepare-for-deployment"></a>Preparar para a implantação

Para que outras pessoas possam usar o modelo preditivo desenvolvido neste tutorial, implante-o como um serviço Web do Azure.

Até agora, você fez experiências de treinamento do modelo. Agora é hora de gerar novas previsões com base na entrada do usuário.

A preparação para a implantação é um processo de duas etapas:  

1. Converter o *teste de treinamento* criado em um *teste preditivo*
1. Implantar o teste preditivo como um serviço Web

O ideal é fazer uma cópia do teste primeiro selecionando **Salvar como** na parte inferior da tela do teste.

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter o teste de treinamento em um teste preditivo

Para preparar esse modelo para implantação, converta esse teste de treinamento em um teste preditivo. Isso normalmente envolve três etapas:

1. Salvar o modelo treinados e substituir os módulos de treinamento
1. Cortar o teste para remover os módulos necessários somente para treinamento
1. Definir o local em que o serviço Web aceitará os dados de entrada e o local em que ele gerará a saída

Você pode executar essas etapas manualmente ou selecionar **Configurar Serviço Web** na parte inferior da tela do teste para fazer isso automaticamente.

![GIF animado mostrando a conversão automática de um teste de treinamento em um teste preditivo](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

Quando você seleciona **Configurar Serviço Web**, várias coisas acontecem:

* O modelo treinado é convertido em um único módulo **Modelo Treinado**. Ele é armazenado na paleta de módulos à esquerda da tela do teste. Você pode encontrá-lo em **Modelos Treinados**.
* Os módulos que foram usados para o treinamento são removidos; especificamente:
  * Treinar Modelo
  * Dividir Dados
  * Avaliar Modelo
* O modelo treinado salvo é adicionado de volta ao teste
* Os módulos **Entrada de serviço Web** e **Saída de serviço Web** são adicionados. Esses módulos identificam o local em que os dados do usuário serão inseridos no modelo e o local em que os dados são retornados.

Você pode ver que o teste é salvo em duas partes nas novas guias na parte superior da tela do teste. O teste de treinamento original está na guia **Teste de treinamento**, e o teste de previsão recém-criado está em **Teste de previsão**. O teste preditivo é o que será implantado como um serviço Web.

Agora, o teste deverá ser semelhante ao seguinte:  

![Captura de tela mostrando a configuração esperada do teste após sua preparação para implantação](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

Execute o teste uma última vez (selecione **Executar**). Escolha o destino de computação no qual você deseja executar o teste na caixa de diálogo pop-up. Para verificar se o modelo ainda está funcionando, selecione a saída do módulo Pontuar Modelo e selecione **Exibir Resultados**. Você pode ver que os dados originais são exibidos, juntamente com o preço previsto ("Rótulos Pontuados").

## <a name="deploy-the-web-service"></a>Implantar o serviço Web

Para implantar um novo serviço Web derivado do teste:

1. Selecione **Implantar Serviço Web** abaixo da tela.
1. Selecione o **Destino de Computação** no qual deseja executar o serviço Web.

    Atualmente, a interface visual só dá suporte à implantação em destinos de computação do AKS (Serviço de Kubernetes do Azure). Escolha um dos destinos de computação do AKS disponíveis em seu workspace do serviço do Machine Learning ou configure um novo ambiente do AKS usando as etapas da caixa de diálogo exibida.

    ![Captura de tela mostrando uma configuração possível para um novo destino de computação](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Selecione **Implantar Serviço Web**. Você verá a notificação a seguir quando a implantação for concluída. A implantação poderá levar alguns minutos.

    ![Captura de tela mostrando a mensagem de confirmação para uma implantação bem-sucedida.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Testar o serviço Web

Os dados de entrada do usuário são inseridos no modelo implantado por meio do módulo **Entrada do serviço Web**. A entrada é então pontuada no módulo **Pontuar Modelo**. Da forma como você configurou o teste preditivo, o modelo espera os dados no mesmo formato do conjunto de dados original de preços de automóveis. Por fim, os resultados são retornados ao usuário por meio do módulo **Saída do serviço Web**.

Você pode testar um serviço Web na guia do serviço Web na interface visual.

1. Acesse a seção do serviço Web. Você verá o serviço Web implantado com o nome **Tutorial – Prever o Preço de Automóveis [Teste Preditivo]**.

     ![Captura de tela mostrando a guia do serviço Web com o serviço Web recém-criado realçado](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Selecione o nome do serviço Web para exibir detalhes adicionais.

     ![Captura de tela mostrando os detalhes adicionais disponíveis na exibição do serviço Web](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. Selecione **Testar**.

    ![Captura de tela mostrando a página de teste do serviço Web](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. Insira os dados de teste ou use os dados de exemplo preenchidos automaticamente e selecione **Testar** na parte inferior. A solicitação de teste é enviada para o serviço Web e os resultados são mostrados na página.

## <a name="manage-the-web-service"></a>Gerenciar o serviço Web

Depois de implantar o serviço Web, será possível gerenciá-lo na guia **Serviços Web** da interface visual.

Você pode excluir um serviço Web selecionando **Excluir** na página de detalhes do serviço Web.

   ![Captura de tela mostrando a localização do botão Excluir serviço Web na parte inferior da janela](./media/ui-tutorial-automobile-price-deploy/web-service-delete.png)

## <a name="consume-the-web-service"></a>Consumir o serviço Web

Nas etapas anteriores deste tutorial, você implantou um modelo de previsão de automóveis como um serviço Web do Azure. Agora os usuários podem enviar dados para ele e receber os resultados por meio da API REST.

**Solicitação/resposta** -o usuário envia uma ou mais linhas de dados de automóveis para o serviço usando um protocolo HTTP. O serviço responde com um ou mais conjuntos de resultados.

Encontre as chamadas REST de exemplo na guia **Consumir** da página de detalhes do serviço Web.

   ![Captura de tela mostrando uma chamada REST de exemplo que os usuários podem encontrar na guia Consumir](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Navegue até a guia **Documentação da API** para encontrar mais detalhes sobre a API.

  ![Captura de tela mostrando mais detalhes da API que os usuários podem encontrar na guia Documentação da API](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments-in-azure-machine-learning-service-workspace"></a>Gerenciar modelos e implantações no workspace do Serviço do Azure Machine Learning

Os modelos e as implantações de serviço Web criados na interface visual podem ser gerenciados no workspace do Serviço do Azure Machine Learning.

1. Abra seu workspace na [portal do Azure](https://portal.azure.com/).  

1. No workspace, selecione **Modelos**. Em seguida, selecione o teste que você criou.

    ![Captura de tela mostrando como navegar para os testes no portal do Azure](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    Nessa página, você verá mais detalhes sobre o modelo.

    ![Captura de tela mostrando uma visão geral das estatísticas do teste no portal do Azure](./media/ui-tutorial-automobile-price-deploy/model-details.png)

1. Selecione **Implantações**, e serão listados todos os serviços Web que usam o modelo. Selecione o nome do serviço Web, e ele acessará a página de detalhes do serviço Web. Nessa página, você pode obter informações mais detalhadas do serviço Web.

    ![Captura de tela do relatório de execução detalhado](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu as principais etapas na criação, na implantação e no consumo de um modelo de machine learning na interface visual. Para saber mais sobre como você pode usar a interface visual para resolver outros tipos de problemas, confira os testes de exemplo.

> [!div class="nextstepaction"]
> [Amostra de classificação de risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
