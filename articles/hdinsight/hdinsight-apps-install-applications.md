---
title: Instalar aplicativos de terceiros no HDInsight do Azure
description: Saiba como instalar aplicativos de terceiros do Hadoop no Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 47a634ca1c4e904cc1054db3a834483489ade0ec
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093549"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Instalar aplicativos Apache Hadoop de terceiros no Azure HDInsight

Saiba como instalar um aplicativo do [Apache Hadoop](https://hadoop.apache.org/) de terceiros no Azure HDInsight. Para obter instruções sobre como instalar seu próprio aplicativo, confira [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).

Um aplicativo do HDInsight é um aplicativo que os usuários podem instalar em um cluster do HDInsight. Esses aplicativos podem ser desenvolvidos pela Microsoft, por ISVs (fornecedores independentes de software) ou por conta própria.  

A lista a seguir mostra os aplicativos publicados:

* A **Plataforma de Inteligência AtScale** transforma seu cluster do HDInsight em um servidor OLAP de expansão. O aplicativo permite consultar bilhões de linhas de dados interativamente usando as ferramentas de BI do Microsoft Excel, do PowerBI e do Tableau Software para QlikView.
* **Cask CDAP para o HDInsight** fornece a primeira plataforma de integração unificada para Big Data, o que reduz o tempo de inatividade na produção para aplicativos de dados e data lakes em 80%. Este aplicativo só dá suporte a clusters HBase 3.4 padrão.
* **DDS DATAIKU no HDInsight** permite que profissionais de dados gerem protótipos, criem e implantem serviços altamente específicos que transformam dados brutos em previsões de negócios de impacto.
* **O Datameer** é uma plataforma escalável de autoatendimento para preparar, explorar e governar seus dados para acelerações de análise, transformando dados complexos de múltiplas fontes em informações úteis e prontas para o negócio, fornecendo insights mais rápidos e inteligentes em escala corporativa.
* **A inteligência Artificial para HDInsight (Beta) H20**  H2O Sparkling Water suporta os seguintes algoritmos distribuídos: GLM, Naïve Bayes, Distributed Random Forest, Gradient Boosting Machine, Deep Neural Networks, Deep learning, K-means, PCA, Generalized Low Rank Models, Anomaly Detection e Autoencoders.
* **Kyligence Enterprise**, alimentado por tecnologia Apache Kylin, traz informações instantâneas sobre grandes conjuntos de dados usuários comerciais e analistas de dados. Com a tecnologia de aprendizado de máquina de ponta e funcionalidade de modelagem de dados inteligentes, ele melhora significativamente a produtividade da análise de big data. 
* **Preparação de dados de autoatendimento Paxata**
* **Spark Job Server for KNIME Spark Executor** O Spark Job Server for KNIME Spark Executor é usado para conectar a Plataforma de Análise KNIME aos clusters HDInsight.
* **Explosão Presto** Presto é um rápido e escalonável distribuído mecanismo de consulta SQL. Arquitetado para a separação de armazenamento e computação, o Presto é perfeito para consultar dados no Azure Data Lake Storage, no Armazenamento de Blobs do Azure, em bancos de dados SQL e NoSQL e em outras fontes de dados.
* O **Coletor de Dados de Streamsets para HDnsight** fornece um IDE (ambiente de desenvolvimento integrado) completo que permite projetar, testar, implantar e gerenciar pipelines de ingestão de qualquer ponto a qualquer ponto, que combinam dados de lote e malha e incluem uma variedade de transformações no fluxo – tudo sem ter que escrever código personalizado. 
* **O Striim** (pronuncia-se "fluxo") é uma integração de dados de fluxo contínuo de ponta a ponta + plataforma de inteligência, permitindo a ingestão, o processamento e a análise contínuos de fluxos de dados diferentes.
* **[Trifacta](http://www.trifacta.com/)** permite que engenheiros e analistas de dados explorem e preparem com mais eficiência os diversos dados de hoje, utilizando o aprendizado de máquina para fornecer uma experiência de usuário, fluxo de trabalho e arquitetura inovadores.
* **O Unifi Data Platform** é um conjunto integrado de ferramentas de dados de autoatendimento projetado para capacitar o usuário corporativo a enfrentar desafios de dados que geram receita incremental, reduzem custos ou complexidade operacional. 
* **Aplicativo de HDI do WANdisco Fusion** permite conectividade de dados consistente e contínua conforme eles são alterados, não importa o local em que estejam. Dá acesso aos seus dados a qualquer momento e em qualquer lugar sem nenhum tempo de inatividade nem interrupção.
* **Linha de flutuação** cataloga, organiza e governa dados usando a AI para auto tag dados com termos comerciais. O catálogo didático de negócios da Waterline é um componente crítico e de sucesso para iniciativas de análise, conformidade e governança de autoatendimento e gerenciamento de TI.

As instruções fornecidas neste artigo usam o portal do Azure. Também é possível exportar o modelo do Azure Resource Manager do portal ou obter uma cópia do modelo do Resource Manager de fornecedores e usar o Azure PowerShell e a CLI clássica do Azure para implantar o modelo.  Consulte [Criar clusters do Apache Hadoop no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Pré-requisitos
Se você quiser instalar aplicativos do HDInsight em um cluster HDInsight existente, precisará ter um cluster HDInsight. Para criar um, confira [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Você também pode instalar aplicativos do HDInsight quando cria um cluster HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalar aplicativos em clusters existentes
O procedimento a seguir mostra como instalar aplicativos do HDInsight em um cluster HDInsight existente.

**Instalar um aplicativo do HDInsight**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Clusters HDInsight** no menu esquerdo.
3. Clique em um cluster HDInsight.  Se não tiver um, você deverá criá-lo primeiro.  Confira [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Clique em **Aplicativos** sob a categoria **Configurações**. Você pode ver uma lista de aplicativos instalados. Se você não encontrar aplicativos, isso significará que não há nenhum aplicativo para esta versão do cluster HDInsight.
   
    ![Menu do portal de aplicativos do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Clique em **Adicionar** no menu. Você poderá ver uma lista de aplicativos existentes do HDInsight.
   
    ![Aplicativos disponíveis de aplicativos HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Clique em um dos aplicativos disponíveis e, em seguida, siga as instruções para aceitar os termos legais.

Você pode ver o status de instalação nas notificações do portal (clique no ícone de sino na parte superior do portal). Depois que o aplicativo é instalado, ele é exibido na folha Aplicativos Instalados.

## <a name="install-applications-during-cluster-creation"></a>Instalar aplicativos durante a criação do cluster
Você tem a opção de instalar aplicativos do HDInsight ao criar um cluster. Durante o processo, os aplicativos do HDInsight são instalados depois que o cluster é criado e está no estado de execução. Para instalar aplicativos durante a criação de cluster usando o portal do Azure, use a opção --Personalizado--, em vez do padrão --Criação rápida--.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Listar os aplicativos do HDInsight instalados e as propriedades
O portal mostra uma lista dos aplicativos do HDInsight instalados para um cluster e as propriedades de cada aplicativo instalado.

**Listar o aplicativo do HDInsight e exibir as propriedades**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Clusters HDInsight** no menu esquerdo. 
3. Clique em um cluster HDInsight.
4. Em **Configurações**, clique em **Aplicativos** na categoria **Configuração**. Os aplicativos Instalados estão listados à direita. 
   
    ![Aplicativos instalados de aplicativos do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Clique em um dos aplicativos instalados para mostrar a propriedade. A propriedades lista:
   
   * Nome do aplicativo: nome do aplicativo.
   * Status: status do aplicativo. 
   * Página da web: A URL do aplicativo Web que você implantou no nó de borda. A credencial é igual às credenciais de usuário HTTP que você configurou para o cluster.
   * Ponto de extremidade HTTP: A credencial é igual às credenciais de usuário HTTP que você configurou para o cluster. 
   * Ponto de extremidade SSH: Você pode usar o SSH para conectar-se ao nó de borda. As credenciais SSH são iguais às credenciais de usuário SSH que você configurou para o cluster. Para obter informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
6. Para excluir um aplicativo, clique nele com o botão direito do mouse e clique em **Excluir** no menu de contexto.

## <a name="connect-to-the-edge-node"></a>Conectar-se ao nó de borda
Você pode se conectar ao nó de borda usando HTTP e SSH. As informações de ponto de extremidade podem ser encontradas no [portal](#list-installed-hdinsight-apps-and-properties). Para obter informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

As credenciais de ponto de extremidade HTTP são as credenciais de usuário HTTP que você configurou para o cluster HDInsight. As credenciais de ponto de extremidade do SSH são as credenciais SSH que você configurou para o cluster HDInsight.

## <a name="troubleshoot"></a>Solucionar problemas
Confira [Solucionar problemas de instalação](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Próximas etapas
* [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md): saiba como implantar um aplicativo do HDInsight não publicado no HDInsight.
* [Publicar aplicativos HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [MSDN: Instalar um aplicativo HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Saiba como definir aplicativos do HDInsight.
* [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como usar a Ação de Script para instalar aplicativos adicionais.
* [Personalizar clusters Apache Hadoop baseados em Linux no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do Resource Manager para criar clusters HDInsight.
* [Usar nós de borda vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como usar um nó de borda vazio para acessar o cluster HDInsight, testar e hospedar aplicativos HDInsight.

