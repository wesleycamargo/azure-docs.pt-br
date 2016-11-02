<properties
   pageTitle="Script do PowerShell para implantar o cluster HPC do Windows | Microsoft Azure"
   description="Executar um script do PowerShell para implantar um cluster de Pacote HPC do Windows nas máquinas virtuais do Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>


# <a name="create-a-windows-high-performance-computing-(hpc)-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Criar um cluster HPC (computação de alto desempenho) do Windows usando o script de implantação IaaS do HPC Pack

Execute o script do PowerShell de implantação do Pacote HPC IaaS para implantar um cluster HPC completo de cargas de trabalho do Windows nas máquinas virtuais do Azure. O cluster consiste em um nó principal associado do Active Directory que executa o Windows Server e o Pacote HPC da Microsoft, e os recursos de computação especificados. Se você desejar implantar um cluster de HPC Pack no Azure para cargas de trabalho do Linux, consulte [Criar um cluster de HPC Linux com o script de implantação do HPC Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Você também pode usar um modelo do Gerenciador de Recursos do Azure para implantar um cluster Pacote HPC. Para obter exemplos, confira [Criar um cluster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) e [Criar um cluster HPC com uma imagem do nó de computação personalizada](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>Exemplo de arquivos de configuração

Nos exemplos a seguir, substitua seus próprios valores para a ID de assinatura ou nome, e os nomes da conta e do serviço.

### <a name="example-1"></a>Exemplo 1

O arquivo de configuração a seguir implanta um cluster de Pacote HPC que tem um nó de cabeçalho com os bancos de dados locais e cinco nós de computação executando o sistema operacional do Windows Server 2012 R2. Todos os serviços de nuvem são criados diretamente no local do Oeste dos EUA. O nó principal atua como controlador de domínio da floresta de domínio.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>Exemplo 2

O arquivo de configuração a seguir implanta um cluster Pacote HPC em uma floresta de domínio existente. O cluster tem um nó principal com bancos de dados locais e 12 nós de computação com a extensão de VM BGInfo aplicada.
A instalação automática de atualizações do Windows está desabilitada para todas as VMs na floresta de domínio. Todos os serviços de nuvem são criados diretamente no local na Ásia Oriental. Os nós de computação são criados em três serviços de nuvem e três contas de armazenamento: _MyHPCCN-0001_ a _MyHPCCN-0005_ em _MyHPCCNService01_ e _mycnstorage01_; _MyHPCCN-0006_ a _MyHPCCN0010_ em _MyHPCCNService02_ e _mycnstorage02_; e _MyHPCCN-0011_ a _MyHPCCN-0012_ em _MyHPCCNService03_ e _mycnstorage03_). Os nós de computação são criados de uma imagem privada existente capturada de um nó de computação. O serviço de aumento e encolhimento automático está habilitado com intervalos padrão de aumentar e encolher.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
      </DomainController>
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>Exemplo 3

O arquivo de configuração a seguir implanta um cluster Pacote HPC em uma floresta de domínio existente. O cluster contém um nó de cabeçalho, um servidor de banco de dados com um disco de dados de 500 GB, dois nós agentes executando o sistema operacional Windows Server 2012 R2 e cinco nós de computação executando o sistema operacional Windows Server 2012 R2. O serviço de nuvem MyHPCCNService é criado no grupo de afinidades *MyIBAffinityGroup* e os outros serviços de nuvem são criados no grupo de afinidades *MyAffinityGroup*. A API REST do Agendador de trabalho do HPC e o portal da Web do HPC estão habilitados no nó principal.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>Exemplo 4

O arquivo de configuração a seguir implanta um cluster Pacote HPC em uma floresta de domínio existente. O cluster tem um nó de cabeçalho com os bancos de dados locais, dois modelos de nós do Azure são criados e três nós de tamanho Médio do Azure são criados para o modelo de nó do Azure _AzureTemplate1_. Um arquivo de script será executado no nó de cabeçalho depois que este for configurado.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>Solucionar problemas


* **Erro "VNet não existe"**: se você executar o script para implantar vários clusters no Azure simultaneamente em uma assinatura, uma ou mais implantações poderão falhar com o erro *"VNet \_Nome*VNet não existe".
Se esse erro ocorrer, execute novamente o script para a implantação com falha.

* **Problema ao acessar a Internet na rede virtual do Azure** – Se você criar um cluster com um novo controlador de domínio usando o script de implantação ou se promover manualmente uma VM do nó de cabeçalho ao controlador de domínio, poderá ter problemas ao conectar as VMs à Internet. Esse problema poderá ocorrer se um servidor DNS encaminhador for configurado automaticamente no controlador de domínio e não resolver corretamente.

    Para contornar esse problema, faça logon no controlador de domínio e remova a configuração do encaminhador ou configure um servidor DNS encaminhador válido. Para definir essa configuração, no Gerenciador do Servidor, clique em **Ferramentas** >
    **DNS** para abrir o Gerenciador DNS e clique duas vezes em **Encaminhadores**.

* **Problemas de acesso à rede RDMA de VMs com computação intensiva** – se você adicionar VMs de nó de agente ou de computação do Windows Server usando um tamanho com capacidade para RDMA como A8 ou A9, você poderá enfrentar problemas para se conectar essas VMs à rede RDMA do aplicativo. Um dos motivos pelos quais esse problema ocorre é a extensão HpcVmDrivers não estar corretamente instalada quando as VMs são adicionadas ao cluster. Por exemplo, a extensão pode estar presa no estado de instalação.

    Para contornar esse problema, verifique primeiro o estado da extensão nas VMs. Se a extensão não estiver instalada corretamente, tente remover os nós do cluster de HPC e adicioná-los novamente. Por exemplo, você pode adicionar VMs de nó de computação executando o script Add-HpcIaaSNode.ps1 no nó principal.
    
## <a name="next-steps"></a>Próximas etapas

* Tente executar uma carga de trabalho de teste no cluster. Para obter um exemplo, consulte o [guia de Introdução](https://technet.microsoft.com/library/jj884144)do Pacote HPC.

* Para ver um tutorial sobre como usar script para implantação de um cluster e executar uma carga de trabalho do HPC, veja [Introdução a um cluster de Pacote HPC no Azure para executar cargas de trabalho do Excel e da SOA](virtual-machines-windows-excel-cluster-hpcpack.md).

* Experimente as ferramentas do Pacote HPC para iniciar, parar, adicionar e remover nós de computação de um cluster que você queira criar. Consulte [Gerenciar nós de computação em um cluster de Pacote HPC no Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md).

* Para se preparar para enviar trabalhos para o cluster a partir de um computador local, confira [Enviar trabalhos HPC de um computador local para um cluster de Pacote HPC no Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).



<!--HONumber=Oct16_HO2-->


