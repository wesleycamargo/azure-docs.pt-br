<properties linkid="manage-services-hdinsight-hbase-provision-on-vnet" urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="Provision HBase clusters on Azure Virtual Network | Azure" metaKeywords="" description="Learn how to create HDInsight clusters on Azure Virtual Network." metaCanonical="" services="hdinsight" documentationCenter="" title="Provision HBase clusters on Azure Virtual Network" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao"></tags>

# Provisionar clusters do HBase na Rede Virtual do Azure

Saiba como criar clusters do HDInsight na Rede Virtual do Azure.

Com a integração da rede virtual, os clusters do HBase podem ser implantados na mesma rede virtual que seus aplicativos, de modo que os aplicativos possam se comunicar diretamente com o HBase. Os benefícios incluem:

-   Conectividade direta do aplicativo da web com os nós do cluster do HBase, que permite a comunicação usando APIs RPC Java do HBase.
-   Aprimore o desempenho evitando que o tráfego percorra diversos gateways e balanceadores de carga.
-   Processe informações confidenciais de maneira mais segura, sem expor um ponto de extremidade público

> [WACOM.NOTE]Atualmente, apenas clusters do HBase podem ser provisionados nas Redes Virtuais do Azure. Não há suporte para clusters do Hadoop.

## Neste artigo

