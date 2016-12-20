---
title: Migrar do HDInsight baseado em Windows para o HDInsight baseado em Linux | Microsoft Docs
description: Saiba como migrar de um cluster HDInsight baseado no Windows para um cluster HDInsight baseado no Linux.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: ff35be59-bae3-42fd-9edc-77f0041bab93
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: cc59d7785975e3f9acd574b516d20cd782c22dac
ms.openlocfilehash: f82e8fcb6228df25c8e3181059fe06fea5fbce78


---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migrar de um cluster HDInsight baseado no Windows para um cluster baseado em Linux
Embora o HDInsight baseado em Windows seja uma maneira fácil de usar o Hadoop na nuvem, você pode descobrir que precisa de um cluster baseado em Linux para aproveitar as ferramentas e tecnologias necessárias para sua solução. Muitos itens no ecossistema do Hadoop são desenvolvidos em sistemas baseados em Linux e alguns podem não estar disponíveis para uso com o HDInsight baseado em Windows. Além disso, muitos livros, vídeos e outros materiais de treinamento supõem que você esteja usando um sistema Linux quando trabalha com o Hadoop.

Este documento fornece detalhes sobre as diferenças entre o HDInsight no Windows e no Linux, bem como instruções sobre como migrar cargas de trabalho existentes para um cluster baseado em Linux.

> [!NOTE]
> Os clusters HDInsight usam Ubuntu LTS (suporte de longo prazo) como o sistema operacional para os nós no cluster. Para obter informações sobre a versão do Ubuntu disponível com o HDInsight, além de outras informações de controle de versão do componente, confira [Versões de componente do HDInsight](hdinsight-component-versioning.md).
>
>

## <a name="migration-tasks"></a>Tarefas de migração
Veja a seguir o fluxo de trabalho geral da migração.

