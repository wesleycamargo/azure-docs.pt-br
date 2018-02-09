---
title: "Migrar um cluster HBase para uma nova versão – HDInsight do Azure | Microsoft Docs"
description: "Como migrar clusters HBase para uma nova versão."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: bc7df6957c0a8d9d40c5e8e5f9d274e0d1aa40bd
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="migrate-an-hbase-cluster-to-a-new-version"></a>Migrar um cluster HBase para uma nova versão

Clusters com base no trabalho, como Spark e Hadoop, são simples de atualizar – consulte [Atualizar cluster HDInsight para uma versão mais recente](../hdinsight-upgrade-cluster.md):

1. Fazer backup de dados transitórios (armazenados localmente).
2. Exclua o cluster existente.
3. Crie um novo cluster na mesma sub-rede de VNET.
4. Importe dados transitórios.
5. Inicie os trabalhos e continue processando usando no novo cluster.

Para atualizar um cluster HBase, algumas etapas adicionais são necessárias, conforme descrito neste artigo.

> [!NOTE]
> O tempo de inatividade durante a atualização deve ser mínimo, na ordem de minutos. Esse tempo de inatividade é causado pelas etapas para liberar todos os dados na memória, e então, pelo tempo para configurar e reiniciar os serviços no novo cluster. Os resultados vão variar, dependendo do número de nós, da quantidade de dados e de outras variáveis.

## <a name="review-hbase-compatibility"></a>Revisar a compatibilidade do HBase

Antes de atualizar o HBase, verifique se as versões do HBase nos clusters de origem e destino são compatíveis. Para obter mais informações, consulte [Componentes e versões do Hadoop disponíveis com o HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]
> É altamente recomendável que você examine a matriz de compatibilidade de versão no [Catálogo do HBase](https://hbase.apache.org/book.html#upgrading).

Esta é um exemplo de matriz de compatibilidade de versão, onde Y indica compatibilidade e N indica uma potencial incompatibilidade:

| Tipo de compatibilidade | Versão principal| Versão secundária | Patch |
| --- | --- | --- | --- |
| Compatibilidade de transferência cliente-servidor | N | S | S |
| Compatibilidade servidor-servidor | N | S | S |
| Compatibilidade de formato de arquivo | N | S | S |
| Compatibilidade de API do cliente | N | S | S |
| Compatibilidade de binário do cliente | N | N | S |
| **Compatibilidade de API limitada ao lado do servidor** |  |  |  |
| Estável | N | S | S |
| Evoluindo | N | N | S |
| Instável | N | N | N |
| Compatibilidade de dependência | N | S | S |
| Compatibilidade operacional | N | N | S |

> [!NOTE]
> Qualquer incompatibilidade de interrupção deve ser descrita nas notas de versão da versão do HBase.

## <a name="upgrade-with-same-hbase-major-version"></a>Atualizar com a mesma versão principal do HBase

O cenário a seguir é para atualização do HDInsight 3.4 para 3.6 (ambos fornecidos com o Apache HBase 1.1.2) com a mesma versão principal do HBase. Outras atualizações de versão são semelhantes, desde que não haja nenhum problema de compatibilidade entre versões de origem e de destino.

1. Certifique-se de que seu aplicativo é compatível com a nova versão, conforme mostrado na matriz de compatibilidade e nas notas de versão do HBase. Teste seu aplicativo em um cluster que esteja executando a versão de destino do HDInsight e do HBase.

2. [Configure um novo cluster do HDInsight de destino](../hdinsight-hadoop-provision-linux-clusters.md) usando a mesma conta de armazenamento, mas com um nome de contêiner diferente:

    ![Usar a mesma Conta de armazenamento, mas criar um Contêiner diferente](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Libere o cluster HBase de origem. Este é o cluster do qual você está atualizando. O HBase grava os dados de entrada em um repositório na memória, chamado _memstore_. Após atingir um determinado tamanho, o memstore é liberado para disco para armazenamento de longo prazo na conta de armazenamento do cluster. Ao excluir o cluster antigo, os memstores são reciclados, possivelmente perdendo dados. Para liberar manualmente o memstore para cada tabela para o disco, execute o script a seguir. A versão mais recente do script está no [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh) do Azure.

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1 or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```
    
4. Interrompa a ingestão para o cluster antigo do HBase.
5. Para garantir que nenhum dado recente do memstore seja liberado, execute novamente o script anterior.
6. Faça logon no Ambari no cluster antigo (https://NOMEDOCLUSTERANTIGO.azurehdidnsight.net) e interrompa os serviços do HBase. Quando for solicitado a confirmar que você gostaria de interromper os serviços, marque a caixa para ativar o modo de manutenção para o HBase. Para saber mais sobre como usar e conectar-se ao Ambari, confira [Gerenciar clusters HDInsight usando a interface de usuário do Ambari Web](../hdinsight-hadoop-manage-ambari.md).

    ![Em Ambari, clique na guia Serviços, em HBase no menu esquerdo, em seguida, em Interromper em Ações de serviço.](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Marque a caixa de seleção Ativar no modo de manutenção para o HBase e confirme.](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Faça logon no Ambari no novo cluster HDInsight. Altere a configuração `fs.defaultFS` HDFS para apontar para o nome do contêiner usado pelo cluster original. Essa configuração está em **HDFS > Configurações > Avançado > Site principal avançado**.

    ![No Ambari, clique na guia Serviços, em HDFS no menu esquerdo, na guia Configurações, e então, na guia Avançado abaixo.](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Em Ambari, altere o nome do contêiner.](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. Salve suas alterações.
9. Reinicie todos os serviços necessários conforme indicado pelo Ambari.
10. Aponte seu aplicativo para o novo cluster.

    > [!NOTE]
    > O DNS estático para que as alterações de aplicativo durante a atualização. Em vez de fazer hard-coding deste DNS, você pode configurar um CNAME nas configurações de DNS do nome de domínio que aponta para o nome do cluster. Outra opção é usar um arquivo de configuração para o aplicativo que você pode atualizar sem reimplantação.

11. Inicie a ingestão para verificar se tudo está funcionando conforme o esperado.
12. Se o novo cluster for satisfatório, exclua o cluster original.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o HBase e como atualizar clusters do HDInsight, consulte os artigos a seguir:

* [Atualizar um cluster HDInsight para uma versão mais recente](../hdinsight-upgrade-cluster.md)
* [Monitorar e gerenciar o Azure HDInsight usando a IU da Web do Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Componentes e versões do Hadoop](../hdinsight-component-versioning.md)
<!--  * [Optimizing configurations using Ambari](hdinsight-changing-configs-via-ambari.md#hbase-optimization-with-the-ambari-web-ui)  -->
