---
title: Use o Apache Phoenix e o SQLLine com o HBase no Azure HDInsight
description: Saiba como usar o Apache Phoenix no HDInsight. Além disso, saiba como instalar e configurar o SQLLine no seu computador para se conectar a um cluster HBase no HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: hrasheed
ms.openlocfilehash: 38f86bd19c85440fbad0e7fd56a3dd9ba836c7b8
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58903295"
---
# <a name="use-apache-phoenix-with-linux-based-apache-hbase-clusters-in-hdinsight"></a>Usar o Apache Phoenix com clusters do Apache HBase baseado em Linux no HDInsight
Saiba como usar o [Apache Phoenix](https://phoenix.apache.org/) no Azure HDInsight e como usar o SQLLine. Para obter mais informações sobre o Phoenix, consulte [Apache Phoenix em 15 minutos ou menos](https://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Para conhecer a gramática do Phoenix, consulte [Gramática do Apache Phoenix](https://phoenix.apache.org/language/index.html).

> [!NOTE]  
> Para obter informações sobre a versão do Phoenix no HDInsight, consulte [Novidades nas versões de cluster Apache Hadoop fornecidas pelo HDInsight](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Usar o SQLLine
[SQLLine](http://sqlline.sourceforge.net/) é um utilitário de linha de comando para executar o SQL.

### <a name="prerequisites"></a>Pré-requisitos
Antes de poder usar o SQLLine, você deve ter os seguintes itens:

* **Um cluster do Apache HBase no HDInsight**. Para criar um, veja [Introdução ao Apache HBase no HDInsight](./apache-hbase-tutorial-get-started-linux.md).

Ao se conectar a um cluster do HBase, você precisa se conectar a uma das [VMs do Apache ZooKeeper](https://zookeeper.apache.org/). Cada cluster HDInsight tem três VMs do ZooKeeper.

**Para obter o nome de host do ZooKeeper**

1. Abra o [Apache Ambari](https://ambari.apache.org/) navegando até **https: //\< nome do cluster \>.azurehdinsight.net**.
2. Para se conectar, insira o nome de usuário HTTP (cluster) e a senha.
3. No menu à esquerda, selecione **ZooKeeper**. Três instâncias do **Servidor do ZooKeeper** serão listadas.
4. Clique em uma das instâncias do **Servidor do ZooKeeper** listadas. No painel **Resumo**, encontre o **Nome do host**. Ele é semelhante a *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Para usar o SQLLine**

1. Conecte-se ao cluster usando o SSH. Para obter mais informações, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. No SSH, use os seguintes comandos para executar o SQLLine:

        cd /usr/hdp/current/phoenix-client/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Execute os seguintes comandos para criar uma tabela do HBase e insira alguns dados:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Para obter mais informações, consulte o [manual do SQLLine](http://sqlline.sourceforge.net/#manual) e a [gramática do Apache Phoenix](https://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar o Apache Phoenix no HDInsight. Para saber mais, consulte os seguintes artigos:

* [Visão geral do HBase do HDInsight][hdinsight-hbase-overview].
  O Apache HBase é um banco de dados NoSQL de código-fonte aberto e Apache criado no Apache Hadoop que fornece acesso aleatório e consistência forte para grandes quantidades de dados não estruturados e semi-estruturados.
* [Provisione clusters do Apache HBase na rede virtual do Azure][hdinsight-hbase-provision-vnet].
  Com a integração de rede virtual, os clusters do Apache HBase podem ser implantados na mesma rede virtual dos seus aplicativos, para que os aplicativos possam se comunicar diretamente com o HBase.
* [Configure a replicação do Apache HBase no HDInsight](apache-hbase-replication.md). Saiba como configurar a replicação do Apache HBase em dois datacenters do Azure.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


