---
title: Instalar aplicativo publicado - Coletor de dados dos conjuntos de fluxos - HDInsight do Azure | Microsoft Docs
description: Instalar e usar o aplicativo de Hadoop de terceiros do Coletor de Dados dos Conjuntos de Fluxos.
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 90775452c58457ae8ecc73687a375606474158f5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---streamsets-data-collector"></a>Instalar aplicativo publicado - Coletor de dados dos conjuntos de fluxos

Este artigo descreve como instalar e executar o aplicativo de Hadoop publicado do [Coletor de dados dos conjuntos de fluxos para o HDInsight](https://streamsets.com/) no HDInsight do Azure. Para obter uma visão geral da plataforma do aplicativo HDInsight e uma lista dos aplicativos publicados de Independent Software Vendor (ISV), consulte [Instalar aplicativos de Hadoop de terceiros](hdinsight-apps-install-applications.md). Para obter instruções sobre como instalar seu próprio aplicativo, confira [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).

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

## <a name="prerequisites"></a>pré-requisitos

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
* [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): Saiba como usar a Ação de Script para instalar aplicativos adicionais.
* [Usar nós de borda vazios no HDInsight](hdinsight-apps-use-edge-node.md): Saiba como usar um nó de borda vazio para acessar clusters HDInsight, e testar e hospedar aplicativos HDInsight.
