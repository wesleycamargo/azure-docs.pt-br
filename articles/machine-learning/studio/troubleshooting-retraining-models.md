---
Título: Solução de problemas ao treinar novamente um serviço Web clássico do Machine Learning Studio – titleSuffix: Descrição do Azure Machine Learning Studio: Identifique e corrija os problemas comuns encontrados quando você está readaptando o modelo para um serviço Web do Azure Machine Learning Studio.
serviços: machine-learning ms.service: machine-learning ms.component: studio ms.topic: artigo

autor: ericlicoding ms.author: amlstudiodocs ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT ms.date: 11/01/2017
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-studio-classic-web-service"></a>Solucionando problemas do novo treinamento de um serviço da web clássico do Azure Machine Learning Studio
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

1. Chame o serviço Web de treinamento:  a chamada é para o BES (Serviço de Execução em Lote), não o RRS (Serviço de Resposta à Solicitação). Você pode usar o Código c# de exemplo na página de ajuda da API para fazer a chamada. 
2. Encontre os valores de *BaseLocation*, *RelativeLocation* e *SasBlobToken*: Esses valores são retornados na saída da chamada para o serviço Web de treinamento. 
   ![exibindo a saída do exemplo de readaptação e os valores BaseLocation, RelativeLocation e SasBlobToken.][image6]
3. Atualize o ponto de extremidade do serviço Web de pontuação adicionado com o novo modelo treinado: usando o código de exemplo fornecido em Treinar novamente os modelos de machine learning de forma programática, atualize o novo ponto de extremidade adicionado ao modelo de pontuação com o modelo recém-treinado do Serviço Web de Treinamento.

## <a name="common-obstacles"></a>Obstáculos comuns
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Verifique se você tem a URL correta do PATCH
A URL do PATCH que você está usando deve ser a associada ao novo ponto de extremidade de pontuação adicionado ao serviço Web de pontuação. Há várias maneiras de obter a URL do PATCH:

**Opção 1: Programaticamente**

Para obter a URL correta do PATCH:

1. Execute o código de exemplo [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .
2. Na saída do AddEndpoint, encontre o valor *HelpLocation* e copie a URL.
   
   ![HelpLocation na saída do exemplo addEndpoint.][image2]
3. Cole a URL em um navegador para navegar até uma página que fornece links de ajuda para o serviço Web.
4. Clique no link **Atualizar Recurso** para abrir a página de ajuda do patch.

**Opção 2: usar o portal de serviços Web do Azure Machine Learning**

1. Entre no portal [Serviços Web do Azure Machine Learning](https://services.azureml.net/).
2. Clique em **Serviços Web** ou **Serviços Web Clássicos** na parte superior.
4. Clique no serviço Web de pontuação com o qual você está trabalhando (se você não modificar o nome padrão do serviço Web, ele terminará em "[Pontuação Exp.]").
5. Clique em **+NOVO**.
6. Depois do ponto de extremidade ser adicionado, clique no nome dele.
7. No URL do **Patch**, clique em **Ajuda da API** para abrir a página de ajuda de aplicação de patch.

> [!NOTE]
> Se você tiver adicionado o ponto de extremidade no serviço Web de treinamento e não no serviço Web preditivo, você receberá o seguinte erro ao clicar no link **Recurso de Atualização**: "Não há suporte para esse recurso ou ele não está disponível neste contexto. Este serviço Web não tem recursos atualizáveis. Pedimos desculpas pelo inconveniente e estamos trabalhando para melhorar esse fluxo de trabalho.”
> 
> 

A página de ajuda do PATCH contém a URL do PATCH que você deve usar e fornece o código de exemplo que você pode usar para chamar.

![URL de patch.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Verifique se você está atualizando o ponto de extremidade correto de pontuação
* Não corrija o serviço Web de treinamento: a operação de patch precisa ser executada no serviço Web de pontuação.
* Não corrija o ponto de extremidade padrão no serviço Web: a operação de patch precisa ser executada no novo ponto de extremidade de serviço Web de pontuação que você adicionou.

Você pode verificar em qual serviço Web está o ponto de extremidade acessando o portal de Serviços Web. 

> [!NOTE]
> Certifique-se de que você está adicionando o ponto de extremidade ao serviço Web de previsão e não ao serviço da Web de treinamento. Se você tiver implantado corretamente um serviço Web de previsão e um serviço da Web de treinamento, você verá dois serviços Web separados listados. O serviço Web de previsão deve terminar com "[predictive exp.]".
> 
> 

1. Entre no portal [Serviços Web do Azure Machine Learning](https://services.azureml.net/).
2. Clique em **Serviços Web** ou **Classic Web Services (Serviços Web clássicos)**.
3. Selecione o Serviço Web de previsão.
4. Verifique se o novo ponto de extremidade foi adicionado ao serviço Web.

### <a name="check-that-your-workspace-is-in-the-same-region-as-the-web-service"></a>Verifique se o seu workspace está na mesma região que o serviço Web
1. Entre no [Machine Learning Studio](https://studio.azureml.net/).
2. Na parte superior, clique na lista suspensa dos seus workspaces.

   ![IU da região do aprendizado de máquina.][image4]

3. Verifique a região em que está seu workspace.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/check-workspace-region.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
