---
title: "Integração do Azure Stream Analytics e do Machine Learning | Microsoft Docs"
description: "Como usar uma função definida pelo usuário e o Aprendizado de Máquina em um trabalho do Stream Analytics"
keywords: 
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: cfced01f-ccaa-4bc6-81e2-c03d1470a7a2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: d1ffb9aba0eb1e17d1efd913f536f6f3997fccbb
ms.openlocfilehash: 7b635b1810536f5b3eb1371d687e9c355e604e41
ms.lasthandoff: 02/14/2017

---

# <a name="sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Análise de sentimento usando o Stream Analytics do Azure e o Aprendizado de Máquina do Azure
Este artigo é projetado para ajudar você a configurar rapidamente um trabalho simples do Stream Analytics do Azure com a integração do Aprendizado de Máquina do Azure. Usaremos um modelo de análise de sentimento do Aprendizado de Máquina da Galeria do Cortana Intelligence para analisar dados de texto de streaming e determinar a pontuação de sentimento em tempo real. As informações neste artigo podem ajudar você a compreender cenários, como a análise de sentimento em tempo real sobre o fluxo de dados do Twitter, analisar os registros de chats de clientes com a equipe de suporte e avaliar comentários em fóruns, blogs e vídeos, além de muitos outros cenários de pontuação preditiva em tempo real.

Este artigo oferece um arquivo CSV com texto como entrada no Armazenamento de Blobs do Azure, mostrado na imagem a seguir. O trabalho aplica o modelo de análise de sentimento como uma UDF (função definida pelo usuário) nos dados de texto de exemplo do armazenamento de blob. O resultado final é colocado no mesmo armazenamento de blob em outro arquivo CSV. 

