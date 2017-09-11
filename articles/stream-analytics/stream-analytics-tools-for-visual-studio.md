---
title: Usar Ferramentas do Stream Analytics do Azure para Visual Studio | Microsoft Docs
description: "Tutorial de introdução para as Ferramentas do Stream Analytics do Azure para Visual Studio"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: samacha
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 
ms.author: samacha
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 618c1055795a75e0ed71dacddba3e076f81f4946
ms.contentlocale: pt-br
ms.lasthandoff: 07/04/2017

---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Usar Ferramentas do Stream Analytics do Azure para Visual Studio
## <a name="introduction"></a>Introdução
Neste tutorial, você aprenderá como usar as Ferramentas do Stream Analytics do Azure para Visual Studio para criar, testar localmente, gerenciar e depurar os trabalhos do Stream Analytics. 

Depois de concluir este tutorial, você poderá:
* Familiarize-se com as Ferramentas do Stream Analytics para Visual Studio.
* Configurar e implantar um trabalho do Stream Analytics.
* Testar seu trabalho local com os dados de exemplo locais.
* Use o monitoramento para solucionar problemas.
* Exportar os trabalhos existentes para projetos.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará dos seguintes pré-requisitos:
* Conclua as etapas anteriores a "Criar um trabalho de Stream Analytics" no [tutorial Criar uma solução de IoT usando o Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Use o Visual Studio 2015, Visual Studio 2013 Update 4 ou Visual Studio 2012. As edições Enterprise (Ultimate/Premium), Professional, Community têm suporte. Não há suporte para a Edição Express. O Visual Studio 2017 não tem suporte. 
* Use o SDK do Azure para .NET versão 2.7.1 ou posterior. Instale-o usando o [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
* Instale as [Ferramentas do Stream Analytics para Visual Studio](http://aka.ms/asatoolsvs).

## <a name="create-a-stream-analytics-project"></a>Criar um projeto do Stream Analytics
1. No Visual Studio, clique no menu **Arquivo** e selecione **Novo Projeto**. 

2. Na lista de modelos à esquerda, selecione **Stream Analytics** e clique em **Aplicativo do Stream Analytics do Azure**.

3. Insira o **Nome** do projeto, a **Localização** e o **Nome da solução** na parte inferior, como faria para outros projetos.

    ![Janela Novo Projeto](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

    Um projeto **Pedágio** é gerado no **Gerenciador de Soluções**.

    ![Projeto Pedágio gerado no Gerenciador de Soluções](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Escolha a assinatura correta
1. No Visual Studio, clique em **Exibir** no menu e abra **Gerenciador de Servidores**.

2. Entre com sua conta do Azure. 

## <a name="define-the-input-sources"></a>Definir as fontes de entrada
1.  Em **Gerenciador de Soluções**, expanda o nó **Entradas** e renomeie **Input.json** para **EntryStream.json**. Clique duas vezes em **EntryStream.json**.
2.  O **Alias de Entrada** agora é **EntryStream**. O alias de entrada é usado no script de consulta. 
3.  Em **Tipo de Origem**, selecione **Fluxo de Dados**.
4.  Em **Origem**, selecione **Hub de Eventos**.
5.  No **Namespace do Barramento de Serviço**, selecione a opção **TollData**.
6.  Em **Nome do Hub de Eventos**, selecione **entrada**.
7.  Em **Nome da Política do Hub de Eventos**, selecione é **RootManageSharedAccessKey** (o valor padrão).
8.  Em **Formato de Serialização de Evento**, selecione **Json**. 
9.  Em **Codificação**, selecione **UTF8**. Suas configurações devem ter aparência semelhante à captura de tela a seguir:

    ![Janela Entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
 
10. Para concluir o assistente, clique em **Salvar**. Agora você pode adicionar outra fonte de entrada para criar o fluxo de saída. Clique com o botão direito do mouse no nó **Entradas** e selecione **Novo Item**.

    ![Novo Item](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
 
11. Na janela exibida, selecione **Entrada do Stream Analytics do Azure** e altere o **Nome** para **ExitStream.json**. Clique em **Adicionar**.

    ![Janela Adicionar Novo Item](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
 
12. Clique duas vezes em **ExitStream.json** no projeto e siga as mesmas etapas usadas para o fluxo de entrada. Lembre-se de inserir **saída** para o **Nome do Hub de Eventos**, conforme mostrado na captura de tela a seguir:

    ![Janela ExitStream](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)

    Agora você definiu dois fluxos de entrada:

    ![Fluxos de recebimento de entrada e saída](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
 
    Em seguida, adicione a entrada de dados de referência para o arquivo de blob que contém os dados de registro do carro.

13. Clique com botão direito do mouse no nó **Entradas** no projeto e, em seguida, siga as mesmas etapas usadas para as entradas de fluxo. Em **Alias de Entrada**, digite **Registro** e em **Tipo de Origem**, selecione **Dados de referência**.

    ![Janela Registro](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)

14. Em **Conta de Armazenamento**, selecione a opção **tolldata**. Em **Contêiner**, selecione **tolldata** e, em **Padrão de Caminho**, digite **registration.json**. Este nome de arquivo diferencia maiúsculas de minúsculas e deve estar em minúsculas.
15. Para concluir o assistente, clique em **Salvar**.

Agora, todas as entradas estão definidas.

## <a name="define-the-output"></a>Definir a saída
1.  No **Gerenciador de Soluções**, expanda o nó **Entradas** e clique duas vezes em **Output.json**.

2.  Em **Alias de Saída**, digite **saída**. 
3.  Em **Coletor**, selecione **Banco de Dados SQL**.
4.  Em **Banco de Dados**, selecione **TollDataDB**.
5.  Em **Nome de Usuário**, digite **tolladmin**. 
6.  Em **Senha**, digite **123toll!**.
7.  Em **Tabela**, digite **TollDataRefJoin**.
8.  Clique em **Salvar**.

    ![Janela de Saída](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="create-a-stream-analytics-query"></a>Criar uma consulta do Stream Analytics
Este tutorial tenta responder várias perguntas comerciais relacionadas a dados de pedágio. Ele também constrói consultas do Stream Analytics que podem ser usadas no Stream Analytics para fornecer respostas relevantes.
Antes de você começar seu primeiro trabalho do Stream Analytics, exploraremos um cenário simples e a sintaxe de consulta.

### <a name="introduction-to-the-stream-analytics-query-language"></a>Introdução à linguagem de consulta do Stream Analytics
Digamos que você precise contar o número de veículos que entram em uma cabine de pedágio. Como este exemplo se trata de um fluxo contínuo de eventos, você precisa definir um período de tempo. Modifique a pergunta para "Quantos veículos entram em uma cabine de pedágio a cada três minutos?" Esse modo de contar dados é conhecido como contagem em cascata.

Veja a consulta do Stream Analytics que responde a essa pergunta:

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

O Stream Analytics usa uma linguagem de consulta parecida com SQL e adiciona algumas extensões para especificar aspectos da consulta relacionados ao tempo.

Para obter mais informações, veja as construções de [Gerenciamento de Tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e de [Janela](https://msdn.microsoft.com/library/azure/dn835019.aspx) usadas na consulta no MSDN.

Agora que você escreveu sua primeira consulta do Stream Analytics, é hora de testá-la. Use os arquivos de dados de exemplo localizados na pasta TollApp no seguinte caminho:

..\TollApp\TollApp\Data

Esta pasta contém os seguintes arquivos:
*   Entry.json
*   Exit.JSON
*   registration.json

## <a name="count-the-number-of-vehicles-entering-a-toll-booth"></a>Contar o número de veículos que entram em uma cabine de pedágio
No projeto, clique duas vezes em **Script.asaql** para abrir o script no **Editor de Consultas**. Copie e cole o script na seção anterior no editor. O Editor de Consultas dá suporte ao IntelliSense, a uso de cores de sintaxe e ao marcador de erro.

![Editor de Consultas](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>Testar as consultas do Stream Analytics localmente

1. Para compilar a consulta para ver se há algum erro de sintaxe, clique com o botão direito do mouse no projeto e selecione **Compilar**. 

2. Para validar essa consulta em relação aos dados de exemplo, você pode usar dados de exemplo locais. Clique com o botão direito do mouse na entrada e selecione **Adicionar entrada local**.

    ![Adicionar entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
 
3. Na janela pop-up, selecione os dados de exemplo do caminho local. Clique em **Salvar**.

    ![Janela Adicionar entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
 
    Um arquivo chamado **local_EntryStream.json** é adicionado automaticamente à pasta de entradas.

    ![Arquivo adicionado à pasta de entradas](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
 
4. No **Editor de Consultas**, clique em **Executar Localmente**. Ou você pode pressionar a tecla F5.

    ![Executar Localmente](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)

    ![Saída de execução local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)

    Pressione qualquer tecla para exibir a saída na janela **Resultado da Execução Local de ASA** no Visual Studio. 

    ![Janela Resultado da Execução Local de ASA](./media/stream-analytics-tools-for-vs/local-testing-output.png)

5. Clique em **Abrir a Pasta de Resultados** para verificar os arquivos de saída nos formatos CSV e JSON.

    ![Saída de Abrir a Pasta de Resultados](./media/stream-analytics-tools-for-vs/local-testing-files.png)
 

### <a name="sample-the-input-data"></a>Amostrar os dados de entrada
Você também pode realizar a amostragem de dados de entrada de fontes de entrada para um arquivo local. 
1. Clique com o botão direito do mouse no arquivo de configuração de entrada e selecione **Dados de Exemplo**. 

   ![Dados de exemplo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

    Você pode fazer amostragem apenas do Hub de Eventos ou do Hub IoT, por enquanto. Não há suporte para outras fontes de entrada.

2. Na janela pop-up, digite o caminho local usado para salvar os dados de exemplo. Clique em **Exemplo**.

    ![Janela Dados de Exemplo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
    Você pode ver o andamento na janela de **Saída**. 

    ![Janela de Saída](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Enviar uma consulta do Stream Analytics para o Azure
1. No **Editor de Consultas**, clique em **Enviar para o Azure** no editor de scripts.

    ![Enviar para o Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. Selecione **Criar um Novo Trabalho do Stream Analytics do Azure**. Insira o **Nome do Trabalho** e selecione a **Assinatura** correta. Clique em **Enviar**.

    ![Janela Enviar Trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-a-job"></a>Iniciar um trabalho
Agora que o trabalho foi criado, a exibição de trabalho é aberta automaticamente. 
1. Para iniciar o trabalho, clique no botão com a **seta verde**.

    ![Iniciar um trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. Selecione a configuração padrão e clique em **Iniciar**.
 
    ![Janela Iniciar Trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

    O **Status** do trabalho é alterado para **Em Execução** e **Eventos de Entrada** e **Eventos de Saída** aparecem.

    ![Status Em Execução no Resumo do Trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-the-results-in-visual-studio"></a>Verificar os resultados no Visual Studio
1. No Visual Studio, abra o **Gerenciador de Servidores** e clique com o botão direito do mouse na tabela **TollDataRefJoin**.
2. Selecione **Mostrar Dados da Tabela** para ver a saída do seu trabalho.
   
    ![Seleção de Mostrar Dados da Tabela no Gerenciador de Servidores](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)


### <a name="view-the-job-metrics"></a>Exibir as métricas de trabalho
Algumas estatísticas básicas de trabalho podem ser encontradas em **Métricas do Trabalho**. 

![Janela Métricas de Trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>Listar o trabalho no Gerenciador de Servidores
No **Gerenciador de Servidores**, clique em **Trabalhos do Stream Analytics** e clique em **Atualizar**. O trabalho é exibido em **Trabalhos do Stream Analytics**.

![Trabalhos do Stream Analytics listados no Gerenciador de Servidores](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>Abrir a exibição do trabalho
Para abrir a exibição de trabalho, expanda o nó do trabalho e clique duas vezes no nó **Exibição de Trabalho**.

![Nó Exibição de Trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Exportar um trabalho existente para um projeto
Há duas maneiras que você pode usar para exportar um trabalho para um projeto.

No **Gerenciador de Servidores**, clique com o botão direito do mouse no nó de trabalho no nó **Trabalhos do Stream Analytics** e selecione **Exportar para um Novo Projeto do Stream Analytics**.

![Exportar para um novo projeto do Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)

O projeto é gerado no **Gerenciador de Soluções**.

![Projeto gerado no Gerenciador de Soluções](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
 
Você também pode usar a exibição de trabalho e clicar em **Gerar Projeto**.

![Gerar Projeto](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos
 
- Não há suporte para a saída do Power BI e a saída do Azure Date Lake Store.
- Não há suporte do editor para adicionar ou alterar funções definidas pelo usuário de JavaScript.

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

