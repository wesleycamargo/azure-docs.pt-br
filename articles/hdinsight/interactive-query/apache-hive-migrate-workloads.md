---
title: Migrar cargas de trabalho de Hive do Azure HDInsight 3.6 HDInsight 4.0
description: Saiba como migrar cargas de trabalho Apache Hive no HDInsight 3.6 HDInsight 4.0.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 04/15/2019
ms.openlocfilehash: 708df64802ace17fa77b4e0a695c9f1c3bd18a77
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610185"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrar cargas de trabalho de Hive do Azure HDInsight 3.6 HDInsight 4.0

Este documento mostra como migrar cargas de trabalho do Apache Hive e LLAP no HDInsight 3.6 HDInsight 4.0. HDInsight 4.0 fornece recursos mais recentes de Hive e LLAP como Exibições materializadas e cache de resultado da consulta. Ao migrar suas cargas de trabalho para HDInsight 4.0, você pode usar muitos recursos mais recentes do Hive 3 que não estão disponíveis no HDInsight 3.6.

Este artigo aborda os seguintes assuntos:

* Migração de metadados de Hive para HDInsight 4.0
* Migração segura de transações ACID e tabelas não ACID
* Preservação da seção de políticas de segurança entre as versões do HDInsight
* Execução de consulta e de depuração de HDInsight 3.6 para HDInsight 4.0

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Migrar metadados Apache Hive para HDInsight 4.0

Uma vantagem do Hive é a capacidade de exportar metadados para um banco de dados externo (conhecido como o Metastore do Hive). O **Metastore Hive** é responsável por armazenar as estatísticas de tabela, incluindo o local de armazenamento de tabela, nomes de coluna e informações de índice de tabela. O esquema de banco de dados do metastore é diferente entre as versões do Hive. Faça o seguinte para atualizar um Metastore do Hive do HDInsight 3.6 para que ele seja compatível com o HDInsight 4.0.

1. Crie uma nova cópia do seu metastore externo. A versão 3.6 do HDInsight e HDInsight 4.0 exigem metastore diferentes esquemas e não é possível compartilhar um único metastore.
1. Anexe a nova cópia do metastore a) um cluster HDInsight 4.0 existente ou b) um cluster que você está criando pela primeira vez. Ver [usar armazenamentos de metadados externos no Azure HDInsight](../hdinsight-use-external-metadata-stores.md) para saber mais sobre como anexar um metastore externo a um cluster HDInsight. Depois que o Metastore é anexado, ele será convertido automaticamente em um metastore 4.0 compatível.

> [!Warning]
> A atualização que converte o esquema de metadados 3.6 do HDInsight para o esquema de HDInsight 4.0, não poderá ser revertida.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrar de tabelas do Hive para HDInsight 4.0

