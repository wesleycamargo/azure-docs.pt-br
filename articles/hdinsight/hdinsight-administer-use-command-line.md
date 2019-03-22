---
title: Gerenciar clusters do Apache Hadoop usando o CLI do Azure Classic - Azure HDInsight
description: Aprenda a usar a CLI clássica do Azure para gerenciar clusters do Apache Hadoop no HDInsight do Azure.
services: hdinsight
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: tyfox
ms.openlocfilehash: 61be4088034d8cc557c67c5b6de4972766654a39
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202377"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-using-the-azure-classic-cli"></a>Gerenciar clusters do Apache Hadoop no HDInsight usando a CLI do Azure Classic
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Aprenda a usar a [CLI clássica do Azure](../cli-install-nodejs.md) para gerenciar clusters do [Apache Hadoop](https://hadoop.apache.org/) no HDInsight do Azure. O clássico CLI é implementado no Node.js. Ela pode ser usada em qualquer plataforma que dê suporte ao Node.js, incluindo Windows, Mac e Linux.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **CLI do Azure Classic** - Consulte [Instalar e configurar o CLI do Azure Classic](../cli-install-nodejs.md) para obter informações sobre instalação e configuração.
* **Conecte-se ao Azure**usando o seguinte comando:

    ```cli
    azure login
    ```
  
    Para obter mais informações sobre a autenticação usando uma conta de trabalho ou escola, consulte [Conectar-se a uma assinatura do Azure da CLI do Azure Classic](/cli/azure/authenticate-azure-cli).
* **Alterne para o modo Gerenciador de Recursos do Azure**usando o seguinte comando:
  
    ```cli
    azure config mode arm
    ```

Para obter ajuda, use a opção **-h** .  Por exemplo: 

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Criar clusters com a CLI
Consulte [Criar clusters no HDInsight usando a CLI do Azure Classic](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Listar e mostrar detalhes do cluster
Use os seguintes comandos para listar e mostrar os detalhes do cluster:

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![Modo de exibição de linha de comando de lista de clusters][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Excluir clusters
Use o seguinte comando para excluir um cluster:

```cli
azure hdinsight cluster delete <Cluster Name>
```

Você também pode excluir um cluster excluindo o grupo de recursos que contém o cluster. Observe que isso excluirá todos os recursos no grupo, incluindo a conta de armazenamento padrão.

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>Dimensionar clusters
Para alterar o tamanho do cluster Apache Hadoop:

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>Habilitar/desabilitar o acesso HTTP para um cluster

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a executar diferentes tarefas administrativas de cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Gerenciar clusters do Apache Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar clusters HDInsight usando o Azure PowerShell][hdinsight-admin-powershell]
* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Como usar a CLI do Azure clássico][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Listar e mostrar clusters"
