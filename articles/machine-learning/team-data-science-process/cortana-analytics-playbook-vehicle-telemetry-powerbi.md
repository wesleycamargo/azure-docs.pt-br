---
title: "Painel do Power BI sobre a integridade do veículo e hábitos de condução - Azure | Microsoft Docs"
description: "Use os recursos do Cortana Intelligence para obter informações preditivas em tempo real sobre a integridade do veículo e hábitos de condução."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: bradsev
ms.openlocfilehash: 626987ec0648f9e770499b4a48bc4ca2d175d2b4
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2017
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Instruções de instalação do Painel de Power BI do modelo de solução de análise de telemetria do veículo
Este menu fornece links para os capítulos deste manual: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

A solução Análise de Telemetria do Veículo demonstra como revendedores de carro, fabricantes de automóveis e seguradoras podem usar os recursos do Cortana Intelligence. Eles podem se aprofundar e receber insights preditivos em tempo real sobre a integridade do veículo e os hábitos de condução para melhorar o desenvolvimento, a pesquisa e a experiência do cliente e as campanhas de marketing. Estas instruções passo a passo mostram como você pode configurar os relatórios e painel do Power BI depois de implantar a solução na sua assinatura. 

## <a name="prerequisites"></a>Pré-requisitos
* Implante a solução [Análise de Telemetria do Veículo](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90). 
* Instalar o [Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331).
* Obtenha uma [assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/). Se você não tiver uma assinatura do Azure, comece com a assinatura gratuita do Azure.
* Abra uma conta do Power BI.

## <a name="cortana-intelligence-suite-components"></a>Componentes do Cortana Intelligence Suite
Como parte do modelo Solução de Análise de Telemetria de Veículo, os serviços do Cortana Intelligence abaixo são implantados em sua assinatura:

* Os **Hubs de Eventos do Azure** transmitem milhões de eventos de telemetria do veículo para o Azure.
* O **Azure Stream Analytics** fornece insights em tempo real sobre a integridade do veículo e persiste esses dados no armazenamento de longo prazo para uma análise em lote mais avançada.
* O **Azure Machine Learning** detecta anomalias em tempo real e usa o processamento em lotes para fornecer insights preditivos.
* O **Azure HDInsight** transforma os dados em larga escala.
* O **Azure Data Factory** cuida da orquestração, do agendamento, do gerenciamento de recursos e do monitoramento do pipeline do processamento em lotes.

**PowerBI** fornece essa solução a um painel avançado para os dados em tempo real e as visualizações da análise preditiva. 

A solução usa duas fontes de dados diferentes:

* Sinais de veículo simulados e conjuntos de dados de diagnóstico
* Catálogo do veículo

Um simulador de telemática do veículo é incluído como parte desta solução. Ele emite informações de diagnóstico e sinais que correspondem ao estado do veículo e ao padrão de condução em determinado momento. 

O catálogo de veículos é um conjunto de dados de referência que mapeia VINs para modelos.

## <a name="power-bi-dashboard-preparation"></a>Preparação do painel do Power BI
### <a name="set-up-the-power-bi-real-time-dashboard"></a>Instalar o Painel do Power BI em tempo real

#### <a name="start-the-real-time-dashboard-application"></a>Iniciar o aplicativo do painel em tempo real
Depois que a implantação for concluída, siga as instruções de operação manual.

1. Baixe o aplicativo de painel em tempo real RealtimeDashboardApp.zip e descompacte-o.

2.  Na pasta descompactada, abra o arquivo de configuração de aplicativo RealtimeDashboardApp.exe.config. Substitua appSettings para os Hubs de Eventos, o armazenamento de Blobs do Azure e as conexões do serviço Azure Machine Learning pelos valores nas instruções de operação manual. Salve suas alterações.

