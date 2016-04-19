<properties
   pageTitle="Script do PowerShell para implantar o cluster HPC do Linux | Microsoft Azure"
   description="Executar um script do PowerShell para implantar um cluster HPC Pack do Linux nos serviços de infraestrutura do Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="big-compute"
   ms.date="04/05/2016"
   ms.author="danlep"/>

# Criar um cluster HPC (computação de alto desempenho) do Linux usando o script de implantação IaaS do HPC Pack

Execute o script do PowerShell de implantação do Pacote HPC IaaS em um computador cliente para implantar um cluster Pacote HPC completo para cargas de trabalho do Linux nos IaaS (Serviços de Infraestrutura do Azure). Se você desejar implantar um cluster de HPC Pack no Azure para cargas de trabalho do Linux, consulte [Criar um cluster de HPC Windows com o script de implantação do HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## Exemplo de arquivos de configuração

### Exemplo 1

O arquivo de configuração a seguir cria uma nova floresta de domínio e implanta um cluster Pacote HPC que tem um nó principal com bancos de dados locais e 20 nós de computação Linux. Todos os serviços de nuvem são criados diretamente no local na Ásia Oriental. Os nós de computação Linux são criados em quatro serviços de nuvem e em quatro contas de armazenamento (ou seja, _MyLnxCN-0001_ para _MyLnxCN-0005_ em _MyLnxCNService01_ e _mylnxstorage01_, _MyLnxCN-0006_ para _MyLnxCN-0010_ em _MyLnxCNService02_ e _mylnxstorage02_, _MyLnxCN-0011_ para _MyLnxCN-0015_ em _MyLnxCNService03_ e _mylnxstorage03_ e _MyLnxCN-0016_ para _MyLnxCN-0020_ em _MyLnxCNService04_ e _mylnxstorage04_). Os nós de computação são criados de uma imagem do Linux OpenLogic CentOS versão 7.0.

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
## Solucionar problemas

* **Erro "VNet não existe"**: se você executar o script de implantação do Pacote HPC IaaS para implantar vários clusters no Azure simultaneamente em uma assinatura, uma ou mais implantações podem falhar com o erro "VNet *Nome\_VNet* não existe". Se esse erro ocorrer, execute novamente o script para a implantação com falha.

* **Problema ao acessar a Internet da rede virtual do Azure** - Se você criar um cluster de Pacote HPC com um novo controlador de domínio usando o script de implantação, ou se promover manualmente uma VM ao controlador de domínio, poderá enfrentar problemas ao conectar as VMs na rede virtual do Azure à Internet. Isso pode ocorrer se um servidor DNS encaminhador for configurado automaticamente no controlador de domínio e não resolver corretamente.

    Para contornar esse problema, faça logon no controlador de domínio e remova a configuração do encaminhador ou configure um servidor DNS encaminhador válido. Para fazer isso, no Server Manager, clique em **Ferramentas** > **DNS** para abrir o Gerenciador DNS e clique duas vezes em **Encaminhadores**.
    
## Próximas etapas

* Para obter tutoriais que usam o script para criar um cluster e executar uma carga de trabalho do HPC no Linux, veja [Executar o NAMD com o Microsoft HPC Pack em nós de computação do Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md) ou [Executar o OpenFOAM com o Microsoft HPC Pack em nós de computação do Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md).

* Você também pode usar um modelo do Gerenciador de Recursos do Azure para implantar um cluster Pacote HPC. Para obter um exemplo, consulte [Criar um cluster HPC com nós de computação Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

<!---HONumber=AcomDC_0406_2016-->