![Diagrama do fluxo de trabalho da migração](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Leia cada seção deste documento para entender as alterações que podem ser necessárias ao migrar fluxos de trabalho, trabalhos etc. existentes para um cluster baseado em Linux.
2. Crie um cluster baseado em Linux como um ambiente de teste/controle de qualidade. Para saber mais sobre como criar um cluster baseado em Linux, confira [Criar clusters Hadoop baseados em Linux em HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
3. Copie trabalhos, fontes de dados e coletores existentes para o novo ambiente. Confira a seção Copiar dados para o ambiente de teste para obter mais detalhes.
4. Execute testes de validação para garantir que os trabalhos funcionem conforme o esperado no novo cluster.

Depois de verificar se tudo está funcionando conforme o esperado, agende o tempo de inatividade para a migração. Durante esse tempo de inatividade, execute as tarefas a seguir.

1. Faça backup de dados transitórios armazenados localmente em nós do cluster. Por exemplo, se você tiver dados armazenados diretamente em um nó principal.
2. Exclua o cluster baseado no Windows.
3. Crie um cluster baseado no Linux usando o mesmo repositório de dados padrão que o cluster baseado no Windows usava. Isso permitirá que o novo cluster continue trabalhando nos dados de produção existentes.
4. Importe o backup de todos os dados transitórios.
5. Inicie os trabalhos/continue processando usando o novo cluster.

### <a name="copy-data-to-the-test-environment"></a>Copie os dados para o ambiente de teste.
Há vários métodos para copiar os dados e trabalhos. No entanto, os dois abordados nesta seção são os métodos mais simples para mover os arquivos diretamente para um cluster de teste.

#### <a name="hdfs-dfs-copy"></a>Cópia HDFS DFS
Você pode usar o comando HDFS do Hadoop para copiar diretamente os dados do armazenamento do cluster de produção existente para o armazenamento de um novo cluster de teste usando as etapas a seguir.

1. Encontre as informações sobre a conta de armazenamento e o contêiner padrão do cluster existente. Você pode fazer isso usando o script a seguir do Azure PowerShell.

        $clusterName="Your existing HDInsight cluster name"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
        write-host "Default container: $clusterInfo.DefaultStorageContainer"
2. Siga as etapas no documento Criar clusters baseados em Linux no HDInsight para criar um novo ambiente de teste. Pare antes de criar o cluster e escolha **Configuração Opcional**.
3. Na folha Configuração Opcional, escolha **Contas de Armazenamento Vinculadas**.
4. Escolha **Adicionar uma chave de armazenamento**e, quando solicitado, escolha a conta de armazenamento que foi retornada pelo script do PowerShell na etapa 1. Clique em **Selecionar** em cada folha para fechá-las. E por último, crie o cluster.
5. Assim que o cluster tiver sido criado, conecte-o usando **SSH.**  Se você não sabe direito como usar o SSH com o HDInsight, confira um dos artigos a seguir.

   * [Usar SSH com HDInsight baseado em Linux de clientes Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
   * [Usar SSH com HDInsight baseado em Linux de clientes Linux, Unix ou Mac](hdinsight-hadoop-linux-use-ssh-unix.md)
6. Na sessão SSH, use o comando a seguir para copiar arquivos da conta de armazenamento vinculada para a nova conta de armazenamento padrão. Substitua CONTAINER e ACCOUNT pelas informações de contêiner e conta retornadas pelo script do PowerShell na etapa 1. Substitua o caminho para dados pelo caminho para um arquivo de dados.

        hdfs dfs -cp wasbs://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location

    [AZURE.NOTE] Se a estrutura de diretório que contiver os dados não existir no ambiente de teste, você poderá criá-la usando o comando a seguir.

        hdfs dfs -mkdir -p /new/path/to/create

    A opção `-p` permite a criação de todos os diretórios no caminho.

#### <a name="direct-copy-between-azure-storage-blobs"></a>Cópia direta entre blobs do Armazenamento do Azure
Como alternativa, talvez seja conveniente usar o cmdlet `Start-AzureStorageBlobCopy` do Azure PowerShell para copiar blobs entre contas de armazenamento fora do HDInsight. Para saber mais, confira a seção Como gerenciar blobs do Azure em Usando o Azure PowerShell com o Armazenamento do Azure.

## <a name="client-side-technologies"></a>Tecnologias do lado do cliente
De modo geral, as tecnologias do lado do cliente, como os [cmdlets do Azure PowerShell](../powershell-install-configure.md), a [CLI do Azure](../xplat-cli-install.md) ou o [SDK do .NET para Hadoop](https://hadoopsdk.codeplex.com/), continuarão funcionando do mesmo jeito com os clusters baseados em Linux, pois elas dependem das APIs REST que são as mesmas em ambos os tipos de sistema operacional do cluster.

## <a name="server-side-technologies"></a>Tecnologias do lado do servidor
A tabela a seguir fornece instruções de como migrar componentes do lado servidor que sejam específicos do Windows.

| Se estiver usando esta tecnologia... | Execute esta ação... |
| --- | --- |
| **PowerShell** (scripts do lado do servidor, incluindo as Ações de Script usadas durante a criação do cluster) |Reescreva-os como scripts Bash. Para as Ações de Script, confira [Personalizar clusters HDInsight baseados em Linux usando as Ações de Script](hdinsight-hadoop-customize-cluster-linux.md) e [Desenvolvimento de ação de script com o HDInsight](hdinsight-hadoop-script-actions-linux.md). |
| **CLI do Azure** (scripts de servidor) |Embora a CLI do Azure esteja disponível no Linux, ela não vem pré-instalada nos nós principais do cluster HDInsight. Se você precisar dela para criar o script de servidor, confira [Instalar a CLI do Azure](../xplat-cli-install.md) para obter informações sobre a instalação em plataformas baseadas em Linux. |
| **Componentes do .NET** |O .NET não é totalmente compatível com os clusters HDInsight baseados em Linux. Os clusters Storm no HDInsight baseados em Linux criados depois de 28/10/2017 aceitam topologias Storm do C# usando a estrutura SCP.NET. O suporte adicional para o .NET será adicionado em futuras atualizações. |
| **Componentes do Win32 ou de outra tecnologia exclusiva do Windows** |A orientação depende do componente ou da tecnologia; talvez você possa encontrar uma versão que seja compatível com Linux, ou talvez precise encontrar uma solução alternativa, ou reescrever esse componente. |

## <a name="cluster-creation"></a>Criação do cluster
Esta seção fornece informações sobre as diferenças na criação do cluster.

### <a name="ssh-user"></a>Usuário do SSH
Os clusters do HDInsight baseados em Linux usam o protocolo **SSH (Secure Shell)** para fornecer acesso remoto aos nós do cluster. Diferentemente da Área de Trabalho Remota para clusters baseados em Windows, a maioria dos clientes SSH não proporciona uma experiência de usuário gráfica, mas uma linha de comando que permite executar comandos no cluster. Alguns clientes (como a [MobaXterm](http://mobaxterm.mobatek.net/)) fornecem um navegador gráfico de sistema de arquivos além da linha de comando remota.

Durante a criação do cluster, você deve fornecer um usuário SSH e uma **senha** ou um **certificado de chave pública** para autenticação.

É recomendável usar um certificado de chave pública, pois é mais seguro do que usar uma senha. A autenticação de certificado funciona gerando um par de chaves pública/privada assinada e fornecendo a chave pública na criação do cluster. Ao se conectar ao servidor usando o SSH, a chave privada no cliente fornece autenticação para a conexão.

Para saber mais sobre como usar SSH com HDInsight, confira:

* [Usar SSH com o HDInsight em clientes Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
* [Usar SSH com HDInsight em clientes Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

### <a name="cluster-customization"></a>Personalização do cluster
**Ações de Script** usadas com clusters baseados em Linux devem ser escritas no script Bash. Embora as Ações de Script possam ser usadas durante a criação do cluster, em clusters baseados em Linux, elas também podem ser usadas na personalização depois que um cluster estiver ativo. Para obter mais informações, confira [Personalizar clusters HDInsight baseados em Linux usando as Ação de Script](hdinsight-hadoop-customize-cluster-linux.md) e [Desenvolvimento de ação de script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

Outro recurso de personalização é a **inicialização**. Em clusters do Windows, ela permite especificar o local das bibliotecas adicionais para uso com o Hive. Após a criação do cluster, essas bibliotecas são automaticamente disponibilizadas para uso com as consultas do Hive sem a necessidade de usar o `ADD JAR`.

A inicialização para clusters baseados em Linux não fornece essa funcionalidade. Em vez disso, use a ação de script documentada em [Adicionar bibliotecas do Hive durante a criação do cluster](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Redes Virtuais
Os clusters HDInsight baseados em Windows funcionam apenas com Rede Virtuais Clássicas, enquanto os clusters HDInsight baseados em Linux exigem Redes Virtuais do Resource Manager. Se você tiver recursos em uma Rede Virtual Clássica à qual o cluster HDInsight Linux deve se conectar, confira [Conectando redes virtuais clássicas a novas redes virtuais](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Para saber mais sobre os requisitos de configuração para usar as Redes Virtuais do Azure com o HDInsight, confira [Extend HDInsight capabilities by using a Virtual Network](hdinsight-extend-hadoop-virtual-network.md)(Estender os recursos do HDInsight usando uma Rede Virtual).

## <a name="management-and-monitoring"></a>Gerenciamento e monitoramento
Muitas das interfaces de usuário que você pode ter usado com HDInsight baseado em Windows, a interface de usuário do Yarn ou do Histórico de Trabalhos, estão disponíveis por meio do Ambari. Além disso, o Modo de Exibição do Hive do Ambari fornece uma maneira de executar consultas Hive usando o navegador da Web. A interface de usuário da Web do Ambari está disponível em clusters baseados em Linux em https://NOMEDOCLUSTER.azurehdinsight.net.

Para saber mais sobre como trabalhar com o Ambari, confira os seguintes documentos:

* [Ambari Web](hdinsight-hadoop-manage-ambari.md)
* [API REST do Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Alertas do Ambari
O Ambari tem um sistema de alerta que pode informar a você sobre problemas potenciais com o cluster. Os alertas aparecem como entradas vermelhas ou amarelas na interface de usuário do Ambari Web. No entanto, você também pode recuperá-las por meio da API REST.

> [!IMPORTANT]
> Os alertas do Ambari indicam que *pode* haver um problema, e não que *há* um problema. Por exemplo, você pode receber um alerta de que HiveServer2 não pode ser acessado, mesmo que consiga acessá-lo normalmente.
>
> Vários alertas são implementados como consultas baseadas em intervalo em um serviço e esperam uma resposta por um período específico. Portanto, o alerta não significa necessariamente que o serviço está inativo, apenas que ele não retornou resultados dentro do tempo esperado.
>
>

De modo geral, antes de tomar alguma medida, você deve avaliar se um alerta vem ocorrendo por um longo período ou se espelha problemas do usuário que foram relatados anteriormente com o cluster.

## <a name="file-system-locations"></a>Locais do sistema de arquivos
O sistema de arquivos do cluster Linux é disposto de modo diferente dos clusters HDInsight baseados em Windows. Use a tabela a seguir para encontrar arquivos usados com frequência.

| Preciso encontrar... | Está localizado em... |
| --- | --- |
| Configuração |`/etc`. Por exemplo, `/etc/hadoop/conf/core-site.xml` |
| Arquivos de log |`/var/logs` |
| HDP (Hortonworks Data Platform) |`/usr/hdp`. Há dois diretórios localizados aqui, um que é a versão atual do HDP (por exemplo, `2.2.9.1-1`) e `current`. O diretório `current` contém links simbólicos para arquivos e diretórios localizados no diretório do número de versão e é fornecido como uma maneira conveniente de acessar arquivos HDP, uma vez que o número de versão mudará conforme a versão do HDP for atualizada. |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

De modo geral, se você souber o nome do arquivo, será possível usar o comando a seguir em uma sessão do SSH para encontrar o caminho do arquivo:

    find / -name FILENAME 2>/dev/null

Você também pode usar caracteres curinga com o nome do arquivo. Por exemplo, `find / -name *streaming*.jar 2>/dev/null` retornará o caminho para qualquer arquivo jar que contenha a palavra 'streaming' como parte do nome do arquivo.

## <a name="hive-pig-and-mapreduce"></a>Hive, Pig e MapReduce
As cargas de trabalho de Pig e MapReduce são bastante semelhantes nos clusters baseados em Linux — a principal diferença é que se você usar a Área de Trabalho Remota para se conectar a um cluster baseado no Windows e executar trabalhos, verá um SSH com clusters baseados no Linux.

* [Usar o Pig com o SSH](hdinsight-hadoop-use-pig-ssh.md)
* [Usar o MapReduce com o SSH](hdinsight-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive
A tabela a seguir fornece instruções para migrar as cargas de trabalho do Hive.

| Com base no Windows, uso... | Com base no Linux... |
| --- | --- |
| **Editor de Hive** |[Modo de Exibição do Hive no Ambari](hdinsight-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` para habilitar o Tez |O Tez é o mecanismo de execução padrão para clusters baseados em Linux, de modo que a instrução set não é mais necessária. |
| Arquivos ou scripts CMD no servidor invocados como parte de um trabalho do Hive |Scripts Bash |
| `hive` na Área de Trabalho Remota |O [Beeline](hdinsight-hadoop-use-hive-beeline.md) ou o [Hive em uma sessão do SSH](hdinsight-hadoop-use-hive-ssh.md) |

## <a name="storm"></a>Storm
| Com base no Windows, uso... | Com base no Linux... |
| --- | --- |
| Painel de Controle do Storm |O Painel Storm não está disponível. Confira [Deploy and Manage Storm topologies on Linux-based HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md) (Implantar e gerenciar topologias Storm no HDInsight baseado em Linux) para saber como enviar topologias |
| Interface do Usuário do Storm |A interface de usuário do Storm está disponível em https://NOMEDOCLUSTER.azurehdinsight.net/stormui |
| Visual Studio para criar, implantar e gerenciar topologias híbridas ou do C# |O Visual Studio pode ser usado para criar, implantar e gerenciar topologias C# (SCP.NET) ou híbridas em clusters Storm no HDInsight baseados em Linux criados depois de 28/10/2017. |

## <a name="hbase"></a>HBase
Em clusters baseados em Linux, o znode pai do HBase é `/hbase-unsecure`. Você deve definir isso na configuração para qualquer aplicativo cliente Java que use API Java nativa para HBase.

Confira [Build a Java-based HBase application](hdinsight-hbase-build-java-maven.md) (Criar aplicativo HBase baseado em Java) para obter um exemplo de cliente que defina esse valor.

## <a name="spark"></a>Spark
Os clusters Spark estavam disponíveis em clusters baseados em Windows durante a versão prévia. No entanto, no lançamento, o Spark estará disponível somente com clusters baseados em Linux. Não há caminho de migração de um cluster da versão prévia do Spark baseado em Windows para um cluster de lançamento baseado em Linux.

## <a name="known-issues"></a>Problemas conhecidos
### <a name="azure-data-factory-custom-net-activities"></a>Atividades personalizadas do .NET no Azure Data Factory
Atualmente, atividades personalizadas do .NET no Azure Data Factory não são compatíveis com clusters HDInsight baseados em Linux. Sendo assim, você deve usar um dos métodos a seguir para implementar atividades personalizadas como parte do seu pipeline ADF.

* Execute atividades do .NET no pool do Lote do Azure. Veja a seção Usar serviço vinculado do Lote do Azure em [Usar atividades personalizadas em um pipeline do Azure Data Factory](../data-factory/data-factory-use-custom-activities.md)
* Implemente a atividade como uma atividade do MapReduce. Confira [Invocar Programas MapReduce da Data Factory](../data-factory/data-factory-map-reduce.md) para obter mais informações.

### <a name="line-endings"></a>Terminações de linha
No geral, as terminações de linha em sistemas baseados no Windows usam CRLF, enquanto os sistemas baseados no Linux usam LF. Se você produzir, ou esperar, dados com terminações de linha CRLF, talvez seja preciso modificar os produtores ou consumidores para trabalhar com a terminação de linha LF.

Por exemplo, usar o Azure PowerShell para consultar o HDInsight em um cluster baseado no Windows retornará dados com CRLF. A mesma consulta com um cluster baseado em Linux retornará LF. Em muitos casos, isso não importa para o consumidor dos dados. No entanto, isso deve ser investigado antes da migração para um cluster baseado em Linux.

Em caso de scripts que serão executados diretamente em nós de cluster do Linux (como um script Python usado com um trabalho do MapReduce ou Hive), você sempre deverá usar LF como a terminação de linha. Caso use CRLF, você poderá ver erros ao executar os scripts em um cluster baseado em Linux.

Se você souber que os scripts não contêm cadeia com caracteres CR inseridos, será possível fazer uma alteração em massa nas terminações de linha usando um dos métodos a seguir:

* **Se você tiver scripts que pretende carregar no cluster**, use as seguintes instruções do PowerShell para alterar as terminações de linha de CRLF para LF antes de carregar o script no cluster.

      $original_file ='c:\path\to\script.py'
      $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
      [IO.File]::WriteAllText($original_file, $text)
* **Se tiver scripts que já estão no armazenamento usado pelo cluster**, você poderá usar o comando a seguir em uma sessão do SSH para o cluster baseado em Linux, a fim de modificar o script.

      hdfs dfs -get wasbs:///path/to/script.py oldscript.py
      tr -d '\r' < oldscript.py > script.py
      hdfs dfs -put -f script.py wasbs:///path/to/script.py

## <a name="next-steps"></a>Próximas etapas
* [Saiba como criar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Conectar-se a um cluster baseado em Linux usando SSH em um cliente Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
* [Conectar-se a um cluster baseado em Linux usando SSH em um cliente Linux, Unix ou Mac](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gerenciar um cluster baseado em Linux usando o Ambari](hdinsight-hadoop-manage-ambari.md)



<!--HONumber=Nov16_HO3-->


