<properties
	pageTitle="Análise de sentimento usando o Stream Analytics do Azure e o Aprendizado de Máquina do Azure | Microsoft Azure"
	description="Como usar uma função definida pelo usuário e o Aprendizado de Máquina em um trabalho do Stream Analytics"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>


<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="08/08/2016" 
	ms.author="jeffstok"
/>

# Análise de sentimento usando o Stream Analytics do Azure e o Aprendizado de Máquina do Azure #

Este artigo é projetado para ajudar você a configurar rapidamente um trabalho simples do Stream Analytics do Azure com a integração do Aprendizado de Máquina do Azure. Usaremos um modelo de análise de sentimento do Aprendizado de Máquina da Galeria do Cortana Intelligence para analisar dados de texto de streaming e determinar a pontuação de sentimento em tempo real. As informações neste artigo podem ajudar você a compreender cenários, como a análise de sentimento em tempo real sobre o fluxo de dados do Twitter, analisar os registros de chats de clientes com a equipe de suporte e avaliar comentários em fóruns, blogs e vídeos, além de muitos outros cenários de pontuação preditiva em tempo real.

Este artigo oferece um arquivo CSV com texto como entrada no Armazenamento de Blobs do Azure, mostrado na imagem a seguir. O trabalho aplica o modelo de análise de sentimento como uma UDF (função definida pelo usuário) nos dados de texto de exemplo do armazenamento de blob. O resultado final é colocado no mesmo armazenamento de blob em outro arquivo CSV.

![Aprendizado de Máquina do Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)

