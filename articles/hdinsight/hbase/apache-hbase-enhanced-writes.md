---
title: Azure HDInsight aprimorada grava para o Apache HBase (versão prévia)
description: Fornece uma visão geral do recurso grava aprimorado do Azure HDInsight, que usa discos gerenciados premium para melhorar o desempenho do Apache HBase escrever em frente Log.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 3/27/2019
ms.openlocfilehash: b0c71d0f101ea262b6ce56c845ef9f375a7de85e
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804200"
---
# <a name="azure-hdinsight-enhanced-writes-for-apache-hbase-preview"></a>Azure HDInsight aprimorada grava para o Apache HBase (versão prévia)

Este artigo fornece informações sobre o **aprimorada grava** recurso para o Apache HBase no HDInsight do Azure e como ele pode ser usado com eficiência para melhorar o desempenho de gravação. **Aprimorado gravações** usa [discos gerenciados premium do Azure SSD](../../virtual-machines/linux/disks-types.md#premium-ssd) para melhorar o desempenho do Apache HBase escrever em frente Log (WAL). Para saber mais sobre o Apache HBase, consulte [o que é o Apache HBase no HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Visão geral da arquitetura do HBase

No HBase, um **linha** consiste em um ou mais **colunas** e é identificado por um **chave de linha**. Várias linhas compõem uma **tabela**. Colunas contêm **células**, que são versões de data/hora do valor dessa coluna. Colunas são agrupadas em **famílias de colunas**, e todas as colunas em uma família de coluna são armazenadas juntos em arquivos de armazenamento chamados **HFiles**.

**Regiões** no HBase são usados para balancear a carga de processamento de dados. HBase primeiro armazena as linhas de uma tabela em uma única região e, em seguida, distribui as linhas em várias regiões como a quantidade de dados na tabela aumenta. **Servidores de região** pode lidar com solicitações de várias regiões.

## <a name="write-ahead-log-for-apache-hbase"></a>Log Write-Ahead para o Apache HBase

As atualizações de dados no HBase são gravadas primeiro para um tipo de log de confirmação chamado uma gravação em frente Log (WAL). Depois que a atualização é armazenada em WAL, ela é gravada na memória **MemStore**. Quando os dados na memória atingem sua capacidade máxima, ele foi gravado no disco como um **HFile**.

Se um **RegionServer** falhar ou ficar indisponível antes que o MemStore é liberado, o Log de gravação em frente pode ser usado para reproduzir as atualizações. Sem o WAL, se um **RegionServer** falhou antes de liberar atualizações para um **HFile**, todas as atualizações sejam perdidas.

## <a name="enhanced-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Recurso aprimorado de gravações no Azure HDInsight para o Apache HBase

O recurso avançado grava resolve o problema de latências de gravação maiores causado pelo uso de Logs Write Ahead que estão no armazenamento em nuvem.  O recurso avançado grava para clusters HDInsight Apache HBase, anexa premium SSD discos gerenciados para a cada RegionServer (nó de trabalho). Gravar que logs em frente, em seguida, são gravados para o arquivo de sistema HDFS (Hadoop) montado nesses discos gerenciados premium em vez de armazenamento em nuvem.  Managed disks Premium usam discos de estado sólido (SSDs) e oferecem excelente desempenho de e/s com tolerância a falhas.  Ao contrário de discos não gerenciados, se uma unidade de armazenamento ficar inativo, ele não afetará outras unidades de armazenamento no mesmo conjunto de disponibilidade.  Como resultado, os discos gerenciados fornecem melhor resiliência para seus aplicativos e baixa latência de gravação. Para saber mais sobre o Azure managed disks, consulte [Introdução ao Azure managed disks](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-enhanced-writes-for-hbase-in-hdinsight"></a>Como habilitar aprimorada grava para o HBase no HDInsight

Para criar um novo cluster HBase com o recurso avançado grava, siga as etapas em [configurar clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) até chegar **etapa 3, o armazenamento**. Sob **configurações de Metastore**, clique na caixa de seleção ao lado **habilitar aprimorada grava (visualização)**. Em seguida, continue com as etapas restantes para a criação do cluster.

![Habilitar a opção de gravações aprimorados para o Apache HBase no HDInsight](./media/apache-hbase-enhanced-writes/enhanced-writes-cluster-creation.jpg)

## <a name="other-considerations"></a>Outras considerações

Para preservar a durabilidade dos dados, crie um cluster com um mínimo de três nós de trabalho. Depois de criado, você não pode reduzir verticalmente o cluster para menos de três nós de trabalho. 

Liberação ou desabilitar as tabelas HBase antes de excluir o cluster, para que você não perca dados gravar o Log em frente.

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>Próximas etapas

* Documentação oficial do Apache HBase no [recurso de gravar o Log em frente](https://hbase.apache.org/book.html#wal)
* Para atualizar seu cluster do Apache HBase do HDInsight para usar aprimorada grava, consulte [migrar um cluster do Apache HBase para uma nova versão](apache-hbase-migrate-new-version.md).
