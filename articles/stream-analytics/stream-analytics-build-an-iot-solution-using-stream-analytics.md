---
title: Compilar uma solução de IoT usando o Stream Analytics | Microsoft Docs
description: Tutorial de introdução da solução de IoT Stream Analytics de um cenário de pedágio
keywords: solução de iot, funções da janela
documentationcenter: ''
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun

ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 09/26/2016
ms.author: jeffstok

---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Compilar uma solução de IoT usando o Stream Analytics
## <a name="introduction"></a>Introdução
Neste tutorial, você aprenderá a usar o Stream Analytics do Azure para aprofundar-se em seus dados em tempo real. Os desenvolvedores podem facilmente combinar fluxos de dados, como fluxos de cliques, logs e eventos gerados pelo dispositivo, com registros históricos ou dados de referência para gerar insights comerciais. Como um serviço de computação de fluxo em tempo real totalmente gerenciado e hospedado no Microsoft Azure, o Stream Analytics do Azure fornece resiliência interna, baixa latência e escalabilidade para você colocar tudo em funcionamento em minutos.

Depois de concluir este tutorial, você poderá:

* Familiarizar-se com o portal do Stream Analytics do Azure.
* Configurar e implantar um trabalho de transmissão.
* Articular problemas do mundo real e resolvê-los usando a linguagem de consulta do Stream Analytics.
* Desenvolver com confiança soluções de transmissão para seus clientes usando o Stream Analytics do Azure.
* Use o monitoramento e experiência de log para solucionar problemas.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