![Aprendizado de Máquina do Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

A imagem a seguir demonstra essa configuração. Para um cenário mais realista, você pode substituir o Armazenamento de Blobs pela transmissão de dados do Twitter de uma entrada de Hubs de Eventos do Azure. Além disso, você pode compilar uma visualização em tempo real do [Microsoft Power BI](https://powerbi.microsoft.com/) do sentimento agregado.    

![Aprendizado de Máquina do Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Pré-requisitos
Os pré-requisitos para concluir as tarefas que são demonstradas neste artigo são da seguinte maneira:

* Uma assinatura ativa do Azure.
* Um arquivo CSV com alguns dados. Você pode baixar o arquivo mostrado na Figura 1 do [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/sampleinput.csv) ou pode criar seu próprio arquivo. Neste artigo, presumimos que você use aquele fornecido para download no GitHub.

Em um nível alto, para concluir as tarefas demonstradas neste artigo, você fará o seguinte:

1. Carregue o arquivo CSV de entrada para o Armazenamento de Blobs do Azure.
2. Adicione um modelo de Análise de Sentimento da Galeria do Cortana Intelligence ao seu espaço de trabalho do Aprendizado de Máquina do Azure.
3. Implante este modelo como um serviço Web no espaço de trabalho do Aprendizado de Máquina.
4. Crie um trabalho do Stream Analytics que chama o serviço Web como uma função para determinar o sentimento da entrada de texto.
5. Inicie o trabalho do Stream Analytics e observe a saída.

## <a name="create-a-storage-blob-and-upload-the-csv-input-file"></a>Como criar um armazenamento de blobs e carregar o arquivo de entrada CSV
Nesta etapa, você pode usar qualquer arquivo CSV, como já foi especificado, como disponível para download no GitHub. Carregar o arquivo CSV é simples por ser uma opção incluída na criação de um armazenamento de blobs.

Para nosso tutorial, crie uma nova conta de armazenamento clicando em **Novo**, em seguida, procure a "conta de armazenamento" e selecione o ícone resultante para a conta de armazenamento e forneça os detalhes para criar a conta. Forneça um **Nome** (no meu exemplo usei: azuresamldemosa), crie ou use um **grupo de recursos** existente e especifique um **local** (sobre o local, se possível, é importante que todos os recursos criados nesta demonstração usem o mesmo local).

![criar conta de armazenamento](./media/stream-analytics-machine-learning-integration-tutorial/create-sa.png)

Depois de concluir essa etapa, clique no serviço Blob e crie um contêiner de blobs.

![criar contêiner de blobs](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

Em seguida, forneça um **Nome** para o contêiner (no meu exemplo usei: azuresamldemoblob) e confira se o **Tipo de acesso** está definido como "blob".

![criar tipo de acesso de blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

Agora podemos popular o blob com nossos dados. Selecione **Arquivos** e, em seguida, selecione o arquivo na unidade local que você baixou do GitHub. Selecionei o blob de blocos e 4 MB como um tamanho que deve ser o suficiente para esta demonstração. Em seguida, selecione **Carregar** e o portal criará um blob com exemplo de texto para você.

![criar arquivo de carregamento de blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa4.png)

Agora que os dados de exemplo estão em um blob, é hora de habilitar o modelo de análise de sentimento na galeria do Cortana Intelligence.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Adicione o modelo de análise de sentimento da Galeria do Cortana Intelligence
1. Baixe o [modelo preditivo de análise de sentimento](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) da Galeria do Cortana Intelligence.  
2. No Machine Learning Studio, clique em **Abrir no Studio**.  
   
   ![Aprendizado de Máquina do Stream Analytics, abrir o Estúdio de Aprendizado de Máquina](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Entre para acessar o espaço de trabalho. Selecione o local mais adequado à sua localização.
4. Clique em **Executar** na parte inferior da página.  
5. Após a execução com êxito do processo, clique em **Implantar Serviço Web**.
6. O modelo de análise de sentimento está pronto para uso. Para validar, clique no botão **Testar** e forneça o texto de entrada, como "Eu adoro a Microsoft". O teste deve retornar um resultado semelhante ao seguinte:

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`  

![Aprendizado de Máquina do Stream Analytics, dados de análise](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)  

Na coluna **Aplicativos**, clique no link da pasta de trabalho do **Excel 2010 ou anterior** para obter a chave de API e a URL necessárias para configurar o trabalho do Stream Analytics posteriormente. (Esta etapa só é necessária para usar um modelo de Aprendizado de Máquina do espaço de trabalho de outra conta do Azure. Este artigo presume que é pertinente tratar desse cenário.)  

Observe a URL do serviço Web e a tecla de acesso do arquivo Excel baixado, como mostrado abaixo:  

![Aprendizado de Máquina do Stream Analytics, visão rápida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Criar um trabalho do Stream Analytics que usa o modelo de Aprendizado de Máquina
1. Vá para o [Portal do Azure](https://portal.azure.com).  
2. Clique em **Novo** > **Intelligence + analytics** > **Stream Analytics**. Insira um nome para o trabalho em **Nome do trabalho**, especifique um grupo de recursos existente ou crie um novo conforme necessário e indique o local apropriado para o trabalho no campo **Local**.    
   
   ![criar trabalho](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   
3. Depois da criação do trabalho, na guia **Entradas**, clique em **Adicionar uma Entrada**.  
   
   ![Aprendizado de Máquina do Stream Analytics, adicionar entrada do Aprendizado de Máquina](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input.png)  

4. Selecione **Adicionar** e, em seguida, especifique um **Alias de entrada**, selecione **Fluxo de dados**, **Armazenamento de Blobs** como entrada e, em seguida, selecione **Próximo**.  
5. Na página **Configurações de Armazenamento de Blobs** do Assistente, indique o nome do contêiner de blob da conta de armazenamento definida anteriormente ao carregar os dados. Clique em **Próximo**. Para **Formato de serialização de evento**, clique em **CSV**. Aceite os valores padrão para o restante da página **Definições de serialização** . Clique em **OK**.  
   
   ![adicionar o contêiner de blobs de entrada](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input-blob.png)

6. Na guia **Saídas**, selecione **Adicionar uma saída**.  
   
   ![Aprendizado de Máquina do Stream Analytics, adicionar saída](./media/stream-analytics-machine-learning-integration-tutorial/create-output.png)  

7. Clique em **Armazenamento de Blobs**e, em seguida, insira os mesmos parâmetros, exceto para o contêiner. O valor para a **Entrada** foi configurado para ler o contêiner chamado "test", no qual o arquivo **CSV** foi carregado. Para a **Saída**, digite “testoutput”.
8. Valide que as **configurações de serialização** de saída estão definidas como **CSV** e, em seguida, selecione o botão **OK**.
   
   ![Aprendizado de Máquina do Stream Analytics, adicionar saída](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

9. Na guia **Funções**, clique em **Adicionar uma função de Machine Learning**.  
   
   ![Aprendizado de Máquina do Stream Analytics, adicionar função do Aprendizado de Máquina](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  

10. Na página **Configurações do serviço Web Machine Learning** localize o espaço de trabalho do Aprendizado de Máquina, o serviço Web e o ponto de extremidade padrão. Para este artigo, aplique as configurações manualmente para se familiarizar com a configuração de um serviço Web para qualquer espaço de trabalho, desde que você saiba a URL e tenha a chave de API. Insira a **URL** e a **chave de API** do ponto de extremidade. Clique em **OK**. Observe que a **Alias da função** é "sentimento".  
    
    ![Aprendizado de Máquina do Stream Analytics, serviço Web Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/add-function-endpoints.png)    

11. Na guia **Consulta** , modifique a consulta da seguinte maneira:    
    
    ```
    WITH sentiment AS (  
      SELECT text, sentiment(text) as result from datainput  
    )  
    
    Select text, result.[Scored Labels]  
    Into testoutput  
    From sentiment  
    ```    

12. Clique em **Salvar** para salvar a consulta.

## <a name="start-the-stream-analytics-job-and-observe-the-output"></a>Iniciar o trabalho do Stream Analytics e observar a saída
1. Clique em **Iniciar** na parte superior da página do trabalho.
2. Na **Caixa de diálogo Iniciar Consulta**, clique em **Hora Personalizada** e, em seguida, selecione um dia anterior ao momento que você carregou o CSV para o armazenamento de Blobs. Clique em **OK**.  
3. Acesse o Armazenamento de Blobs usando a ferramenta utilizada para carregar o arquivo CSV, por exemplo, o Visual Studio.
4. Depois de alguns minutos do início do trabalho, o contêiner de saída é criado e um arquivo CSV é carregado nele.  
5. Abra o arquivo no editor padrão CSV. Deve ser exibido algo semelhante ao seguinte:  
   
   ![Aprendizado de Máquina do Stream Analytics, exibição de CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

## <a name="conclusion"></a>Conclusão
Este artigo demonstra como criar um trabalho do Stream Analytics que lê dados de transmissão de texto e aplica a análise de sentimento nos dados em tempo real. Você pode fazer tudo isso sem a necessidade de se preocupar com a complexidade de criar um modelo de análise de sentimento. Essa é uma das vantagens do Cortana Intelligence Suite.

Você também pode exibir as métricas relacionadas à função de Aprendizado de Máquina do Azure. Para fazer isso, selecione a guia **Monitor**. Três métricas relativas à função são exibidas.  

* **Solicitações de Função** indica o número de solicitações enviadas para o serviço Web Machine Learning.  
* **Eventos de função** indica o número de eventos na solicitação. Por padrão, cada solicitação para um serviço Web Machine Learning contém até 1.000 eventos.  
  
    ![Aprendizado de Máquina do Stream Analytics, exibição de monitor do Aprendizado de Máquina](./media/stream-analytics-machine-learning-integration-tutorial/job-monitor.png)  


