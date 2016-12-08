---
title: Processar eventos de Hubs de Eventos com o Storm no HDInsight | Microsoft Docs
description: Saiba como processar dados de Hubs de Eventos com uma topologia Storm C# criada no Visual Studio usando as Ferramentas do HDInsight para Visual Studio.
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 77d0dfe7e09baab9b923b3c49af0cb7c28cd8625


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight
Os Hubs de Eventos do Azure permitem processar grandes quantidades de dados de sites, aplicativos e dispositivos. O spout dos Hubs de Eventos facilita o uso do Apache Storm no HDInsight para analisar esses dados em tempo real. Você pode também gravar dados no Hub de Eventos usando o bolt dos Hubs de Eventos.

Neste tutorial, você aprenderá como usar os modelos do Visual Studio instalados com ferramentas do HDInsight para o Visual Studio criar duas topologias que funcionam com os Hubs de Eventos do Azure.

* **EventHubWriter**: gera dados aleatoriamente e grava nos Hubs de Eventos
* **EventHubReader**: lê dados dos Hubs de Eventos e registra os dados nos logs do Storm

> [!NOTE]
> Embora as etapas neste documento dependam de um ambiente de desenvolvimento do Windows com Visual Studio, o projeto compilado pode ser enviado a um cluster HDInsight baseado em Windows ou Linux. Somente os clusters baseados em Linux criados depois de 28/10/2016 são compatíveis com as topologias do SCP.NET.
> 
> Para usar uma topologia do C# com um cluster baseado em Linux, você deverá atualizar o pacote NuGet Microsoft.SCP.Net.SDK usado pelo seu projeto para a versão 0.10.0.6 ou superior. A versão do pacote também deve coincidir com a versão principal do Storm instalada no HDInsight. Por exemplo, o HDInsight versões 3.3 e 3.4 usam o Storm versão 0.10.x, enquanto o HDInsight 3.5 usa o Storm 1.0. x.
> 
> As topologias C# em clusters baseados em Linux devem usar o .NET 4.5 e o Mono para execução no cluster HDInsight. A maioria das coisas funcionará, mas você deve verificar o documento [Compatibilidade do Mono](http://www.mono-project.com/docs/about-mono/compatibility/) em busca de possíveis incompatibilidades.
> 
> Para obter uma versão Java deste projeto que também funcionará em um cluster baseado em Windows ou Linux, confira [Processar eventos dos Hubs de Eventos do Azure com Storm no HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* Um [Apache Storm no cluster HDInsight](hdinsight-apache-storm-tutorial-get-started.md)
* Um [Hub de Eventos do Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* O [SDK do .NET do Azure](http://azure.microsoft.com/downloads/)
* As [Ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

## <a name="completed-project"></a>Projeto concluído
Você pode baixar uma versão concluída do projeto criado neste tutorial do GitHub: [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). No entanto, você ainda precisa fornecer definições de configuração seguindo as etapas neste tutorial.

## <a name="event-hubs-spout-and-bolt"></a>Bolt e spout dos Hubs de Eventos
O bolt e o spout dos Hubs de Eventos são componentes de Java que permitem trabalhar facilmente com o Hub de Eventos do Apache Storm. Embora esses componentes sejam escritos em Java, as Ferramentas do HDInsight para Visual Studio permitem que você crie topologias híbridas que combinem componentes C# e Java.

O bolt e o spout são distribuídos como um único arquivo Java (.jar) denominado **eventhubs-storm-spout-#.#-jar-with-dependencies.jar**, em que #.# é a versão do arquivo.

### <a name="download-the-jar-file"></a>Baixar o arquivo .jar
A versão mais recente do arquivo jar foi incluída no projeto [exemplos do Storm para HDInsight](https://github.com/hdinsight/hdinsight-storm-examples) na pasta **lib/eventhubs**. Para baixar o arquivo, use um dos métodos a seguir.

> [!NOTE]
> O bolt e spout foram enviados para inclusão no projeto Apache Storm. Para obter mais informações, confira [STORM-583: check-in inicial para Hubs de Eventos do storm](https://github.com/apache/storm/pull/336/files) no GitHub.
> 
> 

* **Baixar um arquivo ZIP**: no site [Exemplos do Storm para HDInsight](https://github.com/hdinsight/hdinsight-storm-examples), selecione **Baixar ZIP** no painel à direita para baixar um arquivo .zip que contém o projeto.
  
    ![botão baixar zip](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)
  
    Depois de baixar o arquivo, você poderá extrair o arquivo para o diretório **lib** .
* **Clonar o projeto**: se você tiver o [Git](http://git-scm.com/) instalado, use o comando a seguir para clonar o repositório localmente e, em seguida, localize o arquivo no diretório **lib**.
  
        git clone https://github.com/hdinsight/hdinsight-storm-examples

## <a name="configure-event-hubs"></a>Configurar os Hubs de Eventos
Hubs de Eventos é a fonte de dados para este exemplo. Use as informações da seção **Criar um Hub de Eventos** do documento [Introdução aos Hubs de Eventos](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

1. Depois de criar o hub de eventos, exiba a folha EventHub no portal do Azure e selecione **Políticas de acesso compartilhado**. Use a entrada **+ Adicionar** para incluir as seguintes políticas:
   
   | Nome | Permissões |
   | --- | --- |
   | gravador |Enviar |
   | leitor |Escutar |
   
    ![políticas](./media/hdinsight-storm-develop-csharp-event-hub-topology/sas.png)
2. Selecione as políticas **leitor** e **gravador**. Copie e salve o valor **CHAVE PRIMÁRIA** para ambas as políticas, pois elas serão usadas posteriormente.

## <a name="configure-the-eventhubwriter"></a>Configurar o EventHubWriter
1. Se você ainda não instalou a versão mais recente das Ferramentas do HDInsight para Visual Studio, consulte [Introdução ao uso das Ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).
2. Baixe a solução de [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Abra a solução e examine o código do projeto **EventHubWriter**.
3. No projeto **EventHubWriter**, abra o arquivo **App.config**. Use as informações do Hub de Eventos que você configurou anteriormente para preencher o valor das seguintes chaves:
   
   | Chave | Valor |
   | --- | --- |
   | EventHubPolicyName |gravador (se você usou um nome diferente para a política com a permissão *Enviar*, use-o.) |
   | EventHubPolicyKey |A chave para a política de gravador |
   | EventHubNamespace |O namespace que contém o seu Hub de Eventos |
   | EventHubName |Nome do Hub de Eventos |
   | EventHubPartitionCount |O número de partições no seu Hub de Eventos |
4. Salve e feche o arquivo **App.config**.

## <a name="configure-the-eventhubreader"></a>Configurar o EventHubReader
1. Abra o projeto **EventHubReader** e examine o código.
2. Abra o **App.config** do **EventHubWriter**. Use as informações do Hub de Eventos que você configurou anteriormente para preencher o valor das seguintes chaves:
   
   | Chave | Valor |
   | --- | --- |
   | EventHubPolicyName |leitor (se você usou um nome diferente para a política com a permissão *Escutar*, use-o.) |
   | EventHubPolicyKey |A chave para a política de leitor |
   | EventHubNamespace |O namespace que contém o seu Hub de Eventos |
   | EventHubName |Nome do Hub de Eventos |
   | EventHubPartitionCount |O número de partições no seu Hub de Eventos |
3. Salve e feche o arquivo **App.config**.

## <a name="deploy-the-topologies"></a>Implantar as topologias
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **EventHubReader** e escolha **Enviar para o Storm no HDInsight**.
   
    ![enviar para o storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)
2. Na tela **Enviar Topologia**, selecione seu **Cluster Storm**. Expanda **Configurações Adicionais**, selecione **Caminhos de Arquivo Java**, selecione **...** e o diretório que contém o arquivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** baixado anteriormente. Por fim, clique em **Enviar**.
   
    ![Imagem da caixa de diálogo de envio](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)
3. Depois que a topologia tiver sido enviada, o **Visualizador de Topologias Storm** será exibido. Selecione a topologia **EventHubReader** do lado esquerdo da caixa de diálogo para exibir estatísticas da topologia. Atualmente, nada deveria estar acontecendo, pois nenhum evento foi gravado no Hub de Eventos ainda.
   
    ![exibição de armazenamento de exemplo](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)
4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **EventHubWriter** e escolha **Enviar para o Storm no HDInsight**.
5. Na tela **Enviar Topologia**, selecione seu **Cluster Storm**. Expanda **Configurações Adicionais**, selecione **Caminhos de Arquivo Java**, selecione **...** e o diretório que contém o arquivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** baixado anteriormente. Por fim, clique em **Enviar**.
6. Depois que a topologia tiver sido enviada, atualize a lista de topologia no **Visualizador de Topologias Storm** para verificar se ambas estão em execução no cluster.
7. Em **Visualizador de Topologias Storm**, escolha a topologia **EventHubReader**.
8. No modo de exibição de gráficos, clique duas vezes no componente **LogBolt**. Isso abrirá a página **Resumo do Componente** para o bolt.
9. Na seção **Executores**, selecione um dos links na coluna **Porta**. Isso exibirá informações registradas no log pelo componente. As informações registradas no log são semelhantes a estas:
   
        2016-10-20 13:26:44.186 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {5769732396213255808=520853934697489134}, [{"deviceId":3,"deviceValue":1379915540}]
        2016-10-20 13:26:44.234 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {7154038361491319965=4543766486572976404}, [{"deviceId":3,"deviceValue":459399321}]
        2016-10-20 13:26:44.335 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:6, stream: default, id: {513308780877039680=-7571211415704099042}, [{"deviceId":5,"deviceValue":845561159}]
        2016-10-20 13:26:44.445 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {-2409895457033895206=5479027861202203517}, [{"deviceId":8,"deviceValue":2105860655}]

## <a name="stop-the-topologies"></a>Interromper as topologias
Para interromper as topologias, selecione cada topologia no **Visualizador de Topologia Storm** e clique em **Eliminar**.

![imagem de eliminação de uma topologia](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Excluir o cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="notes"></a>Observações
### <a name="checkpointing"></a>Ponto de verificação
O EventHubSpout cria periodicamente pontos de verificação para seu estado para o nó Zookeeper, que salva o deslocamento atual para mensagens lidas da fila. Isso permite que o componente comece a receber mensagens no deslocamento salvo nos seguintes cenários:

* A instância de componente falha e é reiniciada.
* Você aumenta ou reduz o cluster ao adicionar ou remover nós.
* A topologia é interrompida e reiniciada **com o mesmo nome**.

Você também pode exportar e importar os pontos de verificação persistentes para WASB (Armazenamento do Azure usado pelo seu cluster HDInsight). Os scripts para fazer isso estão localizados no Storm no cluster HDInsight, em **c:\apps\dist\storm-0.9.3.2.2.1.0-2340\zkdatatool-1.0\bin**.

> [!NOTE]
> O número de versão no caminho pode ser diferente, já que a versão do Storm instalada no cluster pode ser alterada no futuro.
> 
> 

Os scripts que estão nesse diretório são:

* **stormmeta_import.cmd**: importe todos os metadados do Storm do contêiner de armazenamento de cluster padrão para o Zookeeper.
* **stormmeta_export.cmd**: exporte todos os metadados do Storm do Zookeeper para o contêiner de armazenamento do cluster padrão.
* **stormmeta_delete.cmd**: exclua todos os metadados do Storm do Zookeeper.

Exportar uma importação permite que você mantenha os dados de ponto de verificação quando precisar excluir o cluster, mas quiser retomar o processamento do deslocamento atual no hub quando colocar um novo cluster novamente online.

> [!NOTE]
> Como os dados são mantidos para o contêiner de armazenamento padrão, o novo cluster **deverá** usar a mesma conta de armazenamento e o contêiner do cluster anterior.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a usar o Bolt e o Spout dos Hub de Eventos Java de uma topologia C# para trabalhar com dados no Hub de Eventos do Azure. Para saber mais sobre a criação de topologias de C#, consulte o seguinte.

* [Desenvolver topologias C# para o Apache Storm no HDInsight usando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Guia de programação do SCP](hdinsight-storm-scp-programming-guide.md)
* [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)




<!--HONumber=Nov16_HO3-->


