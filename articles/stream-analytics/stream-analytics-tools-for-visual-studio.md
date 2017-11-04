---
title: Usar ferramentas do Azure Stream Analytics para Visual Studio | Microsoft Docs
description: "Tutorial de introdução para as Ferramentas do Stream Analytics do Azure para Visual Studio"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: su-jie
manager: jhubbard
editor: cgronlun
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: sujie
ms.openlocfilehash: 8e3f1ae6739896dfd1329561dbcede38a6069546
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2017
---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Usar ferramentas do Azure Stream Analytics para Visual Studio
As Ferramentas do Stream Analytics do Azure para Visual Studio agora estão disponíveis para o público geral. Essas ferramentas permitem uma experiência mais avançada para os usuários do Stream Analytics solucionarem problemas, bem como escreverem consultas complexas e até mesmo escreverem consultas localmente. Você também pode exportar um trabalho do Stream Analytics em um projeto do Visual Studio.

## <a name="introduction"></a>Introdução
Neste tutorial, você aprenderá a usar as ferramentas do Stream Analytics para Visual Studio para criar, testar localmente, gerenciar e depurar os trabalhos do Stream Analytics. 

Depois de concluir este tutorial, você poderá:

* Familiarize-se com as ferramentas do Stream Analytics para Visual Studio.
* Configurar e implantar um trabalho do Stream Analytics.
* Testar seu trabalho local com os dados de exemplo locais.
* Usar o monitoramento para solucionar problemas.
* Exportar os trabalhos existentes para projetos.

## <a name="prerequisites"></a>Pré-requisitos
Você precisará dos seguintes pré-requisitos para concluir este tutorial:

* Conclua as etapas até "Criar um trabalho de Stream Analytics" no tutorial [Criar uma solução de IoT usando o Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Instale o Visual Studio 2017, o Visual Studio 2015, o Visual Studio 2013 Update 4. As edições Enterprise (Ultimate/Premium), Professional, Community têm suporte. Não há suporte para a Edição Express. 
* Siga as [instruções de instalação](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) para instalar as ferramentas do Stream Analytics para o Visual Studio.

## <a name="create-a-stream-analytics-project"></a>Criar um projeto do Stream Analytics
No Visual Studio, selecione **Arquivo** > **Novo Projeto**. Na lista de modelos à esquerda, selecione **Stream Analytics** e então selecione **Aplicativo do Stream Analytics do Azure**.
Na parte inferior da página, insira **Nome**  do projeto, **Localização** e **Nome da solução** como faria para outros projetos.

![Criação de novo projeto](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

O projeto **Pedágio** é gerado no **Gerenciador de Soluções**.

![Projeto de Pedágio no Gerenciador de Soluções](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Escolha a assinatura correta
1. No menu **Exibir**, selecione **Gerenciador de Servidores** no Visual Studio.

2. Faça logon com sua conta do Azure. 

## <a name="define-input-sources"></a>Definir fontes de entrada
1. Em **Gerenciador de Soluções**, expanda o nó **Entradas** e renomeie **Input.json** para **EntryStream.json**. Clique duas vezes em **EntryStream.json**.

2. Para **Alias de Entrada**, insira **EntryStream**. Observe que o alias de entrada é usado no script de consulta.

3. Para **Tipo de Fonte**, selecione **Fluxo de Dados**.

4. Para **Origem**, selecione **Hub de Eventos**.

5. Para **Namespace do Barramento de Serviço**, selecione a opção **TollData** na lista suspensa.

6. Para **Nome do Hub de Eventos**, selecione **entrada**.

7. Para **Nome da Política do Hub de Eventos**, selecione **RootManageSharedAccessKey** (o valor padrão).

8. Para **Formato de Serialização de Eventos**, selecione **Json** e, para **Codificação**, selecione **UTF8**.
   
   Sua configuração se parecerá com esta:
   
   ![Configurações de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9. Na parte inferior da página, selecione **Salvar** para concluir o assistente. Agora você pode adicionar outra fonte de entrada para criar o fluxo de saída. Clique com o botão direito do mouse no nó **Entradas** e selecione **Novo Item**.
   
   ![Novo Item](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. Na janela pop-up, escolha **Entrada do Stream Analytics** e altere o **Nome** para **ExitStream.json**. Selecione **Adicionar**.
   
    ![Adicionar Novo Item](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. Clique duas vezes em **ExitStream.json** no projeto e siga as mesmas etapas do fluxo de entrada para preencher os campos. Para o **Nome do Hub de Eventos**, Lembre-se de inserir **saída** conforme mostrado na captura de tela a seguir:
   
    ![Configurações de ExitStream](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   Agora você definiu dois fluxos de entrada.
   
   ![Dois fluxos de entrada](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   Em seguida, adicione a entrada de dados de referência para o arquivo de blob que contém os dados de registro do carro.
   
12. Clique com botão direito do mouse no nó **Entradas** no projeto e então siga o mesmo processo para as entradas de fluxo. Para **Tipo de Origem**, selecione **Dados de referência** e, para **Alias de Entrada**, insira **Registro**.
   
    ![Configurações de registro](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. Selecione a conta de **Armazenamento** que contém a opção com **TollData**. O nome do contêiner é **TollData** e o **Padrão de Caminho** é **registration.json**. Este nome de arquivo diferencia maiúsculas de minúsculas e deve estar em minúsculas.

14. Selecione **Salvar** para concluir o assistente.

Agora, todas as entradas estão definidas.

## <a name="define-output"></a>Definir saída
1. No **Gerenciador de Soluções**, expanda o nó **Entradas** e clique duas vezes em **Output.json**.

2. Para **Alias de Saída**, digite **saída**. Para **Coletor**, selecione **Banco de Dados SQL**.

3. Para o nome do **Banco de Dados**, insira **TollDataDB**.

4. Para **Nome de Usuário**, insira **tolladmin**. Para **Senha**, insira **123toll!**. Para **Tabela**, insira **TollDataRefJoin**.

5. Selecione **Salvar**.

   ![Configurações de saída](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="stream-analytics-query"></a>Consulta do Stream Analytics
Este tutorial tenta responder várias perguntas comerciais relacionadas a dados de pedágio. Nós criamos consultas do Stream Analytics que podem ser usadas no Stream Analytics para fornecer respostas relevantes. Antes de você começar seu primeiro trabalho do Stream Analytics, exploraremos um cenário simples e a sintaxe de consulta.

### <a name="introduction-to-stream-analytics-query-language"></a>Introdução à linguagem de consulta do Stream Analytics
Digamos que você precise contar o número de veículos que entram em uma cabine de pedágio. Como esse fluxo de eventos é contínuo, é necessário definir um período de tempo. Vamos modificar a pergunta para "Quantos veículos entram em uma cabine de pedágio a cada três minutos?" Essa medida é conhecida como contagem em cascata.

Veja a consulta do Stream Analytics que responde a essa pergunta:

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Como você pode ver, o Stream Analytics usa uma linguagem de consulta como SQL. Ele adiciona algumas extensões para especificar aspectos relacionados ao tempo da consulta.

Para obter mais detalhes, leia sobre [gerenciamento de tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e construções de [janelas](https://msdn.microsoft.com/library/azure/dn835019.aspx) usadas na consulta no MSDN.

Agora que você escreveu sua primeira consulta do Stream Analytics, teste-a usando os arquivos de dados de exemplo localizados na pasta TollApp no caminho a seguir:

**..\TollApp\TollApp\Data**

Esta pasta contém os seguintes arquivos:

* Entry.json
* Exit.JSON
* registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>Pergunta: número de veículos que entram em uma cabine de pedágio
No projeto, clique duas vezes em **Script.asaql** para abrir o script no editor. Cole o script na seção anterior no editor. O editor de consulta dá suporte ao IntelliSense, a cores de sintaxe e a um marcador de erro.

![Editor de consultas](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>Testar as consultas do Stream Analytics localmente
Você pode primeiro compilar a consulta para ver se há algum erro de sintaxe.

1. Para validar essa consulta em relação aos dados de exemplo, use dados de exemplo locais clicando com o botão direito do mouse na entrada e selecionando **Adicionar entrada local**.
   
   ![Adicionar entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
2. Na janela pop-up, selecione os dados de exemplo do caminho local. Selecione **Salvar**.
   
   ![Adicionar entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   Um arquivo chamado **local_EntryStream.json** é adicionado automaticamente à pasta de entradas.
   
   ![Lista de arquivos da pasta de entrada local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. Selecione **Executar Localmente** no editor de consultas. Ou você pode pressionar F5.
   
   ![Executar Localmente](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   Você pode encontrar o caminho de saída da saída do console. Pressione qualquer tecla para abrir a pasta de resultados.
   
   ![Execução local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. Verifique os resultados na pasta local.
   
   ![Resultado de pasta local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>Entrada de exemplo
Você também pode realizar a amostragem de dados de entrada de fontes de entrada para o arquivo local. Clique com o botão direito do mouse no arquivo de configuração de entrada e selecione **Dados de Exemplo**. 

![Dados de exemplo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

Observe que você só pode criar amostras de hubs de eventos ou de hubs IoT por enquanto. Não há suporte para outras fontes de entrada. Na caixa de diálogo pop-up, preencha o caminho local para salvar os dados de exemplo. Selecione **Exemplo**.

![Configuração de dados de exemplo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
Você pode ver o andamento na janela de **Saída**. 

![Saída de dados de exemplo](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Enviar uma consulta do Stream Analytics para o Azure
1. No **Editor de Consultas**, selecione **Enviar para o Azure** no editor de scripts.

   ![Enviar para o Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. Selecione **Criar um Novo Trabalho do Stream Analytics do Azure**. Para **Nome do Trabalho**, insira **TollApp**. Escolha a **Assinatura** correta na lista suspensa. Selecione **Enviar**.

   ![Enviar Trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-the-job"></a>Iniciar o trabalho
Agora que o trabalho foi criado, a exibição do trabalho abrirá automaticamente. 
1. Selecione o botão de seta verde para iniciar o trabalho.

   ![Botão Iniciar trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. Escolha a configuração padrão e selecione **Iniciar**.
 
   ![Iniciar trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

   Você pode ver que o status do trabalho foi alterado para **Em execução** e há eventos de entrada/saída.

   ![Resumo e Métricas de Trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Verificar resultados no Visual Studio
1. Abra o Gerenciador de Servidores do Visual Studio e clique com o botão direito do mouse na tabela **TollDataRefJoin** .

2. Selecione **Mostrar Dados da Tabela** para ver a saída do seu trabalho.
   
   ![Mostrar Dados da Tabela](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>Exibir métricas do trabalho
Algumas estatísticas básicas de trabalho são mostradas em **Métricas de Trabalho**. 

![Métricas do Trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>Listar o trabalho no Gerenciador de Servidores
No **Gerenciador de Servidores**, selecione **Trabalhos do Stream Analytics** e, em seguida, selecione **Atualizar**. Seu trabalho é exibido em **Trabalhos do Stream Analytics**.

![Lista de trabalhos](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>Abrir a exibição do trabalho
Expanda o nó de trabalho e clique duas vezes no nó **Exibição de Trabalho** para abrir uma exibição do trabalho.

![Exibição de Trabalho](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Exportar um trabalho existente para um projeto
Há duas maneiras que você pode usar para exportar um trabalho para um projeto.
* No **Gerenciador de Servidores**, no nó **Trabalhos do Stream Analytics**, clique com o botão direito do mouse no nó do trabalho. Selecione **Exportar para Novo Projeto do Stream Analytics**.
   
   ![Exportar para um novo projeto do Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   O projeto gerado aparece no **Gerenciador de Soluções**.
   
    ![Trabalho do Gerenciador de Soluções](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
* Na exibição de trabalho, selecione **Gerar Projeto**.
   
   ![Gerar Projeto](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos
 
* O teste local não funcionará se a consulta tiver funções geoespaciais.
* Não há suporte do editor disponível para adicionar ou alterar a UDF do JavaScript.
* O teste local não dá suporte ao salvamento da saída no formato JSON. 
* O suporte não está disponível para a saída do Power BI e a saída do ADLS.



## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do gerenciamento do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


