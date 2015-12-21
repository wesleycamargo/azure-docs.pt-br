<properties
   pageTitle="Script do PowerShell para implantar o cluster de Pacote HPC | Microsoft Azure"
   description="Executar um script do Windows PowerShell para implantar um cluster de Pacote HPC completo nos serviços de infraestrutura do Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="big-compute"
   ms.date="09/29/2015"
   ms.author="danlep"/>

# Criar um cluster HPC (computação de alto desempenho) em VMs do Azure com o script de implantação do Pacote HPC IaaS

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.



Execute o script do PowerShell de implantação do Pacote HPC IaaS em um computador cliente para implantar um cluster Pacote HPC completo nos serviços de infraestrutura do Azure (IaaS). O script fornece várias opções de implantação e pode adicionar nós de computação de cluster que executam sistemas operacionais Windows Server ou distribuições Linux com suporte.

Dependendo de seu ambiente e opções, o script pode criar toda a infraestrutura de cluster, incluindo a rede virtual do Azure, as contas de armazenamento, os serviços de nuvem, o controlador de domínio, os bancos de dados SQL locais ou remotos, o nó principal, os nós do agente, os nós de computação e os nós do serviço de nuvem do Azure ("intermitentes" ou PaaS). Como alternativa, o script pode usar a infraestrutura do Azure já existente e criar o nó principal do cluster HPC, os nós agentes, os nós de computação e os nós intermitentes do Azure.


