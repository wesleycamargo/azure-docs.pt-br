---
title: Criar clusters Hadoop baseados no Windows no HDInsight | Microsoft Docs
description: Saiba como criar Hadoop baseado no Windows no HDInsight usando o portal do Azure.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c8689ef3-f56f-4708-8a3a-cc00abc54e8d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/18/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: ede2e4ec5f3414d1c8a17f4c120011eba0d9a6ca
ms.openlocfilehash: a9d7a4372693ad021a898fae9818f2d037d42547


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-the-azure-portal"></a>Crie clusters Hadoop baseados no Windows no HDInsight usando o portal do Azure

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Saiba como criar um cluster Hadoop baseado no Windows no HDInsight usando o portal do Azure. 

As informações neste artigo aplicam-se apenas aos clusters HDInsight baseados no Windows. Para saber mais sobre a criação de clusters baseados no Linux, veja [Criar clusters Hadoop no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Pré-requisitos:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de começar a seguir as instruções neste artigo, você deve ter o seguinte:

* Uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

### <a name="access-control-requirements"></a>Requisitos de controle de acesso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Criar clusters
**Para criar um cluster HDInsight**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **NOVO**, **Inteligência + análises** e clique em **HDInsight**.
3. Digite ou selecione os valores a seguir:
   
   * **Nome do Cluster**: nomeie seu cluster.
   * **Assinatura**: selecione uma assinatura do Azure usada para este cluster.
   * **Configuração do cluster**:

     * **Tipo de cluster**: selecione **Hadoop** para este tutorial.
     * **Sistema operacional**: selecione **Windows** para este tutorial.
     * **Versão**: selecione **Hadoop 2.7.0 (HDI 3.3)**.  Essa é a versão mais recente para os clusters Hadoop baseados no Windows.
     * **Camada de cluster**: selecione **STANDARD** para este tutorial.

   * **Credenciais**:

     * **Nome de usuário de logon do cluster**: o nome padrão é **admin**. 
     * **Senha de logon do cluster**: insira uma senha.
     * **Habilitar Área de Trabalho Remota**: área de trabalho remota não é necessário para este tutorial.

   * **Fonte de dados**:

     * **Método de seleção**: selecione **De todas as assinaturas**.
     * **Selecione uma conta de Armazenamento**: clique em **Criar novo** e então insira um nome para a conta de armazenamento padrão.
     * **Contêiner padrão**: por padrão, o contêiner padrão usa o mesmo nome que o cluster.
     * **Local**: escolha um local perto de você.  Esse local é usado pelo cluster e a conta de armazenamento padrão.
   * **Tamanho do cluster**:

     * **Número de nós de trabalho**: este tutorial requer apenas um nó.
   * **Configurações avançadas**: você pode ignorar esta parte deste tutorial.
   * **Grupo de recursos**: selecione **Criar novo** e então insira um nome.

4. Selecione **Fixar no painel** e clique em **Criar**. Escolher **Fixar no Quadro Inicial** adicionará um bloco para o cluster ao Quadro Inicial do Portal. A criação de um cluster demora entre 15 e 20 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de provisionamento.
5. Quando a criação for concluída, clique no bloco para o cluster no quadro inicial para iniciar a folha de cluster. A folha de cluster fornece informações essenciais sobre o cluster como o nome, o grupo de recursos ao qual ele pertence, o local, o sistema operacional, a URL para o painel do cluster, etc.

## <a name="customize-clusters"></a>Personalizar clusters
* Consulte [Personalizar clusters do HDInsight usando a Inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Consulte [Personalizar clusters do HDInsight baseados em Windows usando a Ação de Script](hdinsight-hadoop-customize-cluster.md).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu várias maneiras de criar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - aprenda como começar a trabalhar com seu cluster HDInsight
* [Enviar trabalhos do Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md) - aprenda como enviar trabalhos ao HDInsight de forma programática
* [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure](hdinsight-administer-use-management-portal.md)




<!--HONumber=Jan17_HO3-->


