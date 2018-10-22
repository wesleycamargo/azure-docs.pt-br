---
title: 'Tutorial: Criar e gerenciar um trabalho do Stream Analytics usando o portal do Azure | Microsoft Docs'
description: Este tutorial ilustra de ponta a ponta como usar o Azure Stream Analytics para analisar chamadas fraudulentas em um fluxo de chamada telefônica.
services: stream-analytics
author: sidramadoss
ms.author: sidram
manager: kfile
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/04/2018
ms.openlocfilehash: 1955fc033e0351be9da89bbee11dc41d6281a63a
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433983"
---
# <a name="create-a-stream-analytics-job-to-analyze-phone-call-data-and-visualize-results-in-a-power-bi-dashboard"></a>Criar um trabalho do Stream Analytics para analisar dados de chamada telefônica e visualizar os resultados em um painel do Power BI
 
Este tutorial mostra como usar o Azure Stream Analytics para analisar uma chamada telefônica de exemplo que é gerada por um aplicativo cliente. Os dados de chamada telefônica gerados pelo aplicativo cliente contêm algumas chamadas fraudulentas e definiremos um trabalho do Stream Analytics para filtrar essas chamadas.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Gerar dados de chamada telefônica de exemplo e enviá-los aos Hubs de Eventos do Azure  
> * Criar um trabalho de Stream Analytics   
> * Configurar a entrada e a saída para o trabalho  
> * Definir uma consulta para filtrar chamadas fraudulentas  
> * Testar e iniciar o trabalho  
> * Visualizar os resultados no Power BI 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).  
* Faça logon no [Portal do Azure](https://portal.azure.com/).  
* Baixe o aplicativo gerador de evento de chamada telefônica [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) do Centro de Download da Microsoft ou obtenha o código-fonte do [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).  

## <a name="create-an-azure-event-hub"></a>Criar um Hub de Eventos do Azure 

Para que o Stream Analytics possa analisar o fluxo de dados de chamadas fraudulentas, você deve enviar os dados ao Azure. Neste tutorial, você enviará dados ao Azure usando o [Hubs de Eventos do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs). Para este tutorial, você cria um hub de eventos e faz com que o aplicativo gerador de evento envie dados de chamadas para esse hub de eventos. Use as seguintes etapas para criar um Hub de Eventos:

1. Faça logon no Portal do Azure.  
2. Selecione **Criar um recurso** > **Internet das Coisas** > **Hubs de Eventos**.  

   ![Localizar o hub de eventos](media/stream-analytics-manage-job/find-eh.png)
3. Preencha o painel **Criar namespace** com os seguintes valores:  

   |**Configuração**  |**Valor sugerido** |**Descrição**  |
   |---------|---------|---------|
   |NOME     | myEventHubNS        |  Um nome exclusivo para identificar o namespace de hub de eventos.       |
   |Assinatura     |   \<Sua assinatura\>      |   Selecione uma assinatura do Azure em que deseja criar o hub de eventos.      |
   |Grupo de recursos     |   MyASADemoRG      |  Selecione **Criar Novo** e insira um novo nome de grupo de recursos para a conta.       |
   |Local padrão     |   Oeste dos EUA 2      |    Local onde o namespace do hub de eventos pode ser implantado.     |

4. Use as opções padrão nas configurações restantes e selecione **Criar**.  

   ![Criar namespace do hub de eventos](media/stream-analytics-manage-job/create-ehns.png)

5. Quando o namespace concluir a implantação, vá para **Todos os recursos** > Localizar "myEventHubNS" na lista de recursos do Azure > selecione para abri-lo.  
6. Em seguida, selecione **+Hub de Eventos** > **Dê o nome** "MyEventHub" ao o hub de eventos. Você pode usar um nome diferente. Use as opções padrão nas configurações restantes, selecione **Criar** e aguarde até que a implantação seja concluída com êxito.

   ![Criar hub de eventos](media/stream-analytics-manage-job/create-eh.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Conceder acesso para o hub de eventos e obter uma cadeia de caracteres de conexão

Antes que um processo possa enviar dados aos Hubs de Eventos do Azure, o hub de eventos deve ter uma política que permita o devido acesso. A política de acesso produz uma cadeia de conexão que inclui informações de autorização.

1. Navegue até os **Hubs de Eventos** criados na etapa anterior, ou seja, "MyEventHub" > selecione **Políticas de acesso compartilhado** no painel do hub de eventos > selecione **+Adicionar**.  
2. Defina o nome da política **Mypolicy** > e selecione **Gerenciar** > Selecione **Criar**.  

   ![Criar política de acesso compartilhado do hub de eventos](media/stream-analytics-manage-job/create-ehpolicy.png)

3. Depois que a política é implantada, selecione-a para abri-la, localize a **cadeia de conexão-chave primária** e selecione a **cópia** ao lado da cadeia de conexão.  
4. Cole a cadeia de conexão em um editor de texto. Você precisará dessa cadeia de conexão na próxima seção.  

   A cadeia de conexão tem esta aparência:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>` 

   Observe que a cadeia de conexão contém vários pares de chave-valor separados por ponto e vírgula: Endpoint, SharedAccessKeyName, SharedAccessKey e EntityPath.  

5. Remova o par EntityPath da cadeia de conexão (não se esqueça de remover o ponto e vírgula anterior).

## <a name="start-the-event-generator-application"></a>Iniciar o aplicativo gerador de evento

Antes de iniciar o aplicativo TelcoGenerator, configure-o para enviar dados para os Hubs de Eventos do Azure criados anteriormente.

1. Extraia o conteúdo do arquivo [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).  
2. Abra o `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` em um editor de texto de sua escolha (há mais de um arquivo .config; verifique se abriu o correto).  

3. Atualize o elemento <appSettings> no arquivo de configuração com os seguintes detalhes:

   * Defina o valor da chave EventHubName como o valor de EntityPath na cadeia de conexão.  
   * Defina o valor da chave Microsoft.ServiceBus.ConnectionString como a cadeia de conexão sem o valor EntityPath, que é o valor que você obteve na etapa 5 na seção anterior.

4. Salve o arquivo.  
5. Em seguida, abra uma janela de comando, altere para a pasta em que você descompactou o aplicativo TelcoGenerator e digite o seguinte comando:

   ```
   telcodatagen.exe 1000 0.2 2
   ```

   Esse comando usa os seguintes parâmetros:
   * **Número de registros de dados de chamadas por hora**.  
   * **Porcentagem de probabilidade de fraude** – que é a frequência com que o aplicativo deve simular uma chamada fraudulenta. O valor 0,2 significa que cerca de 20% dos registros de chamada parecerão ser fraudulentos.  
   * **Duração em horas** – o número de horas que o aplicativo deve ser executado. Você também pode interromper o aplicativo a qualquer momento encerrando o processo (Ctrl+C) na linha de comando.

   Depois de alguns segundos, o aplicativo é iniciado exibindo registros de chamada telefônica na tela, enquanto envia para o hub de eventos. Os dados de chamadas telefônicas contêm os seguintes campos:

   |**Registro**  |**Definição**  |
   |---------|---------|
   |CallrecTime    |  Carimbo de data/hora para a hora de início da chamada.       |
   |SwitchNum     |  Chave do telefone usada para se conectar à chamada. Neste exemplo, as opções são cadeias de caracteres que representam o país de origem (Estados Unidos, China, Reino Unido, Alemanha ou Austrália).       |
   |CallingNum     |  Número de telefone do autor da chamada.       |
   |CallingIMSI     |  A Identidade do Assinante Móvel Internacional (IMSI). É um identificador exclusivo do autor da chamada.       |
   |CalledNum     |   O número de telefone do destinatário da chamada.      |
   |CalledIMSI     |  Identidade do Assinante Móvel Internacional (IMSI). É um identificador exclusivo do destinatário da chamada.       |

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics 

Agora que você tem um fluxo de eventos de chamada, pode criar um trabalho do Stream Analytics que lê dados do hub de eventos.

1. Para criar um trabalho do Stream Analytics, navegue até o portal do Azure  

2. No portal do Azure, selecione **Criar um recurso** > **Internet das Coisas** > **Trabalho do Stream Analytics**.  

3. Preencha o painel **Novo trabalho do Stream Analytics** com os seguintes valores:  

   |**Configuração**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Nome do trabalho     |  ASATutorial       |   Um nome exclusivo para identificar o namespace de hub de eventos.      |
   |Assinatura    |  \<Sua assinatura\>   |   Selecione uma assinatura do Azure em que deseja criar o trabalho.       |
   |Grupo de recursos   |   MyASADemoRG      |   Selecione **Usar existente** e insira um novo nome de grupo de recursos para sua conta.      |
   |Local padrão   |    Oeste dos EUA 2     |      Local onde o trabalho pode ser implantado. É recomendável colocar o trabalho e o hub de eventos na mesma região para melhor desempenho e para que não seja necessário pagar para transferir dados entre regiões.      |
   |Ambiente de hospedagem    | Nuvem        |     Os trabalhos do Stream Analytics podem ser implantados na nuvem ou na borda. O Cloud permite que você implante no Azure Cloud e o Edge permite que você implante em um dispositivo IoT Edge.    |
   |Unidades de transmissão     |    1       |      As unidades de streaming representam os recursos de computação necessários para executar um trabalho. Por padrão, esse valor é definido como 1. Para saber mais sobre como dimensionar unidades de streaming, confira o artigo [Entendendo e ajustando as unidades de streaming](stream-analytics-streaming-unit-consumption.md).      |

   ![Criar um trabalho](media/stream-analytics-manage-job/create-a-job.png)   

4. Use as opções padrão nas configurações restantes, selecione **Criar** e aguarde até que a implantação seja concluída com êxito.

## <a name="configure-job-input"></a>Configurar entrada de trabalho

A próxima etapa é definir uma fonte de entrada de onde o trabalho lerá os dados. Para este tutorial, você usará o hub de eventos criado na seção anterior como entrada. Execute as seguintes etapas para configurar a entrada do trabalho:

1. No portal do Azure, abra o painel **Todos os recursos** e localize o trabalho ASATutorial do Stream Analytics.  

2. Na seção **Topologia do Trabalho** do painel do trabalho do Stream Analytics, selecione a opção **Entradas**.  

3. Selecione **+Adicionar entrada de fluxo** (entrada de referência diz respeito aos dados de pesquisa estática, que você não usará neste tutorial), **Hub de eventos** e, em seguida, preencha o painel com os seguintes valores:  

   |**Configuração**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de entrada     |  CallStream       |  Forneça um nome amigável para identificar a entrada. O alias de entrada pode conter somente caracteres alfanuméricos, hifens e sublinhados e deve ter entre 3 e 63 caracteres.       |
   |Assinatura    |   \<Sua assinatura\>      |   Selecione a assinatura do Azure em que você criou o hub de eventos. O hub de eventos pode estar na mesma assinatura ou em uma diferente da do trabalho do Stream Analytics.       |
   |Namespace do Hub de Eventos    |  MyEventHubNS       |  Selecione o namespace do hub de eventos criado na seção anterior. Todos os namespaces de hub de eventos disponíveis na sua assinatura atual aparecem na lista suspensa.       |
   |Nome do Hub de Eventos    |   MyEventHub      |  Selecione o hub de eventos criado na seção anterior. Todos os hubs de eventos disponíveis na sua assinatura atual aparecem na lista suspensa.       |
   |Nome da política do Hub de Eventos   |  MyPolicy       |  Selecione a política de acesso compartilhado de hub de eventos criada na seção anterior. Todas as políticas de hubs de eventos disponíveis na sua assinatura atual aparecem na lista suspensa.       |

   ![Configurar entrada](media/stream-analytics-manage-job/configure-input.png) 

4. Use as opções padrão nas configurações restantes, selecione **Salvar** e aguarde até que a implantação seja concluída com êxito.

## <a name="configure-job-output"></a>Configurar saída de trabalho 

A última etapa é definir um coletor de saída para o trabalho em que ele pode gravar os dados transformados. Para este tutorial, você exportará os resultados para o Power BI e visualizará a data. Execute as seguintes etapas para configurar a saída do trabalho:

1. No portal do Azure, abra o painel **Todos os recursos** e localize o trabalho ASATutorial do Stream Analytics.  

2. Na seção **Topologia do Trabalho** do painel do trabalho do Stream Analytics, selecione a opção **Saídas**.  

3. Selecione **+Adicionar** > **Power BI** e preencha o formulário com os seguintes detalhes (você pode fornecer um nome amigável para identificar o Alias de saída, o Nome do conjunto de dados e o Nome da tabela, conforme mostrado na tabela) e selecione **Autorizar**:  

   |**Configuração**  |**Valor sugerido**  |
   |---------|---------|---------|
   |Alias de saída  |  MyPBIoutput  |
   |Nome do conjunto de dados  |   ASAdataset  | 
   |Nome da tabela |  ASATable  | 

   ![Configurar saída](media/stream-analytics-manage-job/configure-output.png)  

4. Depois de selecionar **Autorizar**, uma janela pop-up aparece e solicita as credenciais para autenticar sua conta do Power BI. Após a autorização bem-sucedida, procure **Salvar** as configurações. 

## <a name="define-a-query-to-analyze-input-data"></a>Definir uma consulta para analisar os dados de entrada

Depois configurar um trabalho do Stream Analytics para ler um fluxo de dados de entrada, a próxima etapa é criar uma transformação que analisa os dados em tempo real. Defina a consulta de transformação usando a [Linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/dn834998.aspx). Neste tutorial, você define uma consulta que detecta chamadas fraudulentas nos dados do telefone. 

Neste exemplo, vamos considerar que chamadas fraudulentas são as que se originam do mesmo usuário, mas em locais diferentes, e o tempo entre as duas chamadas é de cinco segundos. Por exemplo, o mesmo usuário não pode legitimamente fazer uma chamada da Austrália e dos Estados Unidos ao mesmo tempo. Para definir a consulta de transformação para o trabalho do Stream Analytics, execute as seguintes etapas:

1. No portal do Azure, abra o painel **Todos os recursos** e abra o trabalho **ASATutorial** do Stream Analytics criado anteriormente.  

2. Na seção **Topologia do Trabalho** do painel do trabalho do Stream Analytics, selecione a opção **Consulta**. A janela pop-up lista as entradas e saídas configuradas para o trabalho e permite que você crie uma consulta para transformar o fluxo de entrada.  

3. Em seguida, substitua a consulta existente no editor pelos seguintes dados; essa consulta realiza uma autojunção em um intervalo de 5 segundos de dados de chamadas:

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   Para verificar chamadas fraudulentas, você deve fazer a autojunção dos dados de streaming com base no valor `CallRecTime`. Em seguida, você pode procurar registros de chamada em que o valor `CallingIMSI` (o número de origem) é o mesmo, mas o valor `SwitchNum` (país de origem) é diferente. Quando você usa uma operação JOIN com os dados de streaming, a junção deve fornecer alguns limites sobre a distância de tempo que as linhas correspondentes podem ter umas das outras. Como o fluxo de dados é infinito, os limites de tempo para a relação são especificados dentro da cláusula ON da junção, usando a função [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics). 

   Essa consulta é como uma junção SQL normal, exceto pela função DATEDIFF. A função DATEDIFF usada nessa consulta é específica do Stream Analytics e ela deverá aparecer dentro da cláusula `ON...BETWEEN`.  

4. **Salve** a consulta.  

   ![definir consulta](media/stream-analytics-manage-job/define-query.png) 

## <a name="test-your-query"></a>Testar a consulta

Você pode testar uma consulta do editor de consultas; é necessário ter dados de exemplo para testar uma consulta. Para este passo a passo, você vai extrair dados de exemplo do fluxo da chamada telefônica que estará disponível para o hub de eventos. Execute as seguintes etapas para testar a consulta:

1. Verifique se o aplicativo TelcoGenerator está em execução e gerando os registros de chamada.  

2. No painel **Consulta**, selecione os pontos ao lado da entrada CallStream e selecione **Dados de exemplo da entrada**. Isso abre um painel que permite especificar quantos dados de exemplo devem ser lidos do fluxo de entrada.  

   ![Dados de entrada de exemplo](media/stream-analytics-manage-job/sample-input-data.png)  

3. Definir **Minutos** como 3 e selecionar **OK**. Três minutos de dados são coletados como amostra, e você é notificado quando os dados de exemplo ficam prontos. Você pode exibir o status de amostragem na barra de notificação. 

   Os dados de exemplo são armazenados temporariamente e estão disponíveis enquanto a janela de consulta estiver aberta. Se você fechar a janela de consulta, os dados de exemplo serão descartados e você terá que criar um novo conjunto de dados de exemplo. Como alternativa, você pode obter um arquivo .json que contém dados de exemplo de [do GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)e, em seguida, carregar esse arquivo .json para usar como dados de exemplo para a entrada CallStream.  

4. Selecione **Testar** para testar a consulta. Você verá os resultados de saída como mostrado nesta captura de tela:  

   ![Saída de teste](media/stream-analytics-manage-job/test-output.png)

## <a name="start-the-job-and-visualize-output"></a>Iniciar o trabalho e visualizar a saída

1. Para iniciar o trabalho, navegue até o painel **Visão geral** do seu trabalho e selecione **Iniciar**.  

2. Selecione **Agora** para a hora de início da saída do trabalho e selecione **Iniciar**. O trabalho será iniciado em alguns minutos e você poderá exibir o status na barra de notificação.  

3. Depois que o trabalho for concluído com êxito, navegue até [Powerbi.com](https://powerbi.com/) e entre com sua conta corporativa ou de estudante. Se a consulta do trabalho do Stream Analytics gerar resultados como saída, o conjunto de dados já estará criado. Navegue até a guia **Conjuntos de Dados**. Você pode exibir um conjunto de dados chamado "ASAdataset".  

4. No workspace, selecione **+Criar**. Crie um novo painel e nomeie-o Chamadas Fraudulentas. Você adicionará dois blocos a esse painel; um bloco é usado para exibir a contagem de chamadas fraudulentas em determinada instância e o outro tem uma visualização de gráfico de linha.  

5. Na parte superior da janela, selecione **Adicionar bloco** > selecione **Fluxo de dados personalizado** > Avançar > escolha **ASAdataset** > Para visualização de tipo, selecione **Cartão** e Campos como **fraudulentcalls**. Selecione **Avançar** > insira um nome para o bloco e selecione **Aplicar**.  

   ![Criar blocos](media/stream-analytics-manage-job/create-tiles.png)

6. Siga a etapa 4 novamente, com as seguintes opções:
   * Quando você chegar em Tipo de Visualização, selecione Gráfico de linhas.  
   * Adicionar um eixo e selecione **windowend**.  
   * Adicione um valor e selecione **fraudulentcalls**.  
   * Para **Janela de tempo para exibir**, selecione os últimos 10 minutos.  

7. Seu painel se parece com a captura de tela a seguir, depois que os dois blocos são adicionados. Você observará que, se estiver executando o aplicativo de remetente do hub de eventos e o aplicativo do Streaming Analytics, o painel do Power BI será atualizado periodicamente de acordo com a chegada de novos dados.  

   ![Resultados do Power BI](media/stream-analytics-manage-job/power-bi-results.png)

## <a name="embedding-your-powerbi-dashboard-in-a-web-application"></a>Inserindo seu painel do Power BI em um aplicativo Web

Nesta parte do tutorial, você usará um aplicativo Web [ASP.NET](http://asp.net/) de exemplo criado pela equipe do Power BI para inserir seu painel. Para saber mais sobre a inserção de painéis, confira o artigo [Inserindo com o Power BI](https://docs.microsoft.com/power-bi/developer/embedding).

Neste tutorial, vamos seguir as etapas para o aplicativo O Usuário Possui Dados. Para configurar o aplicativo, vá para o repositório [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) do Github e siga as instruções na seção **O Usuário Possui Dados** (usar as URLs de redirecionamento e de página inicial da subseção **integrate-dashboard-web-app**). Como estamos usando painel de exemplo, use o código de exemplo integrate-dashboard-web-app localizado no [repositório GitHub](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User%20Owns%20Data/integrate-dashboard-web-app).
Quando o aplicativo estiver em execução no seu navegador, siga estas etapas para inserir o painel criado anteriormente na página:

1. Selecione **Entrar no Power BI**, que concede ao aplicativo acesso aos painéis da sua conta do Power BI.  

2. Selecione o botão **Obter Painéis**, que exibe painéis da sua conta em uma tabela. Localize o nome do painel criado anteriormente (powerbi-embedded-dashboard) e copie a **EmbedUrl** correspondente.  

3. Por fim, cole a **EmbedUrl** no campo de texto correspondente e selecione **Inserir Painel**. Agora você pode exibir o mesmo painel inserido em um aplicativo Web.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um trabalho do Stream Analytics simples, analisou os dados de entrada e apresentou resultados em um painel do Power BI. Para saber mais sobre trabalhos do Stream Analytics, prossiga para o seguinte tutorial:

> [!div class="nextstepaction"]
> [Executar o Azure Functions com trabalhos do Stream Analytics](stream-analytics-with-azure-functions.md)