Para obter informações detalhadas sobre o planejamento de um cluster Pacote HPC, consulte o conteúdo de [Avaliação e planejamento de produto](https://technet.microsoft.com/library/jj899596.aspx) e de [Introdução](https://technet.microsoft.com/library/jj899590.aspx) na biblioteca de Pacote HPC do TechNet.

>[AZURE.NOTE]Você também pode usar um modelo do Gerenciador de Recursos do Azure para implantar um cluster Pacote HPC. Para obter um exemplo, consulte [Criar um cluster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/), [criar um cluster HPC com a imagem de um nó de computação personalizado](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/) ou [Criar um cluster HPC com nós de computação Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

## Pré-requisitos

* **Assinatura do Azure**: você pode usar a assinatura no serviço Azure Global ou no Azure China. Seus limites de assinatura afetarão o número e o tipo de nós de cluster que você pode implantar. Para obter informações, consulte [Limites, cotas e restrições de serviço e assinatura do Azure](../azure-subscription-service-limits.md).


* **Computador com Windows Client com o Azure PowerShell 0.8.7 ou posterior instalado e configurado**: consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). O script é executado no gerenciamento de serviço do Azure.


* **Script de implantação do Pacote HPC IaaS**: baixe e descompacte a versão mais recente do script no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Verifique a versão do script executando `New-HPCIaaSCluster.ps1 –Version`. Este artigo se baseia na versão 4.4.0 do script.

* **Arquivo de configuração do script**: você precisará criar um arquivo XML que usa o script para configurar o cluster HPC. Para obter informações e exemplos, consulte as seções neste artigo.


## Sintaxe

```
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
>[AZURE.NOTE]Você deve executar o script como um administrador.

### Parâmetros

* **ConfigFile**: especifica o caminho do arquivo de configuração para descrever o cluster HPC. Para saber mais, consulte [Arquivo de configuração](#Configuration-file) neste tópico ou o arquivo Manual.rtf na pasta que contém o script.

* **AdminUserName**: especifica o nome de usuário. Se a floresta de domínio for criada pelo script, isso se tornará o nome de usuário de administrador local para todas as VMs, bem como o nome do administrador do domínio. Se já existir a floresta de domínio, o usuário de domínio é especificado como o nome de usuário de administrador local para instalar o Pacote HPC.

* **AdminPassword**: especifica a senha do administrador. Se não estiver especificado na linha de comando, o script solicitará que você digite a senha.

* **HPCImageName** (opcional): especifica o nome de imagem da VM do Pacote HPC usada para implantar o cluster HPC. Ele deve ser uma imagem de Pacote HPC fornecida pela Microsoft no Azure Marketplace. Se não estiver especificado (recomendado na maioria dos casos), o script escolhe a imagem de Pacote HPC publicada mais recentemente.

    >[AZURE.NOTE]A implantação falhará se você não especificar uma imagem válida do Pacote HPC.

* **LogFile** (opcional): especifica o caminho de arquivo de log de implantação. Se não for especificado, o script criará um arquivo de log no diretório temporário do computador que executa o script.

* **Force** (opcional): suprime todas as solicitações de confirmação.

* **NoCleanOnFailure** (opcional): especifica que as VMs do Azure que não forem implantadas com êxito não serão removidas. Você deve remover essas VMs manualmente antes de executar novamente o script para continuar a implantação ou esta poderá falhar.

* **PSSessionSkipCACheck** (opcional): para cada serviço de nuvem com VMs implantadas por esse script, um certificado autoassinado é automaticamente gerado pelo Azure e todas as VMs no serviço de nuvem usam esse certificado como o certificado do Windows Remote Management (WinRM) padrão. Para implantar os recursos de HPC nessas VMs do Azure, o script por padrão instala temporariamente esses certificados no repositório de Autoridades de certificação raiz/Computador local do computador cliente para suprimir o erro de segurança "autoridade de certificação não confiável" durante a execução do script; os certificados são removidos quando o script termina. Se esse parâmetro for especificado, os certificados não serão instalados no computador cliente e o aviso de segurança será suprimido.

    >[AZURE.IMPORTANT]Esse parâmetro não é recomendado para implantações de produção.

### Exemplo

O exemplo a seguir cria um novo cluster Pacote HPC usando o arquivo de configuração MyConfigFile.xml e especifica as credenciais administrativas para instalar o cluster.

```
New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### Considerações adicionais

* O script usa a imagem de VM do Pacote HPC no Azure Marketplace para criar o nó principal do cluster. A imagem atual se baseia no Windows Server 2012 R2 Datacenter com Pacote HPC 2012 R2 Atualização 3 instalado.

* O script opcionalmente pode habilitar o envio de trabalho por meio do portal da Web Pacote HPC ou da API REST do Pacote HPC.


* O script pode opcionalmente executar scripts de pré e pós-configuração personalizados no nó principal se você quiser instalar o software adicional ou definir outras configurações.


## Arquivo de configuração

O arquivo de configuração para o script de implantação é um arquivo XML. O arquivo de esquema HPCIaaSClusterConfig.xsd está na pasta de script de implantação do Pacote HPC IaaS. **IaaSClusterConfig** é o elemento raiz do arquivo de configuração, que contém os elementos filhos descritos em detalhes no arquivo Manual.rtf na pasta de script de implantação. Para exemplos de arquivos para diferentes cenários, consulte [Arquivos de configuração de exemplo](#Example-configuration-files) neste artigo.

## Exemplo de arquivos de configuração

### Exemplo 1

O arquivo de configuração a seguir implanta um cluster Pacote HPC em uma floresta de domínio existente. O cluster tem um nó principal com bancos de dados locais e 12 nós de computação com a extensão de VM BGInfo aplicada. A instalação automática de atualizações do Windows está desabilitada para todas as VMs na floresta de domínio. Todos os serviços de nuvem são criados diretamente no local na Ásia Oriental. Os nós de computação são criados em 3 serviços em nuvem e em 3 contas de armazenamento (ou seja, MyHPCCN-0001 para MyHPCCN-0005 em MyHPCCNService01 e mycnstorage01; MyHPCCN-0006 para MyHPCCN0010 em MyHPCCNService02 e mycnstorage02; e MyHPCCN-0011 para MyHPCCN-0012 em MyHPCCNService03 e mycnstorage03). Os nós de computação são criados de uma imagem privada existente capturada de um nó de computação. O serviço de aumento e encolhimento automático está habilitado com intervalos padrão de aumentar e encolher.

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

### Exemplo 2

O arquivo de configuração a seguir implanta um cluster Pacote HPC em uma floresta de domínio existente. O cluster contém um nó principal, um servidor de banco de dados com um disco de dados de 500GB, dois nós agentes executando o sistema operacional Windows Server 2012 R2 e cinco nós de computação executando o sistema operacional Windows Server 2012 R2. O serviço de nuvem MyHPCCNService é criado no grupo de afinidades MyIBAffinityGroup e todos os outros serviços de nuvem são criados no grupo de afinidades MyAffinityGroup. A API REST do Agendador de trabalho do HPC e o portal da Web do HPC estão habilitados no nó principal.

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

### Exemplo 3

O arquivo de configuração a seguir cria uma nova floresta de domínio e implanta um cluster Pacote HPC que tem um nó principal com bancos de dados locais e 20 nós de computação Linux. Todos os serviços de nuvem são criados diretamente no local na Ásia Oriental. Os nós de computação Linux são criados em 4 serviços de nuvem e em 4 contas de armazenamento (ou seja, MyLnxCN-0001 para MyHPCCN-0005 em MyLnxCNService01 e mylnxstorage01, MyLnxCN-0006 para MyLnxCN-0010 em MyLnxCNService02 e mylnxstorage02, MyLnxCN-0011 MyLnxCN-0015 em MyLnxCNService03 e mylnxstorage03 e MyLnxCN-0016 para MyLnxCN-0020 em MyLnxCNService04 e mylnxstorage04). Os nós de computação são criados de uma imagem do Linux OpenLogic CentOS versão 7.0.

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
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>20</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
    <SSHKeyPairForRoot>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </SSHKeyPairForRoot>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```


### Exemplo 4

O arquivo de configuração a seguir implanta um cluster Pacote HPC que tem um nó principal com bancos de dados locais e cinco nós de computação executando o sistema operacional Windows Server 2008 R2. Todos os serviços de nuvem são criados diretamente no local na Ásia Oriental. O nó principal atua como controlador de domínio da floresta de domínio.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
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
    <OSVersion>WindowsServer2008R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### Exemplo 5

O arquivo de configuração a seguir implanta um cluster Pacote HPC em uma floresta de domínio existente. O cluster tem um nó principal com bancos de dados locais, dois modelos de nós do Azure são criados e três nós médios do Azure são criados para o modelo de nó do Azure AzureTemplate1. Um arquivo de script será executado no nó principal depois que este for configurado.

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
## Problemas conhecidos


* **Erro "VNet não existe"**: se você executar o script de implantação do Pacote HPC IaaS para implantar vários clusters no Azure simultaneamente em uma assinatura, uma ou mais implantações podem falhar com o erro "VNet *Nome\_VNet* não existe". Se esse erro ocorrer, execute novamente o script para a implantação com falha.

* **Problema ao acessar a Internet da rede virtual do Azure**: se você criar um cluster de Pacote HPC com um novo controlador de domínio usando o script de implantação, ou se promover manualmente uma máquina virtual a controlador de domínio, poderá enfrentar problemas ao conectar as máquinas virtuais na rede virtual do Azure à Internet. Isso pode ocorrer se um servidor DNS encaminhador for configurado automaticamente no controlador de domínio e não resolver corretamente.

    Para contornar esse problema, faça logon no controlador de domínio e remova a configuração do encaminhador ou configure um servidor DNS encaminhador válido. Para fazer isso, no Server Manager, clique em **Ferramentas** > **DNS** para abrir o Gerenciador DNS e clique duas vezes em **Encaminhadores**.

* **Problemas de acesso à rede RDMA de VMs tamanho A8 ou A9**: se você adicionar VMs de tamanho A8 ou A9 com nós de computação ou nós agentes do Windows Server usando o script de implantação, poderá enfrentar problemas para se conectar essas VMs à rede RDMA do aplicativo. Um dos motivos pode ser a extensão HpcVmDrivers não estar instalada corretamente quando as VMs de tamanho A8 ou A9 são adicionadas ao cluster. Por exemplo, a extensão pode estar presa no estado de instalação.

    Para contornar esse problema, verifique primeiro o estado da extensão nas VMs. Se a extensão não estiver instalada corretamente, tente remover os nós do cluster de HPC e adicioná-los novamente. Por exemplo, você pode adicionar VMs de nó de computação executando o script Add-HpcIaaSNode.ps1 no nó principal.


## Próximas etapas

* Tente executar uma carga de trabalho de teste no cluster. Para obter um exemplo, consulte o [guia de Introdução](https://technet.microsoft.com/library/jj884144) do Pacote HPC.

* Para tutoriais que usam o script para criar um cluster e executar uma carga de trabalho do HPC, consulte [Introdução com um cluster de Pacote HPC no Azure para executar cargas de trabalho do Excel e SOA](virtual-machines-excel-cluster-hpcpac), [Executar o NAMD com o HPC Pack da Microsoft em nós de computação do Linux no Azure](virtual-machines-linux-cluster-hpcpack-namd.md) ou [Executar o OpenFOAM com o Pacote HPC da Microsoft em nós de computação no Azure](virtual-machines-linux-cluster-hpcpack-openfoam.md).

* Experimente as ferramentas do Pacote HPC para iniciar, parar, adicionar e remover nós de computação de um cluster que você queira criar. Consulte [Gerenciar nós de computação em um cluster Pacote HPC no Azure](virtual-machines-hpcpack-cluster-node-manage.md)

<!---HONumber=AcomDC_1210_2015-->