* A versão mais recente do [Azure PowerShell](../powershell-install-configure.md)
* O Visual Studio 2015 ou o [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
* Uma [assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/)
* Privilégios administrativos no computador
* Baixar [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) do Centro de Download da Microsoft
* Opcional: código-fonte do gerador de evento TollApp no [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## <a name="scenario-introduction:-“hello,-toll!”"></a>Introdução ao cenário: “Olá, pedágio!”
Uma praça de pedágio é um fenômeno comum. Você se depara com elas em várias estradas, pontes e túneis em todo o mundo. Cada praça de pedágio tem várias cabines do pedágio. Em cabines manuais, você para e paga o pedágio para um atendente. Em cabines automatizadas, um sensor sobre cada uma das cabines lê um cartão RFID fixado ao para-brisa do veículo conforme você passa pela cabine. É fácil visualizar a passagem dos veículos por essas estações de pedágio como uma transmissão de eventos sobre quais operações interessantes podem ser executadas.

![Imagem de carros em cabines de pedágio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Dados de entrada
Este tutorial funciona com dois fluxos de dados. Sensores instalados na entrada e na saída das praças de pedágio produzem o primeiro fluxo. O segundo fluxo é um conjunto de dados de pesquisa estático que contém dados de registro dos veículos.

### <a name="entry-data-stream"></a>Fluxo de dados de entrada
O fluxo de dados de entrada contém informações sobre os carros que entram nas praças de pedágio.

| TollID | EntryTime | PlacaDeCarro | Estado | Faça | Modelo | VehicleType | VehicleWeight | Pedágio | Marca |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NOVA IORQUE |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |1001 YXZ |NOVA IORQUE |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |1003 XYZ |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |1007 BNJ |NOVA IORQUE |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Aqui está uma breve descrição das colunas:

| Coluna | Descrição |
| --- | --- |
| TollID |A ID da cabine de pedágio que identifica exclusivamente uma cabine de pedágio |
| EntryTime |A data e hora da entrada do veículo na cabine de pedágio no horário UTC |
| PlacaDeCarro |O número da placa de licença do veículo |
| Estado |Um estado nos Estados Unidos |
| Faça |O fabricante do automóvel |
| Modelo |O número do modelo do automóvel |
| VehicleType |1 para veículos de passageiros ou 2 para veículos comerciais |
| WeightType |Peso do veículo em toneladas; 0 para veículos de passageiros |
| Pedágio |Valor do pedágio em dólares americanos |
| Marca |A e-Tag no automóvel que automatiza o pagamento; deixado em branco quando o pagamento é feito manualmente |

### <a name="exit-data-stream"></a>Fluxo de dados de saída
O fluxo de dados de saída contém informações sobre os carros que estão saindo da praça de pedágio.

| **TollId** | **ExitTime** | **PlacaDeCarro** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |1001 YXZ |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |1003 XYZ |
| 1 |2014-09-10T12:08:00.0000000Z |1007 BNJ |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

Aqui está uma breve descrição das colunas:

| Coluna | Descrição |
| --- | --- |
| TollID |A ID da cabine de pedágio que identifica exclusivamente uma cabine de pedágio |
| ExitTime |A data e hora de saída do veículo da cabine de pedágio no horário UTC |
| PlacaDeCarro |O número da placa de licença do veículo |

### <a name="commercial-vehicle-registration-data"></a>Dados de registro de veículo comercial
Este tutorial usa um instantâneo estático de um banco de dados de registro de veículos comerciais.

| PlacaDeCarro | RegistrationId | Expirado |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Aqui está uma breve descrição das colunas:

| Coluna | Descrição |
| --- | --- |
| PlacaDeCarro |O número da placa de licença do veículo |
| RegistrationId |A ID de registro do veículo |
| Expirado |O status de registro do veículo: 0 se o registro do veículo estiver ativo, 1 se estiver vencido |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configurar o ambiente para o Stream Analytics do Azure
Para concluir este tutorial, você precisa de uma assinatura do Microsoft Azure. A Microsoft oferece uma avaliação gratuita dos serviços do Microsoft Azure.

Se não tiver uma conta do Azure, [solicite uma versão de avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Para se inscrever para uma avaliação gratuita, você precisará de um dispositivo móvel que possa receber mensagens de texto e de um cartão de crédito válido.
> 
> 

Siga as etapas na seção “Limpar sua conta do Azure” no final deste artigo para que você possa fazer o melhor uso de seu crédito Azure gratuito de US$ 200.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Provisionar os recursos do Azure necessários para o tutorial
Este tutorial requer dois hubs de eventos para receber transmissões de dados de *entrada* e de *saída*. O Banco de Dados SQL do Azure gera os resultados dos trabalhos do Stream Analytics. O Armazenamento do Azure armazena dados de referência sobre o registro do veículo.

Você pode usar o script Setup.ps1 na pasta TollApp no GitHub para criar todos os recursos necessários. Devido ao tempo, recomendamos que você execute-o. Se quiser saber mais sobre como configurar esses recursos no portal do Azure, veja o apêndice “Configurar recursos de tutorial no portal do Azure”.

Baixe e salve os arquivos e pastas [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) de suporte.

Abra uma janela do **Microsoft Azure PowerShell***como administrador*. Se ainda não tiver o Azure PowerShell, siga as instruções em [Instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para instalá-lo.

Como o Windows bloqueia automaticamente arquivos .ps1, .dll e .exe, você precisa definir a política de execução antes de executar o script. Verifique se a janela do Azure PowerShell está em execução *como administrador*. Execute **Set-ExecutionPolicy unrestricted**. Quando solicitado, digite **Y**.

![Captura de tela de "Set-ExecutionPolicy unrestricted" em execução na janela do Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Execute **Get-ExecutionPolicy** para confirmar se o comando funcionou.

![Captura de tela de "Get-ExecutionPolicy" em execução na janela do Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Vá até o diretório que tem os scripts e o aplicativo gerador.

![Captura de tela de "cd.\TollApp\TollApp" em execução na janela do Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Digite **.\\Setup.ps1** para configurar sua conta do Azure, crie e configure todos os recursos necessários e comece a gerar eventos. O script seleciona aleatoriamente uma região para criar seus recursos. Para especificar explicitamente uma região, você pode passar o parâmetro **-location** , como no exemplo a seguir:

**.\\Setup.ps1 -location “Central US”**

![Captura de tela da página de entrada do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

O script abre a página **Entrar** do Microsoft Azure. Insira suas credenciais de conta.

> [!NOTE]
> Se sua conta tiver acesso a várias assinaturas, será solicitado que você insira o nome da assinatura que deseja usar para o tutorial.
> 
> 

O script pode levar vários minutos para ser executado. Após a conclusão, a saída deve ser semelhante à seguinte captura de tela.

![Captura de tela da saída do script na janela do Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Você também verá outra janela semelhante à captura de tela a seguir. Este aplicativo está enviando eventos aos Hubs de Eventos do Azure, que é necessário para executar o tutorial. Sendo assim, não interrompa o aplicativo ou feche a janela até concluir o tutorial.

![Captura de tela de "Enviando dados de hub de evento"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Agora, você deve ver todos os recursos criados no portal do Azure. Vá para <https://manage.windowsazure.com> e entre com as credenciais da sua conta.

### <a name="azure-event-hubs"></a>Hubs de eventos do Azure
Clique em **BARRAMENTO DE SERVIÇO** no lado esquerdo do portal do Azure para ver os hubs de eventos criados pelo script na seção anterior.

![BARRAMENTO DE SERVIÇO](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Você verá todos os namespaces disponíveis na sua assinatura. Clique no que começa com *tolldata* (tolldata4637388511 em nosso exemplo). Clique na guia **HUBS DE EVENTOS** .

![Guia dos Hubs de Eventos no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

Você verá dois Hubs de Eventos chamados *entry* e *exit* criados nesse namespace.

![Captura de tela de hubs de eventos de "entrada" e "saída" no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### <a name="azure-storage-container"></a>Contêiner de armazenamento do Azure
1. Clique em **ARMAZENAMENTO** no lado esquerdo do portal do Azure para ver o contêiner de Armazenamento do Azure usado no tutorial.
   
    ![Item de menu de armazenamento](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)
2. Clique no que começa com *tolldata* (tolldata4637388511 em nosso exemplo). Clique na guia **CONTÊINERES** para ver o contêiner criado.
   
    ![Guia Contêineres no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)
3. Clique no contêiner **tolldata** para ver o arquivo JSON carregado com os dados de registro de veículos.
   
    ![Captura de tela do arquivo registration.json no contêiner](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure
1. Clique em **BANCOS DE DADOS SQL** no lado esquerdo do portal do Azure para ver o banco de dados SQL que será usado no tutorial.
   
    ![BANCOS DE DADOS SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)
2. Clique em **tolldatadb**.
   
    ![Captura de tela do banco de dados SQL criado](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)
3. Copie o nome do servidor sem o número da porta (*servername*.database.windows.net, por exemplo).

## <a name="connect-to-the-database-from-visual-studio"></a>Conectar-se ao banco de dados do Visual Studio
Use o Visual Studio para acessar os resultados de consultas no banco de dados de saída.

Conecte-se ao banco de dados SQL (o destino) do Visual Studio:

1. Abra o Visual Studio e clique em **Ferramentas** > **Conectar-se ao banco de dados**.
2. Se for solicitado, clique em **Microsoft SQL Server** como uma fonte de dados.
   
    ![Caixa de diálogo Alterar Fonte de Dados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)
3. No campo **Nome do servidor** , cole o nome copiado na seção anterior do portal do Azure (ou seja, *servername*.database.windows.net).
4. Clique em **Usar Autenticação do SQL Server**.
5. Digite **tolladmin** no campo **Nome de usuário** e **123toll!** in the **Senha** .
6. Clique em **Selecione ou insira um nome de banco de dados** e selecione **TollDataDB** como o banco de dados.
   
    ![Caixa de diálogo Adicionar Conexão](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
7. Clique em **OK**.
8. Abra o Gerenciador de Servidores.
   
    ![Gerenciador de Servidores](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
9. Veja quatro tabelas no banco de dados TollDataDB.
   
    ![Tabelas no banco de dados TollDataDB](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator:-tollapp-sample-project"></a>Gerador de eventos: projeto de exemplo TollApp
O script do PowerShell inicia automaticamente o envio de eventos usando o programa TollApp do aplicativo de exemplo. Você não precisa executar nenhuma etapa adicional.

No entanto, se estiver interessado nos detalhes da implementação, você encontrará o código-fonte do aplicativo TollApp no GitHub [samples/TollApp](https://aka.ms/azure-stream-analytics-toll-source).

![Captura de tela do código de exemplo exibido no Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics
1. No portal do Azure, abra o Stream Analytics e clique em **NOVO** no canto inferior esquerdo da página para criar um novo trabalho de análise.
   
    ![Novo botão](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)
2. Clique em **CRIAÇÃO RÁPIDA**. Selecione a mesma região onde os outros recursos são criados pelo script.
3. Para a configuração **CONTA DE ARMAZENAMENTO DE MONITORAMENTO REGIONAL**, selecione **CRIAR NOVA CONTA DE ARMAZENAMENTO** e dê um nome exclusivo a ela. O Stream Analytics do Azure usará essa conta para armazenar informações de monitoramento para todos os trabalhos futuros.
4. Clique em **CRIAR TRABALHO DO STREAM ANALYTICS** na parte inferior da página.
   
    ![Opção Criar um Trabalho do Stream Analytics](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Definir fontes de entrada
1. Clique no trabalho de análise criado no portal.
   
    ![Captura de tela do trabalho de análise no portal](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)
2. Clique na guia **ENTRADAS** para definir os dados de origem.
   
    ![A guia Entradas](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)
3. Clique em **ADICIONAR UMA ENTRADA**.
   
    ![A opção Adicionar uma Entrada](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)
4. Clique em **FLUXO DE DADOS** na primeira página.
   
    ![A opção Fluxo de Dados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)
5. Clique em **HUB DE EVENTO** na segunda página do assistente.
   
    ![A opção Hub de Evento](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)
6. Digite **EntryStream** como **ALIAS DE ENTRADA**.
7. Clique no menu suspenso **Hub de Eventos** e selecione um que comece com “TollData” (por exemplo, TollData9518658221).
8. Selecione **entry** como nome do hub de evento e **all** como nome da política do hub de evento.
   
    As configurações ficarão semelhantes:
   
    ![Configurações do hub de evento](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)
9. Na página seguinte, selecione **JSON** para **FORMATO DE SERIALIZAÇÃO DO EVENTO** e **UTF8** para **CODIFICAÇÃO**.
   
    ![Configurações de serialização](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)
10. Clique em **OK** na parte inferior da página para concluir o assistente.
    
    ![Captura de tela da entrada EntryStream no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)
    
    Agora que criou a transmissão de entrada, você seguirá as mesmas etapas para criar a transmissão de saída. Na terceira página do assistente, insira os valores da forma indicada na captura de tela a seguir.
    
    ![Configurações do fluxo de saída](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)
    
    Você definiu dois fluxos de entrada:
    
    ![Fluxos de entrada definidos no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)
    
    Em seguida, você adicionará a entrada de dados de referência para o arquivo de blob que contém os dados de registro do carro.
11. Clique em **ADICIONAR ENTRADA** e clique em **DADOS DE REFERÊNCIA**.
    
    ![Opções "Adicionar uma entrada" com Dados de Referência selecionados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)
12. Na página seguinte, selecione a conta de armazenamento que começa com **tolldata**. O nome do contêiner deve ser **tolldata** e o nome do blob em **PADRÃO DO CAMINHO** deve ser **registration.json**. Este nome de arquivo diferencia maiúsculas de minúsculas e deve estar em minúsculas.
    
    ![Configurações de armazenamento de blob](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)
13. Selecione os valores conforme mostrado na captura de tela na próxima página e clique em **OK** para concluir o assistente.
    
    ![Seleção de JSON para "Formato de serialização do evento" e UTF8 para "Codificação"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

Agora, todas as entradas são definidas.

![Captura de tela das três entradas definidas](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## <a name="define-output"></a>Definir saída
1. Clique na guia **SAÍDA** e clique em **ADICIONAR UMA SAÍDA**.
   
    ![A guia Saída e a opção “Adicionar uma saída”](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)
2. Clique em **Bancos de Dados SQL**.
3. Selecione o nome do servidor que foi usado na seção “Conectar-se ao banco de dados do Visual Studio” do artigo. O nome do banco de dados é **TollDataDB**.
4. Digite **tolladmin** no campo **USERNAME**, **123toll!** no campo **SENHA**, e **TollDataRefJoin** no campo **TABELA**.
   
    ![Configurações do Banco de Dados SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## <a name="azure-stream-analytics-query"></a>Consulta do Stream Analytics do Azure
A guia **CONSULTA** contém uma consulta SQL que transforma os dados de entrada.

![Uma consulta adicionada à guia Consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

Este tutorial tenta responder várias perguntas de negócios relacionadas aos dados de pedágio e criar consultas do Stream Analytics que possam ser usadas no Stream Analytics do Azure para fornecer uma resposta relevante.

Antes de você começar seu primeiro trabalho do Stream Analytics do Azure, vamos explorar alguns cenários e a sintaxe de consulta.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Introdução à linguagem de consulta do Stream Analytics do Azure
- - -
Digamos que você precise contar o número de veículos que entram em uma cabine de pedágio. Como se trata de um fluxo contínuo de eventos, você precisa definir um “período”. Vamos modificar a pergunta para "Quantos veículos entram em uma cabine de pedágio a cada três minutos?". Isso é conhecido como contagem em cascata.

Vejamos a consulta do Stream Analytics do Azure que responde essa pergunta:

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Como você pode ver, o Stream Analytics do Azure usa uma linguagem de consulta parecida com SQL e adiciona algumas extensões para especificar aspectos da consulta relacionados ao tempo.

Para obter mais detalhes, leia sobre [Gerenciamento de tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e construções de [Janelas](https://msdn.microsoft.com/library/azure/dn835019.aspx) usadas na consulta no MSDN.

## <a name="testing-azure-stream-analytics-queries"></a>Testando consultas do Stream Analytics do Azure
Agora que você escreveu sua primeira consulta do Stream Analytics do Azure, é hora de testá-la usando os arquivos de dados de exemplo localizados na pasta TollApp no caminho a seguir:

**..\\TollApp\\TollApp\\Data**

Esta pasta contém os seguintes arquivos:

* Entry.json
* Exit.JSON
* registration.json

## <a name="question-1:-number-of-vehicles-entering-a-toll-booth"></a>Pergunta 1: número de veículos que entram em uma cabine de pedágio
1. Abra o portal do Azure e navegue até o trabalho do Stream Analytics do Azure que foi criado. Clique na guia **CONSULTA** e copie e cole a consulta da seção anterior.
   
    ![Consulta colada na guia Consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)
2. Para validar essa consulta em relação aos dados de exemplo, clique no botão **Testar** . Na caixa de diálogo que é aberta, navegue até Entry.json, um arquivo com os dados de amostra do fluxo de eventos **EntryTime** .
   
    ![Captura de tela do arquivo Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)
3. Valide que a saída da consulta é conforme o esperado:
   
    ![Resultados do teste](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## <a name="question-2:-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>Pergunta 2: relatar o tempo total para cada carro passar pela cabine de pedágio
O tempo médio necessário para um carro passar pela cabine de pedágio ajuda a avaliar a eficiência do processo e a experiência do cliente.

Para encontrar o tempo total, você precisa associar o fluxo EntryTime ao fluxo ExitTime. Associe os fluxos nas colunas TollId e LicencePlate. O operador **JOIN** exige a especificação de um espaço de manobra temporal que descreve a diferença de tempo aceitável entre os eventos associados. Use a função **DATEDIFF** para especificar que os eventos não devem ter um intervalo maior que 15 minutos entre si. Aplique também a função **DATEDIFF** às horas de entrada e saída para calcular o tempo real que um carro gasta na praça de pedágio. Observe a diferença do uso da **DATEDIFF** quando usada em uma instrução **SELECT** em comparação com uma condição **JOIN**.

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. Para testar essa consulta, atualize-a na guia **CONSULTA** do trabalho:
   
    ![Consulta atualizada na guia Consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)
2. Clique em **Testar** e especifique os arquivos de entrada de exemplo para EntryTime e ExitTime.
   
    ![Captura de tela dos arquivos de entrada selecionados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)
3. Marque a caixa de seleção para testar a consulta e exibir a saída:
   
    ![Saída do teste](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3:-report-all-commercial-vehicles-with-expired-registration"></a>Pergunta 3: relatar todos os veículos comerciais com o registro vencido
O Stream Analytics do Azure pode usar instantâneos estáticos de dados para se associar com os fluxos de dados temporais. Para demonstrar essa funcionalidade, use a pergunta de exemplo a seguir.

Se um veículo comercial estiver registrado na empresa de pedágio, ele poderá passar pela cabine sem ser parado para inspeção. Use a tabela de pesquisa de Registro de Veículo Comercial para identificar todos os veículos comerciais com o registro vencido.

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

Para testar uma consulta usando dados de referência, você precisa definir uma fonte de entrada para os dados de referência, o que você já fez.

1. Para testar essa consulta, cole-a na guia **CONSULTA**, clique em **Testar** e especifique as duas fontes da entrada:
   
    ![Captura de tela dos arquivos de entrada selecionados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)
2. Exibir a saída da consulta:
   
    ![Captura de tela da saída da consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## <a name="start-the-stream-analytics-job"></a>Iniciar o trabalho do Stream Analytics
Agora, é hora de concluir a configuração e iniciar o trabalho. Salve a consulta da Pergunta 3, o que produzirá a saída correspondente ao esquema da tabela de saída **TollDataRefJoin** .

Navegue até o **PAINEL** de trabalho e clique em **INICIAR**.

![Captura de tela do botão Iniciar no painel de trabalho](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

Na caixa de diálogo que aparece, altere a hora de **INICIAR SAÍDA** para **HORA PERSONALIZADA**. Altere a hora para uma hora antes da hora atual. Essa alteração assegura que todos os eventos do hub de eventos sejam processados desde que você começou a gerar os eventos no início do tutorial. Agora, clique na marca de seleção para iniciar o trabalho.

![Seleção de hora personalizada](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

O início do trabalho pode levar alguns minutos. Você pode ver o status na página de nível superior do Stream Analytics.

![Captura de tela do status do trabalho](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## <a name="check-results-in-visual-studio"></a>Verificar resultados no Visual Studio
1. Abra o Gerenciador de Servidores do Visual Studio e clique com o botão direito do mouse na tabela **TollDataRefJoin** .
2. Selecione **Mostrar Dados da Tabela** para ver a saída do seu trabalho.
   
    ![Seleção de "Mostrar Dados da Tabela" no Gerenciador de Servidores](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Escalar horizontalmente trabalhos do Stream Analytics do Azure
O Stream Analytics do Azure foi projetado para ser dimensionado de forma elástica, de modo a poder lidar com muitos dados. A consulta do Stream Analytics do Azure pode usar uma cláusula **PARTITION BY** para informar ao sistema que esta etapa será escalável horizontalmente. **PartitionId** é uma coluna especial adicionada pelo sistema e corresponde à ID da partição de entrada (hub de evento).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Interrompa o trabalho atual, atualize a consulta na guia **CONSULTA** e abra a guia **ESCALA**.
   
    **UNIDADES DE STREAMING** definem a quantidade de capacidade de computação que o trabalho pode receber.
2. Mova o controle deslizante para 6.
   
    ![Captura de tela da seleção de 6 unidades de streaming](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)
3. Vá até a guia **SAÍDAS** e altere o nome da tabela SQL para **TollDataTumblingCountPartitioned**.

Se você iniciar o trabalho agora, o Stream Analytics do Azure poderá distribuir o trabalho entre mais recursos de computação e obter uma taxa de transferência melhor. Observe que o aplicativo TollApp também está enviando eventos particionados pelo TollId.

## <a name="monitor"></a>Monitoramento
A guia **MONITORAMENTO** contém estatísticas sobre o trabalho em execução.

![Captura de tela de estatísticas sobre os trabalhos em execução](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

Você pode acessar **Logs de Operação** na guia **PAINEL**.

![Opção "Logs de Operação"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![Captura de tela dos logs de operação, em que você pode ver o status dos trabalhos](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

Para obter informações adicionais sobre um evento específico, selecione o evento e clique no botão **DETALHES** .

![Captura de tela de detalhes sobre um evento selecionado](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## <a name="conclusion"></a>Conclusão
Este tutorial forneceu uma introdução ao serviço Stream Analytics do Azure. Ele demonstrou como configurar entradas e saídas para o trabalho do Stream Analytics. Usando o cenário de Dados de Pedágio, o tutorial explicou os tipos comuns de problemas que podem surgir no espaço de dados em movimento e como eles podem ser resolvidos com consultas simples do tipo SQL no Stream Analytics do Azure. O tutorial descreveu construções de extensão do SQL para trabalhar com os dados temporais. Ele mostrou como combinar transmissões de dados, como aprimorar a transmissão de dados com os dados de referência estáticos e como escalar horizontalmente uma consulta para aumentar a taxa de transferência.

Embora este tutorial forneça uma boa introdução, ele não é, de forma alguma, completo. Você pode encontrar mais padrões de consulta usando a linguagem SAQL em [Exemplos de consulta para padrões de uso do Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).
Consulte a [documentação online](https://azure.microsoft.com/documentation/services/stream-analytics/) para saber mais sobre o Stream Analytics do Azure.

## <a name="clean-up-your-azure-account"></a>Limpar sua conta do Azure
1. Interrompa o trabalho do Stream Analytics do portal do Azure.
   
    O script Setup.ps1 cria dois hubs de eventos e um banco de dados SQL. As instruções a seguir ajudarão você a limpar os recursos no final do tutorial.
2. Em uma janela do PowerShell, digite **.\\Cleanup.ps1** Isso iniciará o script que exclui os recursos usados no tutorial.
   
   > [!NOTE]
   > Os recursos são identificados pelo nome. Certifique-se de examinar cuidadosamente cada item antes de confirmar a remoção.
   > 
   > 
   
    ![Captura de tela do processo de limpeza](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)

<!--HONumber=Oct16_HO2-->