A imagem a seguir demonstra essa configuração. Para um cenário mais realista, você pode substituir o Armazenamento de Blobs pela transmissão de dados do Twitter de uma entrada de Hubs de Eventos do Azure. Além disso, você pode compilar uma visualização em tempo real do [Microsoft Power BI](https://powerbi.microsoft.com/) do sentimento agregado.

![Aprendizado de Máquina do Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)

## Pré-requisitos

Os pré-requisitos para concluir as tarefas que são demonstradas neste artigo são da seguinte maneira:

-	Uma assinatura ativa do Azure.
-	Um arquivo CSV com alguns dados. Você pode baixar o arquivo mostrado na Figura 1 do [GitHub](https://github.com/jeffstokes72/azure-stream-analytics-repository/blob/master/sampleinputs.csv) ou pode criar seu próprio arquivo. Neste artigo, presumimos que você use aquele fornecido para download no GitHub.

Em um nível alto, para concluir as tarefas demonstradas neste artigo, você fará o seguinte:

1.	Carregue o arquivo CSV de entrada para o Armazenamento de Blobs do Azure.
2.	Adicione um modelo de Análise de Sentimento da Galeria do Cortana Intelligence ao seu espaço de trabalho do Aprendizado de Máquina do Azure.
3.	Implante este modelo como um serviço Web no espaço de trabalho do Aprendizado de Máquina.
4.	Crie um trabalho do Stream Analytics que chama o serviço Web como uma função para determinar o sentimento da entrada de texto.
5.	Inicie o trabalho do Stream Analytics e observe a saída.

## Carregue o arquivo CSV de entrada para o Armazenamento de Blobs

Nesta etapa, você pode usar qualquer arquivo CSV, como já foi especificado, como disponível para download no GitHub. Você pode usar o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/) ou o Visual Studio para carregar o arquivo, ou pode usar um código personalizado. Podemos usar exemplos com base no Visual Studio.

1.	No Visual Studio, clique em **Azure** > **Armazenamento** > **Anexar Armazenamento Externo**. Insira um **Nome da Conta** e uma **Chave de Conta**.

    ![Aprendizado de Máquina do Stream Analytics, Gerenciador de Servidores](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)

2.	Expanda o armazenamento conectado na etapa 1, clique em **Criar contêiner de blob** e, em seguida, insira um nome lógico. Depois de criar o contêiner, abra-o para exibir seu conteúdo. (Ele estará vazio neste momento).

    ![Aprendizado de Máquina do Stream Analytics, criar blob](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)

3.	Para carregar o arquivo CSV, clique em **Carregar blob** e, em seguida, clique no **arquivo do disco local**.

## Adicione o modelo de análise de sentimento da Galeria do Cortana Intelligence

1.	Baixe o [modelo preditivo de análise de sentimento](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) da Galeria do Cortana Intelligence.
2.	No Estúdio de Aprendizado de Máquina, clique em **Abrir no Studio**.

    ![Aprendizado de Máquina do Stream Analytics, abrir o Estúdio de Aprendizado de Máquina](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)

3.	Entre para acessar o espaço de trabalho. Selecione o local mais adequado à sua localização.
4.	Clique em **Executar** na parte inferior da página.
5.	Depois que o processo tiver sido executado com êxito, clique em **Implantar Serviço Web**.
6.	O modelo de análise de sentimento está pronto para uso. Para validar, clique no botão **Testar** e forneça o texto de entrada, como "Eu adoro a Microsoft". O teste deve retornar um resultado semelhante ao seguinte:

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`

![Aprendizado de Máquina do Stream Analytics, dados de análise](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)

Na coluna **Aplicativos**, clique no link da pasta de trabalho do **Excel 2010 ou anterior** para obter a chave de API e a URL necessária para configurar o trabalho do Stream Analytics posteriormente. (Esta etapa só é necessária para usar um modelo de Aprendizado de Máquina do espaço de trabalho de outra conta do Azure. Este artigo presume que é pertinente tratar desse cenário.)

Observe a URL do serviço Web e a tecla de acesso do arquivo Excel baixado, como mostrado abaixo:

![Aprendizado de Máquina do Stream Analytics, visão rápida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)

## Criar um trabalho do Stream Analytics que usa o modelo de Aprendizado de Máquina

1.	Vá para o [Portal do Azure](https://manage.windowsazure.com).
2.	Clique em **Novo** > **Serviços de Dados** > **Stream Analytics** > **Criação Rápida**. Insira um nome para o trabalho em **Nome do trabalho**, insira a região apropriada para o trabalho em **Região** e, em seguida, selecione a conta em **Conta de armazenamento de monitoramento regional**.
3.	Depois que o trabalho for criado, na guia **Entradas**, clique em **Adicionar uma entrada**.

    ![Aprendizado de Máquina do Stream Analytics, adicionar entrada do Aprendizado de Máquina](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)

4.	Na primeira página do assistente **Adicionar entrada**, clique em **Fluxo de dados** e, em seguida, clique em **Avançar**. Na página seguinte, clique em **Armazenamento de Blobs** como a entrada e, em seguida, clique em **Próximo**.
5.	Na página **Configurações de Armazenamento de Blobs** do Assistente, indique o nome do contêiner de blob da conta de armazenamento definida anteriormente ao carregar os dados. Clique em **Próximo**. Para o **Formato de Serialização de Evento**, clique em **CSV**. Aceite os valores padrão para o restante da página **Definições de serialização**. Clique em **OK**.
6.	Na guia **Saídas**, clique em **Adicionar uma saída**.

    ![Aprendizado de Máquina do Stream Analytics, adicionar saída](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)

7.	Clique em **Armazenamento de Blobs** e, em seguida, insira os mesmos parâmetros, exceto para o contêiner. O valor para a **Entrada** foi configurado para ler o contêiner chamado "test" no qual o arquivo **CSV** foi carregado. Para a **Saída**, digite “testoutput”. Os nomes de contêiner devem ser diferentes. Verifique se esse contêiner existe.
8.	Clique em **Avançar** para definir as **Configurações de serialização** de saída. Assim como acontece com a **Entrada**, clique em **CSV** e, em seguida, clique no botão **OK**.
9.	Na guia **Funções**, clique em **Adicionar uma Função de Aprendizado de Máquina**.

    ![Aprendizado de Máquina do Stream Analytics, adicionar função do Aprendizado de Máquina](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)

10.	Na página **Configurações do serviço Web Machine Learning** localize o espaço de trabalho do Aprendizado de Máquina, o serviço Web e o ponto de extremidade padrão. Para este artigo, aplique as configurações manualmente para se familiarizar com a configuração de um serviço Web para qualquer espaço de trabalho, desde que você saiba a URL e tenha a chave de API. Insira a **URL** e a **chave de API** do ponto de extremidade. Clique em **OK**.

    ![Aprendizado de Máquina do Stream Analytics, serviço Web Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)

11.	Na guia **Consulta**, modifique a consulta da seguinte maneira:

 ```
 	WITH subquery AS (  
 		SELECT text, sentiment(text) as result from input  
  	)  
 
 	Select text, result.[Scored Labels]  
 	Into output  
 	From subquery  
 ```    
12.	Clique em **Salvar** para salvar a consulta.

## Iniciar o trabalho do Stream Analytics e observar a saída

1.	Clique em **Iniciar** na parte inferior da página de trabalho.
2.	No **Diálogo Iniciar Consulta**, clique em **Hora Personalizada** e, então, selecione uma hora anterior ao momento que você carregou o CSV para o Armazenamento de Blobs. Clique em **OK**.

    ![Aprendizado de Máquina do Stream Analytics, hora personalizada](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)

3.	Acesse o Armazenamento de Blobs usando a ferramenta utilizada para carregar o arquivo CSV, por exemplo, o Visual Studio.
4.	Depois de alguns minutos do início do trabalho, o contêiner de saída é criado e um arquivo CSV é carregado nele.
5.	Abra o arquivo no editor padrão CSV. Deve ser exibido algo semelhante ao seguinte:

    ![Aprendizado de Máquina do Stream Analytics, exibição de CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)

## Conclusão

Este artigo demonstra como criar um trabalho do Stream Analytics que lê dados de transmissão de texto e aplica a análise de sentimento nos dados em tempo real. Você pode fazer tudo isso sem a necessidade de se preocupar com a complexidade de criar um modelo de análise de sentimento. Essa é uma das vantagens do Cortana Intelligence Suite.

Você também pode exibir as métricas relacionadas à função de Aprendizado de Máquina do Azure. Para fazer isso, clique na guia **Monitorar**. Três métricas relativas à função são exibidas.

- **Solicitações de Função** indica o número de solicitações enviadas para o serviço Web Machine Learning.
- **Eventos de função** indica o número de eventos na solicitação. Por padrão, cada solicitação para um serviço Web Machine Learning contém até 1.000 eventos.
- **Falhas nas Solicitações de Função** indica o número de solicitações com falha no serviço Web Machine Learning.

    ![Aprendizado de Máquina do Stream Analytics, exibição de monitor do Aprendizado de Máquina](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)

<!---HONumber=AcomDC_0810_2016-->