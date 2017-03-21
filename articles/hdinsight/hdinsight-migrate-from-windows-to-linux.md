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
ms.date: 01/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: a4d30ffc0a5c5ef9fe7bb892d17f0859ff27f569
ms.openlocfilehash: bf6ef38ba28d11d7894a30115174582903f84580
ms.lasthandoff: 03/02/2017


---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migrar de um cluster HDInsight baseado no Windows para um cluster baseado em Linux
Embora o HDInsight baseado no Windows forneça uma maneira fácil de usar o Hadoop na nuvem, você poderá precisar migrar para um cluster baseado em Linux. Por exemplo, para aproveitar as ferramentas e tecnologias baseadas em Linux que são necessárias para sua solução. Muitos itens no ecossistema do Hadoop são desenvolvidos em sistemas baseados em Linux e podem não estar disponíveis para uso com o HDInsight baseado no Windows. Além disso, muitos livros, vídeos e outros materiais de treinamento supõem que você esteja usando um sistema Linux quando trabalha com o Hadoop.

Este documento fornece detalhes sobre as diferenças entre o HDInsight no Windows e no Linux, bem como instruções sobre como migrar cargas de trabalho existentes para um cluster baseado em Linux.

> [!NOTE]
> Os clusters HDInsight usam o Ubuntu LTS (suporte de longo prazo) como o sistema operacional para os nós no cluster. Para obter informações sobre a versão do Ubuntu disponível com o HDInsight, além de outras informações de controle de versão do componente, confira [Versões de componente do HDInsight](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Tarefas de migração
Veja a seguir o fluxo de trabalho geral da migração.

![Diagrama do fluxo de trabalho da migração](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Leia cada seção deste documento para entender as alterações que podem ser necessárias ao migrar fluxos de trabalho, trabalhos etc. existentes para um cluster baseado em Linux.
2. Crie um cluster baseado em Linux como um ambiente de teste/controle de qualidade. Para saber mais sobre como criar um cluster baseado em Linux, confira [Criar clusters Hadoop baseados em Linux em HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
3. Copie trabalhos, fontes de dados e coletores existentes para o novo ambiente.
4. Execute testes de validação para garantir que os trabalhos funcionem conforme o esperado no novo cluster.

Depois de verificar se tudo está funcionando conforme o esperado, agende o tempo de inatividade para a migração. Durante esse tempo de inatividade, execute as tarefas a seguir.

1. Faça backup de dados transitórios armazenados localmente em nós do cluster. Por exemplo, se você tiver dados armazenados diretamente em um nó principal.
2. Exclua o cluster baseado no Windows.
3. Crie um cluster baseado no Linux usando o mesmo repositório de dados padrão que o cluster baseado no Windows usava. O novo cluster pode continuar trabalhando nos dados de produção existentes.
4. Importe o backup de todos os dados transitórios.
5. Inicie os trabalhos/continue processando usando o novo cluster.

### <a name="copy-data-to-the-test-environment"></a>Copie os dados para o ambiente de teste.
Há vários métodos para copiar os dados e trabalhos. No entanto, os dois abordados nesta seção são os métodos mais simples para mover os arquivos diretamente para um cluster de teste.

#### <a name="hdfs-dfs-copy"></a>Cópia HDFS DFS

Use as etapas a seguir para copiar os dados do cluster de produção para o cluster de teste. Essas etapas usam o utilitário `hdfs dfs` incluído no HDInsight.

1. Encontre as informações sobre a conta de armazenamento e o contêiner padrão do cluster existente. O seguinte exemplo usa o PowerShell para recuperar estas informações:

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. Para criar um ambiente de teste, siga as etapas descritas no documento Criar clusters baseados em Linux no HDInsight. Pare antes de criar o cluster e escolha **Configuração Opcional**.
3. Na folha Configuração Opcional, escolha **Contas de Armazenamento Vinculadas**.
4. Escolha **Adicionar uma chave de armazenamento**e, quando solicitado, escolha a conta de armazenamento que foi retornada pelo script do PowerShell na etapa 1. Clique em **Selecionar** em cada folha. E por último, crie o cluster.
5. Assim que o cluster tiver sido criado, conecte-o usando **SSH.** Se não estiver familiarizado com o uso do SSH com o HDInsight, consulte um dos seguintes documentos:

   * [Usar o SSH (PuTTY) com o HDInsight baseado em Linux em clientes Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
   * [Usar o SSH com o HDInsight baseado em Linux no Linux, Unix, OS X e Bash no Windows 10](hdinsight-hadoop-linux-use-ssh-unix.md)

6. Na sessão SSH, use o comando a seguir para copiar arquivos da conta de armazenamento vinculada para a nova conta de armazenamento padrão. Substitua CONTÊINER pelas informações de contêiner retornadas pelo PowerShell. Substitua __CONTA__ pelo nome da conta. Substitua o caminho para dados pelo caminho para um arquivo de dados.

        hdfs dfs -cp wasbs://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location

    > [!NOTE]
    > Se a estrutura de diretório que contém os dados não existir no ambiente de teste, é possível criá-la usando o seguinte comando:

        hdfs dfs -mkdir -p /new/path/to/create

    A opção `-p` permite a criação de todos os diretórios no caminho.

#### <a name="direct-copy-between-azure-storage-blobs"></a>Cópia direta entre blobs do Armazenamento do Azure
Como alternativa, talvez seja conveniente usar o cmdlet `Start-AzureStorageBlobCopy` do Azure PowerShell para copiar blobs entre contas de armazenamento fora do HDInsight. Para saber mais, confira a seção Como gerenciar blobs do Azure em Usando o Azure PowerShell com o Armazenamento do Azure.

## <a name="client-side-technologies"></a>Tecnologias do lado do cliente
De modo geral, as tecnologias do lado do cliente, como os [cmdlets do Azure PowerShell](/powershell/azureps-cmdlets-docs), a [CLI do Azure](../xplat-cli-install.md) ou o [SDK do .NET para Hadoop](https://hadoopsdk.codeplex.com/), continuam funcionando do mesma forma com os clusters baseados em Linux, pois elas dependem das APIs REST que são as mesmas nos dois tipos de sistema operacional do cluster.

## <a name="server-side-technologies"></a>Tecnologias do lado do servidor
A tabela a seguir fornece instruções de como migrar componentes do lado servidor que sejam específicos do Windows.

| Se estiver usando esta tecnologia... | Execute esta ação... |
| --- | --- |
| **PowerShell** (scripts do lado do servidor, incluindo as Ações de Script usadas durante a criação do cluster) |Reescreva-os como scripts Bash. Para as Ações de Script, confira [Personalizar clusters HDInsight baseados em Linux usando as Ações de Script](hdinsight-hadoop-customize-cluster-linux.md) e [Desenvolvimento de ação de script com o HDInsight](hdinsight-hadoop-script-actions-linux.md). |
| **CLI do Azure** (scripts de servidor) |Embora a CLI do Azure esteja disponível no Linux, ela não vem pré-instalada nos nós principais do cluster HDInsight. Se você precisar dela para criar o script de servidor, confira [Instalar a CLI do Azure](../xplat-cli-install.md) para obter informações sobre a instalação em plataformas baseadas em Linux. |
| **Componentes do .NET** |O .NET não é totalmente compatível com todos os tipos de cluster HDInsight baseados em Linux. Os clusters Storm no HDInsight baseados em Linux criados depois de 28/10/2016 aceitam topologias Storm do C# usando a estrutura SCP.NET. O suporte adicional para o .NET será adicionado em futuras atualizações. |
| **Componentes do Win32 ou de outra tecnologia exclusiva do Windows** |As diretrizes dependem do componente ou da tecnologia. Você poderá encontrar uma versão compatível com Linux, talvez precise encontrar uma solução alternativa ou reescrever esse componente. |

## <a name="cluster-creation"></a>Criação do cluster
Esta seção fornece informações sobre as diferenças na criação do cluster.

### <a name="ssh-user"></a>Usuário do SSH
Os clusters do HDInsight baseados em Linux usam o protocolo **SSH (Secure Shell)** para fornecer acesso remoto aos nós do cluster. Ao contrário da Área de Trabalho Remota para clusters baseados no Windows, a maioria dos clientes SSH não fornece uma experiência de usuário gráfica, mas uma linha de comando que permite a execução de comandos no cluster. Alguns clientes (como a [MobaXterm](http://mobaxterm.mobatek.net/)) fornecem um navegador gráfico de sistema de arquivos além da linha de comando remota.

Durante a criação do cluster, você deve fornecer um usuário SSH e uma **senha** ou um **certificado de chave pública** para autenticação.

É recomendável usar um certificado de chave pública, pois é mais seguro do que usar uma senha. A autenticação de certificado funciona gerando um par de chaves pública/privada assinada e fornecendo a chave pública na criação do cluster. Ao se conectar ao servidor usando o SSH, a chave privada no cliente fornece autenticação para a conexão.

Para saber mais sobre como usar SSH com HDInsight, confira:

* [Usar SSH com o HDInsight em clientes Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
* [Usar SSH com HDInsight em clientes Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

### <a name="cluster-customization"></a>Personalização do cluster
**Ações de Script** usadas com clusters baseados em Linux devem ser escritas no script Bash. Embora as Ações de Script possam ser usadas durante a criação do cluster, em clusters baseados em Linux, elas também podem ser usadas na personalização depois que um cluster estiver ativo. Para obter mais informações, confira [Personalizar clusters HDInsight baseados em Linux usando as Ação de Script](hdinsight-hadoop-customize-cluster-linux.md) e [Desenvolvimento de ação de script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

Outro recurso de personalização é a **inicialização**. Em clusters Windows, esse recurso permite especificar a localização das bibliotecas adicionais para uso com o Hive. Após a criação do cluster, essas bibliotecas são automaticamente disponibilizadas para uso com as consultas do Hive sem a necessidade de usar o `ADD JAR`.

O recurso Inicialização para clusters baseados em Linux não fornece essa funcionalidade. Em vez disso, use a ação de script documentada em [Adicionar bibliotecas do Hive durante a criação do cluster](hdinsight-hadoop-add-hive-libraries.md).

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

De modo geral, antes de tomar alguma medida, você deve avaliar se um alerta vem ocorrendo por um longo período ou se espelha problemas do usuário que foram relatados.

## <a name="file-system-locations"></a>Locais do sistema de arquivos
O sistema de arquivos do cluster Linux é disposto de modo diferente dos clusters HDInsight baseados em Windows. Use a tabela a seguir para encontrar arquivos usados com frequência.

| Preciso encontrar... | Está localizado em... |
| --- | --- |
| Configuração |`/etc`. Por exemplo, `/etc/hadoop/conf/core-site.xml` |
| Arquivos de log |`/var/logs` |
| HDP (Hortonworks Data Platform) |`/usr/hdp`.Há dois diretórios localizados aqui: um que é a versão atual do HDP e `current`. O diretório `current` contém links simbólicos para arquivos e diretórios localizados no diretório do número de versão. O diretório `current` é fornecido como uma maneira conveniente de acessar arquivos do HDP, já que o número de versão muda conforme a versão do HDP é atualizada. |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

De modo geral, se você souber o nome do arquivo, será possível usar o comando a seguir em uma sessão do SSH para encontrar o caminho do arquivo:

    find / -name FILENAME 2>/dev/null

Você também pode usar caracteres curinga com o nome do arquivo. Por exemplo, `find / -name *streaming*.jar 2>/dev/null` retorna o caminho para os arquivos jar que contêm a palavra “transmissão” como parte do nome do arquivo.

## <a name="hive-pig-and-mapreduce"></a>Hive, Pig e MapReduce

Cargas de trabalho do Pig e do MapReduce são muito semelhantes em clusters baseados em Linux. A única diferença é a forma de conexão aos nós de cabeçalho do cluster. Para obter mais informações, consulte um dos seguintes documentos:

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
| Visual Studio para criar, implantar e gerenciar topologias híbridas ou do C# |O Visual Studio pode ser usado para criar, implantar e gerenciar topologias do C# (SCP.NET) ou híbridas em clusters Storm no HDInsight baseados em Linux criados após 28/10/2016. |

## <a name="hbase"></a>HBase
Em clusters baseados em Linux, o znode pai do HBase é `/hbase-unsecure`. Defina esse valor na configuração dos aplicativos cliente Java que usam a API nativa do Java para HBase.

Confira [Build a Java-based HBase application](hdinsight-hbase-build-java-maven.md) (Criar aplicativo HBase baseado em Java) para obter um exemplo de cliente que defina esse valor.

## <a name="spark"></a>Spark
Os clusters Spark estavam disponíveis em clusters do Windows durante a visualização. O Spark GA só está disponível em clusters baseados em Linux. Não há caminho de migração de um cluster da versão prévia do Spark baseado em Windows para um cluster de lançamento baseado em Linux.

## <a name="known-issues"></a>Problemas conhecidos
### <a name="azure-data-factory-custom-net-activities"></a>Atividades personalizadas do .NET no Azure Data Factory
Atualmente, atividades personalizadas do .NET no Azure Data Factory não são compatíveis com clusters HDInsight baseados em Linux. Sendo assim, você deve usar um dos métodos a seguir para implementar atividades personalizadas como parte do seu pipeline ADF.

* Execute atividades do .NET no pool do Lote do Azure. Veja a seção Usar serviço vinculado do Lote do Azure em [Usar atividades personalizadas em um pipeline do Azure Data Factory](../data-factory/data-factory-use-custom-activities.md)
* Implemente a atividade como uma atividade do MapReduce. Para obter mais informações, consulte [Invocar programas do MapReduce por meio do Data Factory](../data-factory/data-factory-map-reduce.md).

### <a name="line-endings"></a>Terminações de linha
No geral, as terminações de linha em sistemas baseados no Windows usam CRLF, enquanto os sistemas baseados no Linux usam LF. Se você produzir, ou esperar, dados com terminações de linha CRLF, talvez seja preciso modificar os produtores ou consumidores para trabalhar com a terminação de linha LF.

Por exemplo, usar o Azure PowerShell para consultar o HDInsight em um cluster baseado no Windows retorna dados com CRLF. A mesma consulta com um cluster baseado em Linux retorna LF. Você deve testar para ver se isso causa um problema na solução antes de migrar para um cluster baseado em Linux.

Caso tenha scripts que são executados diretamente nos nós do cluster do Linux, sempre use LF como o final da linha. Caso use CRLF, você poderá ver erros ao executar os scripts em um cluster baseado em Linux.

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

