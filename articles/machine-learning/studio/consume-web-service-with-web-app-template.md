---
title: "Consumir um serviço Web do Machine Learning usando um modelo de aplicativo Web | Microsoft Docs"
description: "Use um modelo de aplicativo Web no Azure Marketplace para consumir um serviço Web preditivo no Azure Machine Learning."
keywords: "serviço Web, operacionalização, API REST, aprendizado de máquina"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye;raymondl
ms.openlocfilehash: 93e75a2864d9aef266b16f8dd7dcc3411b24864b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="consume-an-azure-machine-learning-web-service-by-using-a-web-app-template"></a>Consumir um serviço Web de Azure Machine Learning usando um modelo de aplicativo Web

Você pode desenvolver um modelo de previsão e implantá-lo como um serviço web do Azure, usando:
- Azure Machine Learning Studio.
- Ferramentas como o R ou Python. 

Depois disso, você pode acessar o modelo operacionalizado usando uma API REST.

Há várias maneiras para consumir a API REST e acessar o serviço Web. Por exemplo, você pode gravar um aplicativo em C#, R ou Python usando o código de exemplo gerado quando implantou o serviço Web. (O código de exemplo está disponível no [portal de Serviços Web do Machine Learning](https://services.azureml.net/quickstart) ou no painel de serviços web no Machine Learning Studio.) Se preferir, é possível usar a pasta de trabalho de exemplo do Microsoft Excel criada para você ao mesmo tempo.

