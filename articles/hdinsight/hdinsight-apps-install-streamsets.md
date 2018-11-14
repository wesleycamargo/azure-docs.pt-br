---
title: Instalar aplicativo publicado - Coletor de dados dos conjuntos de fluxos - Azure HDInsight
description: Instalar e usar o aplicativo do Apache Hadoop de terceiros StreamSets Data Collector.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: f963ae53e1396b1ef6279f2bd6502e5ab0cd23a1
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034550"
---
# <a name="install-published-application---streamsets-data-collector"></a>Instalar aplicativo publicado - Coletor de dados dos conjuntos de fluxos

Este artigo descreve como instalar e executar o aplicativo Apache Hadoop [publicado do StreamSets Data Collector para HDInsight](https://streamsets.com/) no Azure HDInsight. Para obter uma visão geral da plataforma do aplicativo HDInsight e uma lista dos aplicativos publicados de Independent Software Vendor (ISV), consulte [Instalar aplicativos de Hadoop de terceiros](hdinsight-apps-install-applications.md). Para obter instruções sobre como instalar seu próprio aplicativo, confira [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-streamsets-data-collector"></a>Sobre o coletor de dados dos Conjuntos de Fluxos

O StreamSets Data Collector é implantado de um aplicativo HDInsight do Azure. O coletor de dados de conjuntos de fluxos fornece um ambiente de desenvolvimento integrado (IDE) de recursos completos que permite que você projete, teste, implante e gerencie qualquer-para-qualquer pipelines de ingestão. Esses pipelines podem mesclar dados de lote e fluxo, e incluir uma variedade de transformações no fluxo, tudo sem a necessidade de gravar código personalizado.

O coletor de dados de conjuntos de fluxos permite compilar fluxos de dados usando vários componentes Big Data, como HDFS, Kafka, Solr, Hive, HBASE e Kudu. Depois que o coletor de dados de conjuntos de fluxos estiver sendo executado em um servidor de borda ou em seu cluster de Hadoop, você obtém o monitoramento em tempo real de anomalia de dados e operações de fluxo de dados. Esse monitoramento inclui alertas baseados em limites, detecção de anomalias e a correção automática dos registros de erro.

O coletor de dados de conjuntos de fluxos foi criado para isolar logicamente cada estágio em um pipeline, para que você possa atender aos novos requisitos de negócios, soltando em novos processadores e conectores sem codificar e com tempo de inatividade mínimo.

### <a name="streamsets-resource-links"></a>Links de recursos dos conjuntos de fluxos

* [Documentação](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [Blog](https://streamsets.com/blog/)
* [Tutoriais](https://github.com/streamsets/tutorials)
* [Fórum do Suporte Developer](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [Canal de Conjuntos de Fluxos Públicos Slack](https://streamsetters.slack.com/)
* [Código-fonte](https://github.com/streamsets)

## <a name="prerequisites"></a>Pré-requisitos

Para instalar este aplicativo em um novo cluster do HDInsight ou em um cluster existente, você deve ter a seguinte configuração:

* Camada(s) do cluster: Standard ou Premium
* Versão(ões) do cluster: 3.5 e posteriores

## <a name="install-the-streamsets-data-collector-published-application"></a>Instale o aplicativo publicado do coletor de dados dos conjuntos de fluxos

Para obter instruções passo a passo sobre como instalar esse e outros aplicativos ISV disponíveis, leia [Instalar aplicativos de Hadoop de terceiros](hdinsight-apps-install-applications.md).

## <a name="launch-streamsets-data-collector"></a>Inicializar o coletor de dados dos Conjuntos de Fluxos

1. Após a instalação, você pode inicializar os conjuntos de fluxos do seu cluster no portal do Azure acessando o painel **Configurações** e, em seguida, selecionando **Aplicativos** na categoria **Geral**. O painel de Aplicativos Instalados lista todos os aplicativos instalados.

    ![Aplicativo de Conjuntos de Fluxos Instalado](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. Quando você seleciona o Coletor de Dados dos Conjuntos de Fluxos, você vê um link para a página da web e o caminho do ponto de extremidade SSH. Selecione o link de página da Web.

3. Na caixa de diálogo de logon, use as credenciais a seguir para fazer logon: `admin` e `admin`.

4. Na página de Introdução, clique em **Criar novo pipeline**.

    ![Criar novo pipeline](./media/hdinsight-apps-install-streamsets/get-started.png)

5. Na janela do novo Pipeline, insira um nome para o pipeline ("Olá, Mundo"); opcionalmente, insira uma descrição e selecione **Salvar**.

6. O console do coletor de dados é mostrado. O painel Propriedades exibe as propriedades de pipeline.
 
    ![Console do coletor de dados](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. Agora você está pronto para seguir o [Tutorial dos Conjuntos de Fluxos](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html). O tutorial fornece instruções passo a passo para criar seu primeiro pipeline.

## <a name="next-steps"></a>Próximas etapas

* [Documentação do coletor de dados dos Conjuntos de Fluxos](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq).
* [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md): Saiba como implantar um aplicativo do HDInsight não publicado no HDInsight.
* [Publicar aplicativos do HDInsight](hdinsight-apps-publish-applications.md): saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [MSDN: instalar um aplicativo do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): saiba como definir aplicativos do HDInsight.
* [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como usar a Ação de Script para instalar aplicativos adicionais.
* [Usar nós de borda vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como usar um nó de borda vazio para acessar clusters HDInsight, e testar e hospedar aplicativos HDInsight.
