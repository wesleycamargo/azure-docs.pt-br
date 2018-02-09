---
title: Instalar aplicativos de terceiros do Hadoop no Azure HDInsight | Microsoft Docs
description: Saiba como instalar aplicativos de terceiros do Hadoop no Azure HDInsight.
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: eaf5904d-41e2-4a5f-8bec-9dde069039c2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/06/2017
ms.author: jgao
ms.openlocfilehash: 9924a9656f2e2e268356b8ce293d58afc3d535a9
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="install-third-party-hadoop-applications-on-azure-hdinsight"></a>Instale aplicativos de terceiros do Hadoop no Azure HDInsight

Saiba como instalar aplicativos Hadoop de terceiros no Azure HDInsight. Para obter instruções sobre como instalar seu próprio aplicativo, confira [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).

Um aplicativo do HDInsight é um aplicativo que os usuários podem instalar em um cluster do HDInsight. Esses aplicativos podem ser desenvolvidos pela Microsoft, por ISVs (fornecedores independentes de software) ou por conta própria.  

A lista a seguir mostra os aplicativos publicados:

* A **Plataforma de Inteligência AtScale** transforma seu cluster do HDInsight em um servidor OLAP de expansão. O aplicativo permite consultar bilhões de linhas de dados interativamente usando as ferramentas de BI do Microsoft Excel, do PowerBI e do Tableau Software para QlikView.
* **Cask CDAP para o HDInsight** fornece a primeira plataforma de integração unificada para Big Data, o que reduz o tempo de inatividade na produção para aplicativos de dados e data lakes em 80%. Este aplicativo só dá suporte a clusters HBase 3.4 padrão.
* **DDS DATAIKU no HDInsight** permite que profissionais de dados gerem protótipos, criem e implantem serviços altamente específicos que transformam dados brutos em previsões de negócios de impacto.
* **Datameer**: o [Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft) oferece aos analistas uma forma interativa de descobrir, analisar e visualizar os resultados em Big Data. Inclua facilmente outras fontes de dados para descobrir novas relações e obter com rapidez as respostas de que precisa.
* **Inteligência Artificial H2O para o HDInsight (Beta)** A água gaseificada H2O oferece suporte aos seguintes algoritmos distribuídos: GLM, Naïve Bayes, Distributed Random Forest, Gradient Boosting Machine, Deep Neural Networks, Deep learning, K-means, PCA, Generalized Low Rank Models, Anomaly Detection e Autoencoders.
* **Kyligence Analytics Platform** O KAP (Kyligence Analytics Platform) é um data warehouse pronto para empresas com tecnologia Apache Kylin e Apache Hadoop; ele fornece a latência de consulta de fração de segundos em conjuntos de dados de escala, além de simplificar a análise de dados para analistas e usuários corporativos. 
* **Preparação de dados de autoatendimento Paxata**
* **SnapLogic Hadooplex** O SnapLogic Hadooplex em execução no HDInsight permite que os clientes obtenham percepções corporativas mais rapidamente, fornecendo preparação e ingestão de dados por autoatendimento de praticamente qualquer fonte para a plataforma de nuvem do Microsoft Azure.
* **Spark Job Server for KNIME Spark Executor** O Spark Job Server for KNIME Spark Executor é usado para conectar a Plataforma de Análise KNIME aos clusters HDInsight.
* O **Coletor de Dados de Streamsets para HDnsight** fornece um IDE (ambiente de desenvolvimento integrado) completo que permite projetar, testar, implantar e gerenciar pipelines de ingestão de qualquer ponto a qualquer ponto, que combinam dados de lote e malha e incluem uma variedade de transformações no fluxo – tudo sem ter que escrever código personalizado. 
* **Aplicativo de HDI do WANdisco Fusion** permite conectividade de dados consistente e contínua conforme eles são alterados, não importa o local em que estejam. Dá acesso aos seus dados a qualquer momento e em qualquer lugar sem nenhum tempo de inatividade nem interrupção.