Mas a maneira mais rápida e fácil de acessar o serviço web é por meio dos modelos de aplicativos Web disponíveis no [Marketplace do Azure](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-machine-learning-web-app-templates"></a>Modelos de aplicativos Web do Azure Machine Learning
Os modelos de aplicativo Web disponíveis no Azure Marketplace podem compilar um aplicativo Web personalizado que conhece os dados de entrada do seu serviço Web e os resultados esperados. Basta fornecer ao aplicativo Web o acesso ao serviço e aos dados da Web para que o modelo faça o resto.

Dois modelos estão disponíveis:

* [Modelo do Aplicativo Web do Serviço de Solicitação-Resposta do Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Modelo do aplicativo Web do Serviço de Execução em Lote do Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Cada modelo cria um aplicativo ASP.NET de exemplo, usando o URI da API e a chave para o serviço Web. O modelo, em seguida, implanta o aplicativo como um site do Azure. 

O modelo RRS (Serviço de Solicitação-Resposta) cria um aplicativo Web que você pode usar para enviar uma única linha de dados para o serviço Web para obter um único resultado. O modelo BES (Serviço de Execução em Lote) cria um aplicativo Web que você pode usar para enviar várias linhas de dados para obter vários resultados.

Nenhuma codificação é necessária para usar esses modelos. Basta fornecer a chave de API e o URI, e o modelo criará o aplicativo para você.

Para obter a chave de API e o URI de solicitação de um serviço Web:

1. No [portal de serviços Web](https://services.azureml.net/quickstart), selecione **serviços Web** na parte superior. Ou para gerenciar um serviço Web Clássico, selecione **Serviços Web Clássicos**.
2. Selecione o serviço Web que você deseja acessar.
3. Em um serviço Web Clássico, selecione o ponto de extremidade que você deseja acessar.
4. Selecione **Consumir** na parte superior.
5. Copie a chave primária ou a secundária e salve-a.
6. Se estiver criando um modelo RRS, copie o URI **Solicitação-Resposta** e salve-o. Se estiver criando um modelo BES, copie o URI **Solicitações em Lote** e salve-o.


## <a name="how-to-use-the-request-response-service-template"></a>Como usar o modelo Serviço de Solicitação-Resposta
Siga estas etapas para usar o modelo de aplicativo Web RRS, conforme mostrado no diagrama a seguir.

![Processo para usar o modelo da Web RRS][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Novo**, pesquise e selecione **Aplicativo Web de Serviço de Solicitação-Resposta do Azure ML** e, em seguida, selecione **Criar**. 
3. No painel **Criar**:
   
   * Dê um nome exclusivo ao seu aplicativo Web. A URL do aplicativo Web será esse nome seguido de **.azurewebsites.net**. Um exemplo é **http://carprediction.azurewebsites.net**.
   * Selecione a assinatura do Azure e os serviços nos quais seu serviço Web está sendo executado.
   * Selecione **Criar**.
     
   ![Criar um aplicativo Web][image5]

4. Quando o Azure concluir a implantação do aplicativo Web, selecione a **URL** na página de configurações do aplicativo Web no Azure, ou insira a URL em um navegador da Web. Por exemplo, insira **http://carprediction.azurewebsites.net**.
5. Quando o aplicativo Web é executado pela primeira vez, ele solicita a **URL de Postagem da API** e a **Chave de API**. Insira os valores salvos anteriormente (URI de solicitação e chave de API, respectivamente). Selecione **Enviar**.
     
   ![Insira o URI de postagem e a chave de API][image6]

6. O aplicativo Web exibe sua página **Configuração de Aplicativo Web** com as configurações atuais do serviço Web. Aqui você pode alterar as configurações que o aplicativo Web usa.
   
   > [!NOTE]
   > Alterar as configurações aqui apenas as altera para este aplicativo Web. Ele não altera as configurações padrão do serviço Web. Por exemplo, se você alterar o texto na **Descrição** aqui, isso não alterará a descrição mostrada no painel de serviço Web no Machine Learning Studio.
   > 
   > 
   
    Quando terminar, selecione **Salvar Alterações** e selecione **Ir para a Página Inicial**.

7. Na home page, é possível inserir valores a serem enviados para o serviço Web. Selecione **Enviar** quando terminar e o resultado será retornado.

Para retornar à página **Configuração**, vá para a página **setting.aspx** do aplicativo Web. Por exemplo, vá para **http://carprediction.azurewebsites.net/setting.aspx**. Você será solicitado a digitar a chave de API novamente. É necessário para acessar a página e atualizar as configurações.

Você pode parar, reiniciar ou excluir o aplicativo Web no portal do Azure como qualquer outro aplicativo Web. Enquanto ele estiver em execução, você poderá navegar até o endereço web da página inicial e inserir novos valores.

## <a name="how-to-use-the-batch-execution-service-template"></a>Como usar o modelo Serviço de Execução em Lote
Você pode usar o modelo de aplicativo Web BES da mesma maneira que o modelo RSS. A diferença é que você pode usar o aplicativo Web criado para enviar várias linhas de dados e receber vários resultados.

Os valores de entrada de um serviço Web de execução em lotes podem ser provenientes do Armazenamento do Azure ou de um arquivo local. Os resultados são armazenados em um contêiner de armazenamento do Azure. Portanto, você precisa de um contêiner de armazenamento do Azure para armazenar os resultados retornados pelo aplicativo web. Você também precisa preparar os dados de entrada.

![Processo para usar o modelo da Web BES][image2]

1. Siga o mesmo procedimento para criar um aplicativo Web do BES para o modelo RRS. Mas neste caso, vá para [Modelo do Aplicativo Web do Serviço de Execução em Lote do Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) para abrir o modelo BES no Azure Marketplace. Selecione **Criar aplicativo Web**.

2. Para especificar onde você deseja que os resultados sejam armazenados, insira as informações do contêiner de destino na home page do aplicativo Web. Especifique também onde o aplicativo Web pode obter os valores de entrada, em um arquivo local ou em um contêiner de armazenamento do Azure.
   Selecione **Enviar**.
   
   ![Informações de armazenamento][image7]

O aplicativo Web exibe uma página com o status do trabalho. Quando o trabalho tiver concluído, você recebe o local dos resultados no armazenamento de Blob do Azure. Você também tem a opção de baixar os resultados em um arquivo local.

## <a name="for-more-information"></a>Para obter mais informações
Para saber mais sobre:

* Criando uma experiência de aprendizado de máquina com o Machine Learning Studio, veja [Criar seu primeiro teste no Azure Machine Learning Studio](create-experiment.md).
* Como implantar sua experiência de aprendizado de máquina como um serviço Web, veja [Implantar um serviço Web de Azure Machine Learning](publish-a-machine-learning-web-service.md).
* Outras maneiras de acessar o serviço Web, veja [Como consumir um serviço Web de Azure Machine Learning](consume-web-services.md).

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
