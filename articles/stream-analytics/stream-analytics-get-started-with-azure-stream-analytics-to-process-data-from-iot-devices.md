<properties
    pageTitle="Introdução ao Stream Analytics do Azure para processar dados de dispositivos IoT. | Microsoft Azure"
    description="Marcas e dados de sensor de fluxos IoT com o processamento de dados em tempo real e Stream Analytics"
    keywords="solução iot, introdução ao ioT"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>Introdução ao Stream Analytics do Azure para processar dados de dispositivos IoT

Neste tutorial, você aprenderá a criar a lógica de processamento de fluxo para reunir dados dos dispositivos da Internet das Coisas (IoT). Usaremos um caso de uso real da Internet das coisas (IoT) para demonstrar como compilar uma solução rápida e econômica.

## <a name="prerequisites"></a>Pré-requisitos

-   [Assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Exemplos de arquivos de consulta e de dados que podem ser baixados de [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Cenário

Contoso, que é uma empresa no espaço de automação industrial, automatizou completamente seu processo de fabricação. O maquinário desta fábrica tem sensores capazes de emitir fluxos de dados em tempo real. Nesse cenário, um gerente de chão de fábrica quer ter informações em tempo real dos dados de sensor para procurar por padrões e tomar ações com relação a eles. Nós usaremos a Stream Analytics Query Language (SAQL) sobre os dados do sensor para descobrir padrões interessantes no fluxo de entrada de dados.

Aqui, os dados estão sendo gerados de um dispositivo de tag de sensor da Texas Instruments.

![Tag de sensor Texas Instruments](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

A carga de dados está no formato JSON e é semelhante ao seguinte:


    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  

Em um cenário real, você poderia ter centenas desses sensores gerando eventos como um fluxo. De forma ideal, um dispositivo de gateway executaria um código para enviar por push esses eventos aos [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) ou aos [Hubs IoT do Azure](https://azure.microsoft.com/services/iot-hub/). Seu trabalho do Stream Analytics receberia esses eventos dos Hubs de Eventos e executaria consultas de análise em tempo real nos fluxos. Em seguida, você poderia enviar os resultados a uma das [saídas com suporte](stream-analytics-define-outputs.md).

Para facilitar o uso, este guia de Introdução fornece um arquivo de dados de exemplo, capturado de dispositivos de tag de sensor reais. Você pode executar consultas nos dados de exemplo e ver os resultados. Nos tutoriais subsequentes, você aprenderá a conectar seu trabalho a entradas e saídas e implantá-lo para o serviço do Azure.

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

1. No [Portal do Azure](http://manage.windowsazure.com), clique em **STREAM ANALYTICS** e clique em **NOVO** no canto inferior esquerdo da página para criar um novo trabalho de análise.

    ![Criar um novo trabalho do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

2. Clique em **CRIAÇÃO RÁPIDA**.

3. Para a configuração **CONTA DE ARMAZENAMENTO DE MONITORAMENTO REGIONAL**, clique em **CRIAR NOVA CONTA DE ARMAZENAMENTO** e dê um nome exclusivo a ela. O Stream Analytics do Azure usará essa conta para armazenar informações de monitoramento para todos os trabalhos futuros.

    > [AZURE.NOTE] Você deve criar essa conta de armazenamento apenas uma vez por região. Esse armazenamento será compartilhado entre todos os trabalhos do Stream Analytics que forem criados nessa região.

4. Clique em **CRIAR TRABALHO DO STREAM ANALYTICS** na parte inferior da página.

    ![Configuração da conta de armazenamento](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.jpg)

## <a name="azure-stream-analytics-query"></a>Consulta do Stream Analytics do Azure

Clique na guia **CONSULTA** para acessar o Editor de Consulta. A guia **CONSULTA** contém uma consulta de T-SQL que executa a transformação dos dados de evento de entrada.

## <a name="archive-your-raw-data"></a>Arquivar seus dados brutos

A forma mais simples de consulta é uma consulta passagem que arquiva todos os dados de entrada em suas saídas indicadas.

![Consulta de trabalho de arquivamento](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

Baixe agora o arquivo de dados de exemplo do [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) para um local em seu computador. Cole a consulta do arquivo PassThrough.txt. Clique no botão **Teste** abaixo e selecione o arquivo de dados chamado HelloWorldASA-InputStream.json de seu local de download.

![Botão Testar no Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

![Testar fluxo de entrada](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

Você pode ver os resultados da consulta no navegador, conforme mostra a captura de tela a seguir.

![Resultados do teste](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

## <a name="filter-the-data-based-on-a-condition"></a>Filtrar os dados com base em uma condição

Vamos tentar filtrar os resultados com base em uma condição. Gostaríamos de mostrar os resultados somente dos eventos que são provenientes do "SensorA". A consulta está no arquivo Filtering.txt.

![Filtrar um fluxo de dados](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Observe que a consulta, que diferencia maiúsculas de minúsculas, compara um valor de cadeia de caracteres. Clique no botão **Executar novamente** para executar a consulta. A consulta deve retornar apenas 389 linhas de 1860 eventos.

![Segundo resultado do teste do teste de consulta](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

## <a name="alert-to-trigger-a-business-workflow"></a>Alerta para disparar o fluxo de trabalho de negócios

Vamos tornar nossa consulta mais detalhada. Para cada tipo de sensor, se você deseja monitorar a temperatura média por 30 segundos e exibir os resultados somente se a temperatura média estiver acima de 100 graus. Escreveremos a consulta a seguir e depois clicaremos em **Executar novamente** para ver os resultados. A consulta está no arquivo ThresholdAlerting.txt.

![Consulta de filtro de 30 segundos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Agora, você pode ver os resultados que contêm apenas 245 linhas e nomes dos sensores nos quais a temperatura média é superior a 100. Esta consulta agrupa o fluxo de eventos por **dspl**, que é o nome de sensor, sobre uma **Janela em Cascata** de 30 segundos. Consultas temporais devem declarar como queremos que o tempo progrida. Usando a cláusula **TIMESTAMP BY**, especificamos a coluna **OUTPUTTIME** para associar tempos a todos os cálculos temporais. Para obter informações detalhadas, leia os artigos do MSDN sobre [Gerenciamento de tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e [Funções de janelas](https://msdn.microsoft.com/library/azure/dn835019.aspx).

![Temp acima de 100](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

## <a name="detect-absence-of-events"></a>Detectar ausência de eventos

Como podemos escrever uma consulta para localizar a falta de eventos de entrada? Vamos descobrir a última vez que um sensor enviou dados e, depois, não enviou mais eventos no próximo minuto. A consulta está no arquivo AbsenseOfEvent.txt.

![Detectar ausência de eventos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-12.png)

Aqui, usamos uma junção **EXTERNA ESQUERDA** no mesmo fluxo de dados (autojunção). Para uma junção **INTERNA**, um resultado retorna somente quando uma correspondência é encontrada.  Mas para uma junção **EXTERNA ESQUERDA**, se um evento do lado esquerdo da junção for incomparável, uma linha com NULL para todas as colunas do lado direito retornará. Essa técnica é muito útil para localizar a ausência de eventos. Consulte a documentação do MSDN para saber mais sobre [JUNÇÃO](https://msdn.microsoft.com/library/azure/dn835026.aspx).

![Resultados da junção](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-13.png)

## <a name="conclusion"></a>Conclusão

O objetivo deste tutorial é demonstrar como escrever consultas de linguagem de consulta do Stream Analytics e ver os resultados no navegador. No entanto, isso está apenas começando. Você pode fazer muito mais com Stream Analytics. O Stream Analytics dá suporte a várias entradas e saídas e ainda pode usar as funções no Aprendizado de Máquina do Azure para torná-la uma ferramenta robusta de análise de fluxos de dados. Você pode começar a explorar mais sobre o Stream Analytics usando nosso [Mapa de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/). Para saber mais sobre como escrever consultas, leia o artigo sobre [padrões comuns de consulta](./stream-analytics-stream-analytics-query-patterns.md).



<!--HONumber=Oct16_HO2-->