As instruções fornecidas neste artigo usam o portal do Azure. Você também pode exportar o modelo do Azure Resource Manager do portal ou obter uma cópia do modelo do Resource Manager de fornecedores e usar o Azure PowerShell e a CLI do Azure para implantar o modelo.  Consulte [Criar clusters do Hadoop no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>pré-requisitos
Se você quiser instalar aplicativos do HDInsight em um cluster HDInsight existente, precisará ter um cluster HDInsight. Para criar um, confira [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Você também pode instalar aplicativos do HDInsight quando cria um cluster HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalar aplicativos em clusters existentes
O procedimento a seguir mostra como instalar aplicativos do HDInsight em um cluster HDInsight existente.

**Instalar um aplicativo do HDInsight**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Clusters HDInsight** no menu esquerdo.
3. Clique em um cluster HDInsight.  Se não tiver um, você deverá criá-lo primeiro.  Confira [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Clique em **Aplicativos** sob a categoria **Configurações**. Você pode ver uma lista de aplicativos instalados. Se você não encontrar aplicativos, isso significará que não há nenhum aplicativo para esta versão do cluster HDInsight.
   
    ![Menu do portal de aplicativos do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Clique em **Adicionar** no menu. 
   
    ![Aplicativos instalados de aplicativos do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps.png)
   
    Você poderá ver uma lista de aplicativos existentes do HDInsight.
   
    ![Aplicativos disponíveis de aplicativos HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Clique em um dos aplicativos, aceite os termos legais e clique em **Selecionar**.

Você pode ver o status de instalação nas notificações do portal (clique no ícone de sino na parte superior do portal). Depois que o aplicativo é instalado, ele é exibido na folha Aplicativos Instalados.

## <a name="install-applications-during-cluster-creation"></a>Instalar aplicativos durante a criação do cluster
Você tem a opção de instalar aplicativos do HDInsight ao criar um cluster. Durante o processo, os aplicativos do HDInsight são instalados depois que o cluster é criado e está no estado de execução. Para instalar aplicativos durante a criação de cluster usando o portal do Azure, use a opção --Personalizado--, em vez do padrão --Criação rápida--.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Listar os aplicativos do HDInsight instalados e as propriedades
O portal mostra uma lista dos aplicativos do HDInsight instalados para um cluster e as propriedades de cada aplicativo instalado.

**Listar o aplicativo do HDInsight e exibir as propriedades**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Clusters HDInsight** no menu esquerdo. 
3. Clique em um cluster HDInsight.
4. Em **Configurações**, clique em **Aplicativos** na categoria **Geral**. Os aplicativos Instalados estão listados à direita. 
   
    ![Aplicativos instalados de aplicativos do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Clique em um dos aplicativos instalados para mostrar a propriedade. A propriedades lista:
   
   * Nome do aplicativo: nome do aplicativo.
   * Status: status do aplicativo. 
   * Página da Web: a URL do aplicativo Web que você implantou no nó de borda. A credencial é igual às credenciais de usuário HTTP que você configurou para o cluster.
   * Ponto de extremidade HTTP: a credencial é igual às credenciais de usuário HTTP que você configurou para o cluster. 
   * Ponto de extremidade SSH: você pode usar o SSH para se conectar ao nó de borda. As credenciais SSH são iguais às credenciais de usuário SSH que você configurou para o cluster. Para obter informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
6. Para excluir um aplicativo, clique nele com o botão direito do mouse e clique em **Excluir** no menu de contexto.

## <a name="connect-to-the-edge-node"></a>Conectar-se ao nó de borda
Você pode se conectar ao nó de borda usando HTTP e SSH. As informações de ponto de extremidade podem ser encontradas no [portal](#list-installed-hdinsight-apps-and-properties). Para obter informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

As credenciais de ponto de extremidade HTTP são as credenciais de usuário HTTP que você configurou para o cluster HDInsight. As credenciais de ponto de extremidade do SSH são as credenciais SSH que você configurou para o cluster HDInsight.

## <a name="troubleshoot"></a>Solucionar problemas
Confira [Solucionar problemas de instalação](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Próximas etapas
* [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md): saiba como implantar um aplicativo do HDInsight não publicado no HDInsight.
* [Publicar aplicativos do HDInsight](hdinsight-apps-publish-applications.md): saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [MSDN: instalar um aplicativo do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): saiba como definir aplicativos do HDInsight.
* [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como usar a Ação de Script para instalar aplicativos adicionais.
* [Personalizar clusters Hadoop baseados em Linux no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do Resource Manager para criar clusters HDInsight.
* [Usar nós de borda vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como usar um nó de borda vazio para acessar o cluster HDInsight, testar e hospedar aplicativos HDInsight.