Depois de concluir o conjunto anterior de etapas para migrar o Metastore do Hive para HDInsight 4.0, as tabelas e bancos de dados registrados no metastore será visíveis de dentro do cluster HDInsight 4.0 executando `show tables` ou `show databases` de dentro do cluster . Ver [execução da consulta entre as versões do HDInsight](#query-execution-across-hdinsight-versions) para obter informações sobre a execução da consulta em clusters de HDInsight 4.0.

Os dados reais das tabelas, no entanto, não estão acessíveis até que o cluster tem acesso às contas de armazenamento necessário. Para garantir que seu cluster HDInsight 4.0 pode acessar os mesmos dados que seu cluster HDInsight 3.6 antigo, conclua as seguintes etapas:

1. Determinar a conta de armazenamento do Azure da sua tabela ou banco de dados usando descrevem formatado.
2. Se seu cluster HDInsight 4.0 já está em execução, anexe a conta de armazenamento do Azure para o cluster por meio do Ambari. Se você ainda não criou o cluster HDInsight 4.0, verifique se que a conta de armazenamento do Azure é especificada como a primária ou uma conta de armazenamento de cluster secundário. Para obter mais informações sobre como adicionar contas de armazenamento para clusters de HDInsight, consulte [adicionar outras contas de armazenamento ao HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> Tabelas são tratadas de maneira diferente no HDInsight 3.6 e 4.0 do HDInsight. Por esse motivo, é possível compartilhar as mesmas tabelas para clusters de versões diferentes. Se você quiser usar o HDInsight 3.6 ao mesmo tempo como HDInsight 4.0, você deve ter cópias separadas dos dados para cada versão.

Sua carga de trabalho do Hive pode incluir uma combinação de ACID e tabelas não ACID. Uma diferença importante entre o Hive no HDInsight 3.6 (seção 2) e o Hive no HDInsight 4.0 (seção 3) é a conformidade de ACID para tabelas. No HDInsight 3.6, permitindo a Hive ACID-conformidade requer configuração adicional, mas no HDInsight 4.0 tabelas são compatíveis com ACID por padrão. A única ação necessária antes da migração é executar uma compactação de principal com base na tabela ACID no cluster 3.6. Da exibição do Hive ou do Beeline, execute a seguinte consulta:

```bash
alter table myacidtable compact 'major';
```

Essa compactação é necessária porque a versão 3.6 do HDInsight e HDInsight 4.0 ACID tabelas entender ACID deltas diferentes. A compactação impõe uma ficha limpa que garante a consistência de tabela. Uma vez concluída a compactação, as etapas anteriores para a migração de metastore e a tabela será suficiente para usar as tabelas de ACID do HDInsight 3.6 HDInsight 4.0.

## <a name="secure-hive-across-hdinsight-versions"></a>Proteja o Hive no HDInsight versões

Desde o HDInsight 3.6, HDInsight integra-se com o Azure Active Directory usando o pacote de segurança de Enterprise HDInsight (ESP). ESP usa Kerberos e Apache Ranger para gerenciar as permissões de recursos específicos dentro do cluster. Políticas do Ranger implantadas no Hive no HDInsight 3.6 podem ser migradas para o HDInsight 4.0 com as seguintes etapas:

1. Navegue até o painel do Ranger Service Manager no seu cluster HDInsight 3.6.
2. Navegue até a diretiva denominada **HIVE** e exportar a política para um arquivo json.
3. Certifique-se de que todos os usuários chamados em json política exportada existem no novo cluster. Se um usuário é chamado no json de política, mas não existe no novo cluster, adicione o usuário para o novo cluster, ou remova a referência da política.
4. Navegue até a **Ranger Service Manager** painel no seu cluster HDInsight 4.0.
5. Navegue até a diretiva denominada **HIVE** e importe o json de política do ranger da etapa 2.

## <a name="query-execution-across-hdinsight-versions"></a>Execução de consulta entre versões do HDInsight

Há duas maneiras de executar e depurar consultas do Hive/LLAP dentro de um cluster HDInsight 3.6. HiveCLI fornece uma experiência de linha de comando e o modo de exibição do Tez exibição/Hive fornece um fluxo de trabalho baseado em GUI.

No HDInsight 4.0, HiveCLI foi substituído com o Beeline. HiveCLI é um cliente de thrift do Hiveserver 1 e o Beeline é um cliente JDBC que fornece acesso ao Hiveserver 2. Beeline também pode ser usado para se conectar a qualquer outro ponto de extremidade de banco de dados de compatível com JDBC. Beeline é disponível out-of-box no HDInsight 4.0 sem qualquer instalação necessária.

No HDInsight 3.6, o cliente de GUI para interagir com o servidor do Hive é o modo de exibição de Hive do Ambari. HDInsight 4.0 substitui a exibição do Hive com Hortonworks Data Analytics Studio (DAS). DAS não é fornecida com clusters de HDInsight out-of-box e não é um pacote com suporte oficial. No entanto, DAS pode ser instaladas da seguinte maneira no cluster:

1. Baixe o [script de instalação do pacote DAS](https://hdiconfigactions.blob.core.windows.net/dasinstaller/install-das-mpack.sh) e executá-lo em ambos os de cabeçalho do cluster. Não execute este script como uma ação de script.
2. Baixe o [script de instalação do serviço DAS](https://hdiconfigactions.blob.core.windows.net/dasinstaller/install-das-component.sh) e executá-lo como uma ação de script. Selecione **nós de cabeçalho** como o tipo de nó de escolha da interface de ação de script.
3. Quando a ação de script for concluída, navegue até o Ambari e selecione **Studio da análise Data** na lista de serviços. Todos os serviços DAS são interrompidos. No canto superior direito, selecione **ações** e **iniciar**. Agora você pode executar e depurar consultas com DAS.

Depois que o DAS é instalada, se você não vir as consultas que você executou no Visualizador de consultas, execute as seguintes etapas:

1. Defina as configurações para o Hive, o Tez e DAS conforme descrito em [deste guia para solução de problemas de instalação DAS](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Certifique-se de que as seguintes configurações de diretório armazenamento do Azure são blobs de página e estão listados sob `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Reinicie o HDFS, Hive, o Tez e em ambos os de cabeçalho.

## <a name="next-steps"></a>Próximos passos

* [Comunicado de HDInsight 4.0](../hdinsight-version-release.md)
* [Aprofundamento de HDInsight 4.0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [3 tabelas ACID do hive](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)