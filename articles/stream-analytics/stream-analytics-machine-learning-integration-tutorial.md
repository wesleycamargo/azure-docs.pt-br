<properties 
	pageTitle="Tutorial: Análise de sentimento usando o Azure Stream Analytics e o Aprendizado de Máquina do Azure | Microsoft Azure" 
	description="Como aproveitar o UDF e o aprendizado de máquina em trabalhos do stream analytics"
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
	ms.date="07/27/2016" 
	ms.author="jeffstok"
/>

# Tutorial: Executar análise de sentimento usando o Stream Analytics e o Aprendizado de Máquina #

Este tutorial é projetado para ajudá-lo a configurar rapidamente um trabalho de Stream Analytics simples com a integração do Aprendizado de Máquina. Vamos aproveitar um Modelo do Aprendizado de Máquina da Análise de Sentimento da Galeria do Cortana Intelligence para analisar dados de texto de streaming e determinar a pontuação de sentimento em tempo real. Este é um bom tutorial para entender cenários como análise de sentimento em tempo real do fluxo de dados do Twitter, análise de registro do chat do cliente com a equipe de suporte, comentários em fóruns/blogs/vídeos e muitos outros cenários preditivos com pontuação em tempo real.
  
Neste tutorial, um arquivo CSV com texto (como mostrado na Figura 1 abaixo) é fornecido como entrada no Armazenamento de Blobs do Azure. O trabalho aplicará o modelo de análise de sentimento como uma UDF (função definida pelo usuário) nos dados de texto de exemplo do armazenamento de blob. O resultado final será colocado no mesmo Armazenamento de Blobs do Azure em outro arquivo CSV. Um diagrama dessa configuração é fornecido na Figura 2 abaixo. Para um cenário mais realista, essa entrada do armazenamento de blob pode ser substituída pelo fluxo de dados do Twitter usando uma entrada de Hub de Eventos do Azure. Além disso, uma visualização em tempo real do sentimento total pode ser criada com o [Power BI](https://powerbi.microsoft.com/) As próximas iterações deste artigo incluirão essas extensões.

Figura 1:

![figura 1 tutorial do aprendizado de máquina do stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)

Figura 2:

![figura 2 tutorial do aprendizado de máquina do stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)

## Pré-requisitos

Os pré-requisitos para este artigo são os seguintes:

1.	Uma assinatura ativa do Azure
2.	Um arquivo CSV com alguns dados. O arquivo mostrado na Figura 2 [em GitHub](https://github.com/jeffstokes72/azure-stream-analytics-repository/blob/master/sampleinputs.csv) para download, ou você pode criar um. Este tutorial foi escrito presumindo o uso do arquivo disponível para download.

Em um alto nível, estas etapas serão executadas:

1.	Carregar o arquivo CSV de entrada no Armazenamento de Blobs
2.	Adicionar um modelo de Análise de Sentimento da Galeria do Cortana Intelligence ao seu espaço de trabalho do Aprendizado de Máquina
3.	Implantar esse modelo como um serviço Web no espaço de trabalho do Aprendizado de Máquina do Azure
4.	Criar um trabalho do Stream Analytics que chama o serviço Web como uma função para determinar o sentimento da entrada de texto
5.	Iniciar o trabalho do Stream Analytics e observar a saída


## Carregar o arquivo CSV de entrada para o Armazenamento de Blobs

Nesta etapa, você pode usar qualquer arquivo CSV, incluindo aquele especificado na introdução. O [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/) e o Visual Studio podem ser usados para carregar o arquivo, bem como o código personalizado. Para este tutorial, os exemplos fornecidos são para o Visual Studio.

1.	Expanda o Azure e clique com o botão direito do mouse em **Armazenamento**. Escolha **Anexar Armazenamento Externo** e forneça o **Nome da Conta** e a **Chave de Conta**.

    ![gerenciador de servidores tutorial do aprendizado de máquina do stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)

2.	Expanda o armazenamento que você acabou de anexar, escolha **Criar Contêiner de Blob** e forneça um nome lógico. Uma vez criado, clique duas vezes no contêiner para exibir seu conteúdo (que está vazio neste momento).

    ![criar blob tutorial do aprendizado de máquina do stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)

3.	Carregue o arquivo CSV clicando no ícone **Carregar Blob** e escolha **arquivo do disco local**.

## Adicione o modelo de análise de sentimento da Galeria do Cortana Intelligence

1.	Baixe o [modelo preditivo de análise de sentimento](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) na Galeria do Cortana Intelligence.
2.	Clique em **Abrir** no Estúdio:

    ![abrir estúdio de aprendizado de máquina tutorial do aprendizado de máquina do stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)

3.	Entre para ser levado ao espaço de trabalho. Escolha o local mais adequado à sua localização.
4.	Agora clique em **Executar** na parte inferior do Estúdio
5.	Depois que ele tiver sido executado com êxito, clique em **Implantar o Serviço Web**.
6.	O modelo de análise de sentimento está pronto para uso. Para validá-lo, clique no botão **testar** e insira o texto de entrada, como "Eu adoro a Microsoft", e o teste deve retornar um resultado semelhante ao mostrado abaixo:

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`

![dados de análise tutorial do aprendizado de máquina do stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)

Clique no link da pasta de trabalho do **Excel 2010 ou anterior** para obter a chave de API e a URL necessária para configurar mais tarde o trabalho do Stream Analytics. (Esta etapa só é necessária para aproveitar um modelo de aprendizado de máquina do espaço de trabalho de outra conta do Azure. Este tutorial presume que é pertinente tratar desse cenário.)

![tutorial stream analytics aprendizado de máquina experimento de análise](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-experiement.png)

Anote a URL do serviço Web e a tecla de acesso da planilha em Excel baixada, como mostrado abaixo:

![tutorial stream analytics aprendizado de máquina visão rápida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)

## Criar um trabalho de Stream Analytics que usa o modelo de aprendizado de máquina

1.	Navegue até o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2.	Clique em **Novo**, **Serviços de Dados**, **Stream Analytics** e **Criação Rápida**. Forneça o **Nome do Trabalho**, a **Região** adequada para o trabalho e escolha uma **Conta de armazenamento de monitoramento regional**.
3.	Depois que o trabalho for criado, navegue até a guia **Entradas** e clique em **Adicionar Entrada**.

    ![tutorial stream analytics aprendizado de máquina adicionar entrada de aprendizado de máquina](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)

4.	Na primeira página da janela do assistente **Adicionar Entrada**, selecione **Fluxo de dados** e clique em Avançar. Na segunda página, selecione **Armazenamento de Blobs** como entrada e clique em **Avançar**.
5.	Na página **Configurações de Armazenamento de Blobs** do Assistente, indique o nome do contêiner de blob da conta de armazenamento definida anteriormente quando os dados foram carregados. Clique em **Avançar**. Escolha **CSV** como **Formato de Serialização de Evento**. Aceite os padrões para o restante das **Definições de serialização**. Clique em **OK**.
6.	Navegue até a guia **Saídas** e clique em **Adicionar uma Saída**.

    ![tutorial stream analytics aprendizado de máquina adicionar saída](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)

7.	Escolha **Armazenamento de Blobs** e forneça os mesmos parâmetros, com exceção do contêiner. A **Entrada** foi configurada para ler o contêiner chamado "test" no qual o arquivo **CSV** foi carregado. Em **Saída**, coloque "testoutput". Os nomes de contêiner precisam ser diferentes; verifique se o contêiner já existe.
8.	Clique em **Avançar** para configurar as **Configurações de serialização** de saída. Assim como com a entrada, escolha **CSV** e clique no botão **OK**.
9.	Navegue até a guia **Funções** e clique em **Adicionar uma Função de Aprendizado de Máquina**.

    ![tutorial stream analytics aprendizado de máquina adicionar função de aprendizado de máquina](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)

10.	Na página **Configurações do Serviço Web de Aprendizado de Máquina** localize o espaço de trabalho do Aprendizado de Máquina, o serviço Web e o ponto de extremidade padrão. Para este tutorial, aplique as configurações manualmente para se familiarizar com a configuração de um serviço Web para qualquer espaço de trabalho, desde que você saiba a URL e tenha a chave. Forneça a **URL** e a **chave de API** do ponto de extremidade. Em seguida, clique em **OK**.

    ![tutorial stream analytics aprendizado de máquina serviço web de aprendizado de máquina](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)

11.	Navegue até a guia **Consultar** e modifique a consulta da seguinte forma:

```
	WITH subquery AS (  
		SELECT text, sentiment(text) as result from input  
	)  
	  
	Select text, result.[Score]  
	Into output  
	From subquery  
```    

Depois, basta clicar em **Salvar** para salvar a consulta.

## Iniciar o Trabalho do Stream Analytics e observar a saída

1.	Clique em **Iniciar** na parte inferior do trabalho.
2.	No **Diálogo Iniciar Consulta**, escolha **Hora Personalizada** e selecione uma hora anterior à do upload do CSV para o Armazenamento de Blobs. Clique em **OK**.

    ![tutorial stream analytics aprendizado de máquina hora personalizada](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)

3.	Navegue até o Armazenamento de Blobs usando a ferramenta utilizada para carregar o arquivo CSV. Este tutorial usou o Visual Studio.
4.	Depois de alguns minutos do início do trabalho, o contêiner de saída é criado e um arquivo CSV é carregado para ele.
5.	Um clique duplo no arquivo abrirá o editor de CSV padrão e deverá mostrar algo semelhante a isto:

    ![tutorial stream analytics aprendizado de máquina exibição csv](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)

## Conclusão

Neste tutorial, um trabalho de Stream Analytics foi criado para ler fluxo de dados de texto e analisar o sentimento nele em tempo real. Você conseguiu fazer tudo isso sem precisar se preocupar com a complexidade de criar um modelo de análise de sentimento. Essa é uma das vantagens do Cortana Intelligence Suite.

A métrica relativa à função de Aprendizado de Máquina do Azure também pode ser observada. Clique na guia **MONITOR**. Há três métricas relativas à função.
  
- SOLICITAÇÕES DE FUNÇÃO indica o número de solicitações para o serviço Web de aprendizado de máquina.
- EVENTOS DE FUNÇÃO indica o número de eventos na solicitação. Por padrão, cada solicitação ao serviço Web do AM contém até mil eventos.
- FALHAS NAS SOLICITAÇÕES DE FUNÇÃO indica o número de solicitações com falha para o serviço Web de aprendizado de máquina.

    ![tutorial stream analytics aprendizado de máquina exibição no monitor am](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)

<!---HONumber=AcomDC_0727_2016-->