3. Execute o aplicativo RealtimeDashboardApp.exe. Na janela de logon, insira suas credenciais do Power BI válidas. 

   ![Janela de logon do Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
4. Selecione **Aceitar**. O aplicativo começa a ser executado.

   ![Permissões do painel do Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

5. Entre no site do Power BI e crie um painel em tempo real.

Agora você está pronto para configurar o painel do Power BI.  

### <a name="configure-power-bi-reports"></a>Configurar relatórios do Power BI
Os relatórios em tempo real e o painel demoram cerca de 30 a 45 minutos para serem concluídos. 

1. Navegue até a página do [Power BI](http://powerbi.com) e entre.

    ![Página de entrada do Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

2. Um novo conjunto de dados é gerado no Power BI. Selecione o conjunto de dados **ConnectedCarsRealtime**.

    ![Conjunto de dados ConnectedCarsRealtime](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

3. Para salvar o relatório em branco, pressione Ctrl+S.

    ![Relatório em branco](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

4. Insira o nome de relatório **Análise de Telemetria do Veículo em tempo real - Relatórios**.

    ![Nome do relatório](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Relatórios em tempo real
Há três relatórios em tempo real nesta solução:

* Veículos em operação
* Veículos que precisam de manutenção
* Estatísticas de integridade do veículo

Você pode configurar os três relatórios em tempo real ou pode parar depois de qualquer estágio. Você pode continuar na próxima seção sobre como configurar relatórios em lote. Recomendamos que você crie todos os três relatórios para visualizar os insights completos do caminho da solução em tempo real.  

### <a name="vehicles-in-operation-report"></a>Relatório Veículos em operação
1. Clique duas vezes na **Página 1** e a renomeie como **Veículos em operação**.

    ![Veículos em operação](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

2. Na guia **Campos**, selecione **vin**. Na guia **Visualizações**, selecione a visualização **Cartão**.  

    A visualização **Cartão** é criada, conforme mostrado na figura abaixo:

    ![Selecionar vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

3. Selecione a área em branco para adicionar uma nova visualização.  

4. Na guia **Campos**, selecione **cidade** e **vin**. Na guia **Visualizações**, selecione a visualização **Mapa**. Arraste **vin** para a área **Valores**. Arraste **city** para a área **Legenda**. 

    ![Visualização de cartão](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

5. Na guia **Visualizações**, selecione a seção **Formatar**. Selecione **título**e altere **Texto** para **Veículos em operação por cidade**.

    ![Veículos em operação por cidade](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    A visualização final é semelhante ao exemplo abaixo:

    ![Visualização final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

6. Selecione a área em branco para adicionar uma nova visualização.  

7. Na guia **Campos**, selecione **cidade** e **vin**. Na guia **Visualizações**, selecione a visualização **Gráfico de Coluna em Cluster**. Arraste **city** para a área **Eixo**. Arraste **vin** para a área **Valor**.

8. Classifique o gráfico por **Contagem de vin**.

    ![Contagem de vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

9. Altere o **Título** do gráfico para **Veículos em operação por cidade**. 

10. Selecione a seção **Formatar**e selecione **Cores dos Dados**. Altere **Mostrar Tudo** para **Ativado**.

    ![Cores dos dados](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

11. Altere a cor de uma cidade individual selecionando o símbolo de cor.

    ![Alteração de cor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

12. Selecione a área em branco para adicionar uma nova visualização.  

13. Na guia **Visualizações**, selecione a visualização **Gráfico de Coluna em Cluster**. Na guia **Campos**, arraste **city** para a área **Eixo**. Arraste **Model** para a área **Legenda**. Arraste **vin** para a área **Valor**.

    ![Gráfico de colunas em cluster](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    O gráfico se parece com a seguinte imagem:

    ![Renderização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

14. Reorganize todas as visualizações para que a página fique semelhante ao exemplo abaixo:

    ![Painel com visualizações](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Você configurou com êxito o relatório em tempo real "Veículos em operação". Você pode criar o próximo relatório em tempo real ou parar aqui e configurar o painel. 

### <a name="vehicles-requiring-maintenance-report"></a>Relatório Veículos que precisam de manutenção

1. Selecione ![Adicionar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) para adicionar um novo relatório. Dê um novo nome, **Veículos que precisam de manutenção**.

    ![Veículos que precisam de manutenção](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

2. Na guia **Campos**, selecione **vin**. Na guia **Visualizações**, selecione a visualização **Cartão**.

    ![Visualização de placa VIN](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    O conjunto de dados contém um campo chamado **MaintenanceLabel**. Esse campo pode ter um valor "0" ou "1". O valor é definido pelo modelo de aprendizado de máquina provisionado como parte da solução. Ele está integrado ao caminho em tempo real. O valor "1" indica que um veículo requer manutenção. 

3. Para adicionar um **filtro de nível de página** a fim de mostrar os dados dos veículos que exigem manutenção: 

   a. Arraste o campo **MaintenanceLabel** para os **Filtros de Nível de Página**.
  
      ![Filtros em nível de página](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    b. Na parte inferior de **MaintenanceLabel de filtros de nível de página**, selecione **Filtragem Básica**.

      ![Filtragem básica](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    c. Defina o valor de filtro como **1**.

      ![Valor do filtro](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

4. Selecione a área em branco para adicionar uma nova visualização.  

5. Na guia **Visualizações**, selecione a visualização **Gráfico de Coluna em Cluster**. 

    ![Placa VIN](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![Gráfico de colunas em cluster](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

6. Na guia **Campos**, arraste **Model** para a área **Eixo**. Arraste **vin** para a área **Valor**. Em seguida, classifique a visualização por **Contagem de vin**. Altere o **Título** do gráfico para **Veículos que exigem manutenção por modelo**. 

7. Na seção **Campos** ![Campos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) da guia **Visualizações**, arraste **vin** para **Saturação de Cor**.

    ![Saturação de cor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

8. Na seção **Formatar**, altere **Cores dos Dados** na visualização: 

    a. Altere a cor do **Mínimo** para **F2C812**.

    b. Altere a cor do **Máximo** para **FF6300**.

    ![Novas cores dos dados](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    As novas cores de visualização ficam semelhantes ao seguinte exemplo:

    ![Novas cores de visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

9. Selecione a área em branco para adicionar uma nova visualização.  

10. Na guia **Visualizações**, selecione **Gráfico de Coluna em Cluster**. Arraste **vin** para a área **Valor**. Arraste **city** para a área **Eixo**. Classifique o gráfico por **Contagem de vin**. Altere o **Título** do gráfico para **Veículos que exigem manutenção por cidade**.

    ![Veículos que precisam de manutenção por cidade](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

11. Selecione a área em branco para adicionar uma nova visualização.  

12. Na guia **Visualizações**, selecione a visualização **Cartão multilinhas**. Arraste **Model** e **vin** para a área **Campos**.

    ![Cartão multilinhas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

13. Reorganize todas as visualizações para que o relatório final fique semelhante ao exemplo abaixo: 

    ![Relatório final reorganizado](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Você configurou com êxito o relatório em tempo real "Veículos que precisam de manutenção". Você pode criar o próximo relatório em tempo real ou parar aqui e configurar o painel. 

### <a name="vehicle-health-statistics-report"></a>Relatório Estatísticas de Integridade do Veículo

1. Selecione ![Adicionar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) para adicionar um novo relatório. Dê um novo nome, **Estatísticas de integridade de veículos**. 

2. Na guia **Visualizações**, selecione a visualização **Medidor**. Arraste **speed** para as áreas **Valor**, **Valor Mínimo** e **Valor Máximo**.

   ![Estatísticas de integridade de veículos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

3. Altere a agregação padrão de **speed** na área **Valor** para **Média**.

4. Altere a agregação padrão de **speed** na área **Valor Mínimo** para **Mínima**.

5. Altere a agregação padrão de **speed** na área **Valor Máximo** para **Máxima**.

   ![Valores de velocidade](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

6. Renomeie o **Título do Medidor** para **Velocidade média**.

   ![Medidor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

7. Selecione a área em branco para adicionar uma nova visualização.  

    Da mesma forma, adicione um **Medidor** para **Média de óleo no motor**, **Média de combustível** e **Temperatura média do motor**.  

8. Altere a agregação padrão dos campos em cada medidor como você fez nas etapas acima, no medidor **Velocidade média**.

    ![Medidores adicionais](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

9. Selecione a área em branco para adicionar uma nova visualização.

10. Na guia **Visualizações**, selecione a visualização **Gráfico de Linha e Coluna em Cluster**. Arraste **cidade** para **Eixo Compartilhado**. Arraste **tirepressure**, **engineoil** e **speed** para a área **Valores de Coluna**. Altere o tipo de agregação para **Média**. 

11. Arraste **engineTemperature** para a área **Valores de Linha**. Altere o tipo de agregação para **Média**. 

    ![Valores de linha e coluna](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

12. Altere o **Título** do gráfico para **Velocidade média, pressão do pneu, óleo do motor e temperatura do motor**.  

    ![Título do gráfico de linha e coluna clusterizado](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

13. Selecione a área em branco para adicionar uma nova visualização.

14. Na guia **Visualizações**, selecione a visualização **Mapa de Árvore**. Arraste **Model** para a área **Grupo**. Arraste **MaintenanceProbability** para a área **Valores**.

15. Altere o **Título do gráfico** para **Modelos de veículo que exigem manutenção**.

    ![Título do mapa de árvore](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

16. Selecione a área em branco para adicionar uma nova visualização.

17. Na guia **Visualizações**, selecione a visualização **Gráfico de Barras Empilhadas 100%**. Arraste **city** para a área **Eixo**. Arraste **MaintenanceProbability** e **RecallProbability** para a área **Valor**.

    ![Áreas de eixo e valor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

18. Na seção **Formatar**, selecione **Cores dos Dados**. Defina a cor de **MaintenanceProbability** com o valor **F2C80F**.

19. Altere o **Título** do gráfico para **Probabilidade de manutenção e recall do veículo por cidade**.

    ![Título do gráfico de barras empilhadas 100%](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

20. Selecione a área em branco para adicionar uma nova visualização.

21. Na guia **Visualizações**, selecione a visualização **Gráfico de Área**. Arraste **Model** para a área **Eixo**. Arraste **engineOil**, **tirepressure**, **speed** e **MaintenanceProbability** para a área **Valores**. Altere o tipo de agregação para **Média**. 

    ![Tipo de agregação](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

22. Altere o **Título** do gráfico para **Média do óleo do motor, pressão do pneu, velocidade e probabilidade de manutenção por modelo**.

    ![Título do gráfico de área](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

23. Selecione a área em branco para adicionar uma nova visualização.

24. Na guia **Visualizações**, selecione a visualização **Gráfico de Dispersão**. Arraste **Model** para as áreas **Detalhes** e **Legenda**. Arraste **fuel** para a área **Eixo X**. Altere a agregação para **Média**. Arraste **engineTemperature** para a área **Eixo Y**. Altere a agregação para **Média**. Arraste **vin** para a área **Tamanho**.

    ![Áreas Detalhes, Tamanho, Eixo e Legenda](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

25. Altere o gráfico **Título** para **Média de combustível, média de temperatura do motor, contagem de vin por modelo e modelo**.

    ![Título do gráfico de dispersão](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    O relatório final é semelhante ao exemplo abaixo:

    ![Relatório final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Fixar visualizações de relatórios para o painel em tempo real
1. Crie um painel em branco selecionando o símbolo de adição ao lado de **Painéis**. Insira o nome **Painel de análise de telemetria do veículo**.

    ![Símbolo de adição do painel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

2. Fixe as visualizações dos relatórios anteriores no painel. 

    ![Símbolo de fixar no painel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    Quando todos os três relatórios forem fixados no painel, ele deve ficar mais ou menos como o exemplo a seguir. Se você não criou todos os relatórios, o painel poderá ficar diferente. 

    ![Painel com relatórios](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Você criou o painel em tempo real com êxito. Durante a execução de CarEventGenerator.exe e RealtimeDashboardApp.exe, você verá atualizações dinâmicas no painel. As etapas a seguir devem demorar cerca de 10 a 15 minutos para serem concluídas.

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>Instalar o painel de processamento em lote do Power BI
> [!NOTE]
> Demora cerca de duas horas (após a conclusão bem-sucedida da implantação) para concluir a execução da pipeline de processamento em lote de ponta a ponta e processar um ano de dados gerados. Aguarde a conclusão do processamento antes de prosseguir com as etapas a seguir. 
> 
> 

### <a name="download-the-power-bi-designer-file"></a>Baixar o arquivo de designer do Power BI

1. Um arquivo de designer pré-configurado do Power BI é incluído como parte das instruções de operação manual de implantação. Procure “2. Configurar o painel de processamento em lotes do PowerBI".

2. Baixe o modelo do Power BI para o painel de processamento em lotes chamado **ConnectedCarsPbiReport.pbix**.

3. Salve-o localmente.

### <a name="configure-power-bi-reports"></a>Configurar relatórios do Power BI

1. Abra o arquivo de designer **ConnectedCarsPbiReport.pbix** usando o Power BI Desktop. Caso ainda não tenha feito isso, instale o Power BI Desktop do site [Instalação do Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331).

2. Selecione **Editar Consultas**.

    ![Editar Consultas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

3. Clique duas vezes em **Fonte**.

    ![Fonte](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

4. Atualize a cadeia de conexão do servidor com o servidor do SQL Azure provisionado como parte da implantação. Examine as instruções de operação manual no banco de dados SQL do Azure:

    * Servidor: somethingsrv.database.windows.net
    * Banco de dados: connectedcar
    * Nome de usuário: username
    * Senha: gerencie sua senha do SQL Server no portal do Azure.

5. Deixe **Banco de dados** como **connectedcar**.

    ![Banco de dados](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

6. Selecione **OK**.

7. A guia **Credencial do Windows** é selecionada por padrão. Altere-a para **Credenciais de banco de dados** selecionando a guia **Banco de Dados** à direita.

8. Insira o **Nome de usuário** e a **Senha** de seu Banco de Dados SQL do Azure especificado durante a configuração da implantação.

    ![Credenciais do banco de dados](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

9. Selecione **Conectar**.

10. Repita as etapas anteriores para cada uma das três consultas restantes presentes no painel à direita. Em seguida, atualize os detalhes de conexão da fonte de dados.

11. Selecione **Fechar e Carregar**. Os conjuntos de dados de arquivo do Power BI Desktop são conectados às tabelas de banco de dados SQL.

12. Selecione **Fechar** fechar o arquivo do Power BI Desktop.

    ![Feche](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

13. Selecione **Salvar** para salvar as alterações. 

Você configurou todos os relatórios que correspondem ao caminho de processamento em lotes na solução. 

## <a name="upload-to-powerbicom"></a>Carregar no powerbi.com
1. Vá para o [portal da Web do Power BI](http://powerbi.com)e entre.

2. Selecione **Obter Dados**.

3. Carregue o arquivo do Power BI Desktop. Selecione **Obter Dados** > **Obter Arquivos** > **Arquivo local**.

4. Vá para **ConnectedCarsPbiReport.pbix**.

5. Depois que o arquivo é carregado, volte para seu espaço de trabalho do Power BI. Um conjunto de dados, relatórios e um painel em branco são criados para você.  

6. Fixe os gráficos no novo painel chamado **Painel de Análise de Telemetria do Veículo** no Power BI. Selecione o painel em branco que foi criado anteriormente e vá para a seção **Relatórios**. Selecione o relatório que acabou de ser carregado.  

    ![Novo painel do Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    O relatório tem seis páginas:

    Página 1: densidade do veículo  
    Página 2: integridade do veículo em tempo real  
    Página 3: veículos com direção agressiva   
    Página 4: veículos que sofreram recall  
    Página 5: veículos com uso eficiente de combustível  
    Página 6: logotipo da Contoso Motors  

    ![Relatório do Power BI com seis páginas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

7. **Na Página 3**, fixe o seguinte conteúdo:  

    a. **Contagem de vin**  

   ![Página 3 Contagem de vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    b. **Veículos com direção agressiva por modelo: gráfico de cascata** 

   ![Página 3 gráfico 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

8. **Na Página 5**, fixe o seguinte conteúdo: 

    a. **Contagem de vin**

   ![Página 5 gráfico 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    b. **Veículos com uso eficiente de combustível: gráfico de colunas em cluster**

   ![Página 5 gráfico 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

9. **Na Página 4**, fixe o seguinte conteúdo:  

    a. **Contagem de vin** 

   ![Página 7 gráfico 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    b. **Veículos que tiveram recall por cidade, modelo : mapa de árvore**

   ![Página 4 gráfico 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

10. **Na Página 6**, fixe o seguinte conteúdo:  

    * **Logotipo da Contoso Motors**

    ![Logotipo da Contoso Motors ](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>Organizar o painel  

1. Vá para o painel.

2. Passe o mouse sobre cada gráfico. Renomeie cada gráfico com base no nome fornecido no exemplo de painel concluído abaixo:

   ![Organização do painel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
3. Mova os gráficos para que se pareçam com o painel de exemplo abaixo:

    ![Painel reorganizado](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

4. Depois de criar todos os relatórios mencionados neste documento, o painel final se parecerá com o exemplo abaixo: 

   ![Painel final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Você criou com êxito os relatórios e o painel para obter informações preditivas, em lote e em tempo real sobre a integridade do veículo e hábitos de condução.  