-   [Pré-requisitos](#prerequisites)
-   [Provisionar clusters do HBase em uma rede virtual](#hbaseprovision)
-   [Conectar-se ao cluster do HBase provisionado em uma rede virtual usando as APIs RPC Java do HBase](#connect)
-   [Próximas etapas](#nextsteps)

## <span id="prerequisites"></span></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

-   **Uma assinatura do Azure**. O Azure é uma plataforma baseada em assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra][], [Ofertas para membros][] ou [Avaliação gratuita][].

-   **Uma estação de trabalho com o PowerShell do Azure instalado e configurado**. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][]. Para executar scripts do PowerShell, você deve executar o PowerShell do Azure como administrador e configurar a política de execução como *RemoteSigned*. Consulte [Executar scripts do Windows PowerShell][].

    Antes de executar scripts do PowerShell, verifique se você está conectado à sua assinatura do Azure usando o seguinte cmdlet:

        Add-AzureAccount

    Se você tiver várias assinaturas do Azure, use o seguinte cmdlet para definir a assinatura atual:

        Select-AzureSubscription <AzureSubscriptionName>

## <span id="hbaseprovision"></span></a>Provisionar um cluster do HBase em uma rede virtual.

**Para criar uma Rede Virtual usando o portal de gerenciamento:**

1.  Entre no [Portal de Gerenciamento do Azure][].
2.  No final da página, clique em **NOVO**, clique em **SERVIÇOS DE REDE**, clique em **REDE VIRTUAL** e, por fim, clique em **CRIAÇÃO RÁPIDA**.
3.  Digite ou selecione os valores a seguir:

    -   **Nome**: O nome da sua rede virtual.
    -   **Espaço de endereço**: Escolha um nome de endereço para a rede virtual que seja grande o suficiente para fornecer endereços para todos os nós do cluster. Caso contrário, a provisão falhará.
    -   **Contagem máxima de VMs**: Escolha uma das contagens máximas de VMs.
    -   **Local**: O local deve ser o mesmo que o do cluster do HBase que você criará.
    -   **Servidor DNS**: Este artigo usa o servidor DNS interno fornecido pelo Azure, então, você pode selecionar **Nenhum**. Configurações de rede mais avançadas com servidores DNS personalizados também são suportadas. Para obter orientações detalhadas, consulte [][]<http://msdn.microsoft.com/library/azure/jj156088.aspx></a>.

4.  Clique em **CRIAR UMA REDE VIRTUAL**. O nome da nova rede virtual aparecerá na lista. Aguarde até que a coluna de Status exiba **Criada**.
5.  No painel principal, clique na rede virtual que você acabou de criar.
6.  No topo da página, clique em **PAINEL**.
7.  Em **visão rápida**, anote a **ID DA REDE VIRTUAL**. Você precisará dela para provisionar o cluster do HBase.
8.  No topo da página, clique em **CONFIGURAR**.
9.  No final da página, o nome da sub-rede padrão é **Subnet-1**. Você também pode renomear a sub-rede ou adicionar uma nova sub-rede ao cluster do HBase. Anote o nome da sub-rede, você precisará dele para provisionar o cluster.
10. Verifique o **CIDR (CONTAGEM DE ENDEREÇOS)** para a sub-rede que será usada para o cluster. A contagem de endereços deve ser maior que o número de nós de trabalho mais sete (Gateway: 2, Headnode: 2, Zookeeper: 3). Por exemplo, se você precisa de um cluster do HBase de 10 nós, a contagem de endereços da sub-rede deve ser maior que 17 (10+7). Caso contrário, a implantação falhará.

    > [WACOM.NOTE] É altamente recomendado designar uma única sub-rede para um cluster.

11. Clique em **Salvar**, no final da página, se tiver atualizado os valores de sub-rede.

> [WACOM.NOTE] Os clusters do HDInsight usam o armazenamento de blob do Azure para armazenar dados. Para obter mais informações, consulte [Usar o armazenamento de blob do Azure com o Hadoop no HDInsight][]. Você precisará de uma conta de armazenamento e um contêiner de armazenamento de blob. O local da conta de armazenamento deve corresponder ao local da rede virtual e ao local do cluster.

**Para criar uma conta de Armazenamento do Azure e um contêiner de armazenamento Blob:**

1.  Entre no [Portal de Gerenciamento do Azure][].
2.  Clique em **NOVO** no canto inferior esquerdo, aponte para **SERVIÇOS DE DADOS**, aponte para **ARMAZENAMENTO** e, em seguida, clique em **CRIAÇÃO RÁPIDA**.

3.  Digite ou selecione os valores a seguir:

    -   **URL**: O nome da conta de armazenamento
    -   **LOCAL**: O local da conta de armazenamento. Certifique-se de que ele corresponda ao local da rede virtual. Não há suporte para grupos de afinidade.
    -   **REPLICAÇÃO**: Para fins de teste, use **Localmente redundante** para reduzir o custo.

4.  Clique em **CRIAR CONTA DE ARMAZENAMENTO**. Você verá a nova conta de armazenamento na lista de armazenamento.
5.  Aguarde até que o **STATUS** da nova conta de armazenamento seja alterado para **Online**.
6.  Clique na nova conta de armazenamento da lista para selecioná-la.
7.  Clique em **GERENCIAR CHAVES DE ACESSO** na parte inferior da página.
8.  Tome nota do **NOME DA CONTA DE ARMAZENAMENTO** e da **CHAVE DE ACESSO PRIMÁRIA** (ou a **CHAVE DE ACESSO SECUNDÁRIA**. As duas chaves funcionam). Você precisará deles mais tarde no tutorial.
9.  No topo da página, clique em **CONTÊINER**.
10. No final da página, clique em **ADICIONAR**.
11. Insira o nome do contêiner. Este contêiner será usado como contêiner padrão para este cluster do HBase. Por padrão, o nome do contêiner padrão corresponde ao nome do cluster. Mantenha o campo **ACESSO** como **Privado**.
12. Clique no ícone de seleção para criar o contêiner.

**Para provisionar um cluster do HBase usando o PowerShell do Azure:**

1.  Abra o ISE do PowerShell.
2.  Cope e cole o seguinte no painel de script.

        $hbaseClusterName = "<HBaseClusterName>"
        $hadoopUserName = "<HBaseClusterUsername>"
        $hadoopUserPassword = "<HBaseClusterUserPassword>"
        $location = "<HBaseClusterLocation>"  #i.e. "West US"
        $clusterSize = <HBaseClusterSize>  
        $vnetID = "<AzureVirtualNetworkID>"
        $subNetName = "<AzureVirtualNetworkSubNetName>"
        $storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
        $storageAccountKey = "<AzureStorageAccountKey>"
        $storageContainerName = "<AzureBlobStorageContainer>"

        $password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password) 

        New-AzureHDInsightCluster -Name $hbaseClusterName `
                                  -ClusterType HBase `
                                  -Version 3.1 `
                                  -Location $location `
                                  -ClusterSizeInNodes $clusterSize `
                                  -Credential $creds `
                                  -VirtualNetworkId $vnetID `
                                  -SubnetName $subNetName `
                                  -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                                  -DefaultStorageAccountKey $storageAccountKey `
                                  -DefaultStorageContainerName $storageContainerName

3.  Clique em **Executar Script** ou pressione **F5**.
4.  Para validar o cluster, você pode verificar o cluster no portal de gerenciamento ou executar o seguinte cmdlet do PowerShell no painel inferior:

    Get-AzureHDInsightCluster

Para testar o novo cluster do HBase, você pode usar os procedimentos encontrados em [Introdução ao uso do HBase com Hadoop no HDInsight][].

## <span id="connect"></span></a>Conectar-se ao cluster do HBase provisionado em uma rede virtual usando as APIs RPC Java do HBase

1.  Provisione uma máquina virtual IaaS na mesma rede virtual do Azure e na mesma sub-rede. De maneira que a máquina virtual e o cluster do HBase usem o mesmo servidor DNS interno para resolver nomes de host. Para fazer isso, você deve escolher a opção Da galeria e selecionar a rede virtual em vez de um datacenter. Para obter as instruções, consulte [Criar uma máquina virtual executando o Windows Server][]. Uma imagem padrão do Windows Server 2012 com uma VM pequena é suficiente.

2.  Obtenha o sufixo DNS específico da conexão do cluster do HBase. Para isso, realize um RDP no cluster do HBase (você estará conectado ao headnode) e execute **ipconfig** por meio de um prompt de comando. Para obter instruções de como habilitar o RDP e se conectar ao cluster usando RDP, consulte [Gerenciar clusters do Hadoop no HDInsight usando o Portal de Gerenciamento do Azure][].

    ![hdinsight.hbase.dns.surffix][]

3.  Altere a configuração de sufixo DNS primário da máquina virtual. Isto permite que a máquina virtual resolva automaticamente o nome de host do cluster HBase sem a especificação explícita do sufixo. Por exemplo, o nome de host *workernode0* será resolvido corretamente no workernode0 do cluster do HBase.
    Para alterar a configuração:

    1.  Realize um RDP na máquina virtual.
    2.  Abra o **Editor de Políticas de Grupo Local**. O executável é gpedit.msc.
    3.  Expanda a **Configuração do Computador**, expanda os **Modelos Administrativos**, expanda **Rede** e clique em **Cliente DNS**.
    4.  Defina **Sufixo DNS Primário** como o valor obtido na etapa 2:
	![hdinsight.hbase.primary.dns.suffix][]

    5.  Clique em **OK**.
    6.  Reinicie a máquina virtual.

    Agora, a máquina virtual está pronta para se comunicar com o cluster do HBase. Para testar a conexão, execute “ping headnode0” a partir da máquina virtual.

## <span id="nextsteps"></span></a>Próximas etapas

Neste tutorial, aprendemos como provisionar um cluster do HBase. Para obter mais informações, consulte:

-   [Introdução ao HDInsight][]
-   [Provisionar clusters do Hadoop no HDInsight][]
-   [Introdução ao uso do HBase com o Hadoop no HDInsight][Introdução ao uso do HBase com Hadoop no HDInsight]
-   [Analisar dados de sentimento no Twitter com o HBase no HDInsight][]
-   [Visão geral da Rede Virtual][].

  [Pré-requisitos]: #prerequisites
  [Provisionar clusters do HBase em uma rede virtual]: #hbaseprovision
  [Conectar-se ao cluster do HBase provisionado em uma rede virtual usando as APIs RPC Java do HBase]: #connect
  [Próximas etapas]: #nextsteps
  [Opções de compra]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Ofertas para membros]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Avaliação gratuita]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Instalar e configurar o PowerShell do Azure]: ../install-configure-powershell
  [Executar scripts do Windows PowerShell]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  []: http://msdn.microsoft.com/library/azure/jj156088.aspx
  [Usar o armazenamento de blob do Azure com o Hadoop no HDInsight]: ../hdinsight-use-blob-storage/
  [Introdução ao uso do HBase com Hadoop no HDInsight]: ../hdinsight-hbase-get-started/
  [Criar uma máquina virtual executando o Windows Server]: ../virtual-machines-windows-tutorial/
  [Gerenciar clusters do Hadoop no HDInsight usando o Portal de Gerenciamento do Azure]: ../hdinsight-administer-use-management-portal/#rdp
  [hdinsight.hbase.dns.surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
  [hdinsight.hbase.primary.dns.suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
  [Introdução ao HDInsight]: ../hdinsight-get-started/
  [Provisionar clusters do Hadoop no HDInsight]: ../hdinsight-provision-clusters/
  [Analisar dados de sentimento no Twitter com o HBase no HDInsight]: ../hdinsight-hbase-twitter-sentiment/
  [Visão geral da Rede Virtual]: http://msdn.microsoft.com/library/azure/jj156007.aspx
