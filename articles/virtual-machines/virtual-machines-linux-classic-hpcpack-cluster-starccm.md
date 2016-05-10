<properties
 pageTitle="Executar o StarCCM+ com o HPC Pack nas VMs do Linux | Microsoft Azure"
 description="Implante um cluster do Microsoft HPC Pack no Azure e execute um trabalho do StarCCM+ em vários nós de computação do Linux em uma rede RDMA."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="xpillons"
 manager="kateh"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="04/13/2016"
 ms.author="xpillons"/>

# Executar o StarCCM+ com o Microsoft HPC Pack em um cluster de RDMA do Linux no Azure
Este artigo mostra como implantar um cluster Microsoft HPC Pack no Azure e executar um trabalho [CD-Adapco StarCCM+](http://www.cd-adapco.com/products/star-ccm%C2%AE) em vários nós de computação Linux interconectados com Infiniband.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

O Microsoft HPC Pack fornece recursos para executar uma variedade de aplicativos de HPC e paralelos em larga escala, incluindo aplicativos MPI, em clusters de máquinas virtuais do Microsoft Azure. O HPC Pack também dá suporte a aplicativos de HPC no Linux em VMs com nós de computação do Linux implantadas em um cluster do HPC Pack. Consulte a [Introdução aos nós de computação do Linux em um cluster do HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md) para saber como começar a usar os nós de computação do Linux com o HCP Pack.

## Instalar o cluster HPC Pack
Baixe os scripts de implantação de IaaS do HPC Pack [daqui](https://www.microsoft.com/pt-BR/download/details.aspx?id=44949) e extraia-os localmente.

O Azure PowerShell é um pré-requisito. Leia este artigo [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) se não estiver configurado no computador local.

No momento da redação deste artigo, as imagens do Linux da Galeria do Azure que contém os drivers Infiniband do Azure são para SLES 12, CentOS 6.5 e CentOS 7.1. Este artigo baseia-se no uso do SLES 12. Para recuperar o nome de todas as imagens do Linux com suporte à HPC na galeria, você pode executar o seguinte comando do PowerShell:

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

A saída lista o local em que essas imagens estão disponíveis e o **ImageName** a ser usado no modelo de implantação posteriormente. Antes de implantar o cluster, você precisa criar um arquivo de modelo de implantação do HPC Pack e porque visamos um pequeno cluster, o nó principal será o controlador de domínio e hospedará um Banco de Dados SQL local. O modelo abaixo implantará o HeadNode, criará um arquivo xml chamado **MyCluster.xml** e substituirá os valores de **SubscriptionId**, **StorageAccount**, **Location**, **VMName** e **ServiceName** pelos seus.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Inicie a criação do HeadNode executando o comando do PowerShell em uma janela de comando com privilégios elevados:

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Depois de 20 a 30 minutos o HeadNode deve estar pronto. Você pode se conectar a ele do portal do Azure clicando no ícone Conectar da máquina virtual.

Finalmente, você poderá precisar corrigir o encaminhador DNS. Para fazer isso, inicie o Gerenciador DNS.

1.  Clique com o botão direito do mouse no nome do servidor no Gerenciador DNS, selecione Propriedades e selecione a guia Encaminhadores

2.  Clique no botão Editar para remover todos os encaminhadores e clique em OK

3.  Certifique-se de que a caixa de seleção "Usar dicas de raiz se encaminhadores não estiverem disponíveis" esteja marcada e clique em OK

## Configurar nós de computação do Linux
A implantação dos nós de computação do Linux é feita por meio exatamente do mesmo modelo de implantação usado acima para criar o HeadNode. Copie o arquivo **MyCluster.xml** do computador local para o HeadNode e atualize a marcação **NodeCount** com o número de nós que você deseja implantar (<=20). Tenha cuidado para ter núcleos suficientes disponíveis na sua cota do Azure, uma vez que cada instância A9 consumirá 16 núcleos em sua assinatura. Você poderá usar instâncias A8 (8 núcleos) em vez de A9 se quiser usar mais VMs no mesmo orçamento.

No nó principal, copie os scripts de implantação de IaaS do HPC Pack.

Abra um Azure PowerShell em uma janela de comando com privilégios elevados:

1.  Execute **Add-AzureAccount** para se conectar à sua assinatura do Azure.

2.  Se você tiver várias assinaturas, execute **Get-AzureSubscription** para listá-las

3.  Defina uma padrão executando o comando **Select-AzureSubscription -SubscriptionName xxxx -Default**

4.  Execute **.\\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml** para iniciar a implantação de nós de computação do Linux. ![Implantação do HeadNode em ação][hndeploy]

Abra a ferramenta de Gerenciador de Cluster do HPC Pack, após alguns minutos, os nós de computação do Linux aparecerão regularmente na lista de nós de computação do cluster. Com o modo de implantação clássico, as VMs de IaaS são criadas sequencialmente, portanto, se o número de nós for importante, poderá levar uma quantidade de tempo significativa para que todos sejam implantados. ![Nós Linux no gerenciador de cluster do HPC Pack][clustermanager]

Agora que todos os nós no cluster estão em execução, há configurações de infraestrutura adicionais a serem feitas.

## Configurar o compartilhamento de arquivos do Azure para nós do Windows e Linux
Para facilitar, usamos Arquivos do Azure que fornecem funcionalidades de CIFS nos Blobs do Azure como um repositório persistente para armazenar nossos scripts, pacotes de aplicativos e arquivos de dados. Lembre-se de que essa não é a solução mais escalonável, mas é a mais simples e não requer VMs dedicadas.

Crie um compartilhamento de arquivos do Azure seguindo as instruções disponíveis no artigo [Get started with Azure File storage on Windows](..\storage\storage-dotnet-how-to-use-files.md) (Introdução ao armazenamento de arquivos do Azure no Windows)

Mantenha o nome da sua conta de armazenamento **saname**, o nome do compartilhamento de arquivo **sharename** e a chave da conta de armazenamento **sakey**.

### Montar o compartilhamento de arquivos do Azure no nó principal
Abra um prompt de comandos com privilégios elevados e execute o seguinte comando para armazenar as credenciais no cofre do computador local

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Em seguida, para montar o compartilhamento de arquivos do Azure, execute

```
    net use Z: \<saname>.file.core.windows.net<sharename> /persistent:yes
```

### Montar o compartilhamento de arquivos do Azure em nós de computação do Linux
Uma ferramenta útil que é fornecida com o HPC Pack é o utilitário do clusrun. Essa linha de comando permite executar o mesmo comando simultaneamente em um conjunto de nós de computação. Em nosso caso, ele é usado para montar o compartilhamento de arquivos do Azure e mantê-la para sobreviver às reinicializações. Em uma janela de comandos com privilégios elevados no HeadNode, execute os comandos a seguir.

Para criar o diretório de montagem.

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Para montar o compartilhamento de arquivos do Azure

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

Para manter o compartilhamento de montagem

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## Atualizar os drivers do Linux
Finalmente, você pode precisar atualizar os drivers Infiniband dos nós de computação do Linux. Leia este artigo para saber se e como fazê-lo [Update the Linux RDMA drivers for SLES 12](virtual-machines-linux-classic-rdma-cluster.md/#update-the-linux-rdma-drivers-for-sles-12) (Atualizar os drivers de RDMA do Linux para SLES 12)

## Instalar o StarCCM +
As instâncias de VM do Azure A8 e A9 dão suporte ao Infiniband e funcionalidades de RDMA. Os drivers de kernel que habilitam essas funcionalidades estão disponíveis no momento da redação para imagens do Windows 2012 R2 e SUSE 12 na Galeria do Azure, o CentOS 7. x estará disponível em breve. O Microsoft MPI e Intel MPI (versão 5. x) são as duas bibliotecas MPI que dão suporte a esses drivers no Azure.

O CD-Adapco StarCCM+ versão 11.x e superior é fornecido com o Intel MPI versão 5.x, portanto o suporte do Infiniband para Azure está incluído.

Obtenha o pacote do Linux64 StarCCM+ do [portal do CD-Adapco](https://steve.cd-adapco.com). Em nosso caso, usamos a versão 11.02.010 na precisão mista.

No HeadNode, no compartilhamento de arquivos do Azure **/hpcdata**, crie um script de shell chamado **setupstarccm.sh** com o conteúdo a seguir. Esse script será iniciado em cada nó de computação para instalar o StarCCM+ localmente.

#### Script setupstarcm.sh de exemplo
```
    #!/bin/bash
    # setupstarcm.sh setup StarCCM+ locally

    # create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # copy the starccm package from the file share to the local dir
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # start a silent installation of starccm without flexlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Agora, para instalar o StarCCM+ em todos os seus nós de computação do Linux, abra uma janela de comando com privilégios elevados e execute o seguinte comando

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Enquanto o comando é executado, você pode monitorar o uso da CPU com o heatmap do Gerenciador de Cluster. Depois de alguns minutos, todos os nós devem estar instalados corretamente.

## Executando trabalhos do StarCCM+
O HPC Pack é usado por suas funcionalidades de agendador de trabalho para executar trabalhos do StarCCM+. Para fazer isso, precisamos do suporte de alguns scripts que são usados para iniciar o trabalho e executar o StarCCM+. Os dados de entrada são mantidos no compartilhamento de arquivos do Azure primeiro pela simplicidade. O script Powershell a seguir é usado para enfileirar um trabalho do StarCCM+. Ele usa três argumentos:

*  O nome do modelo
*  O número de nós a ser usado
*  O número de núcleos em cada nó a ser usado

Como o StarCCM+ pode maximizar a largura de banda da memória, normalmente é melhor usar menos núcleos por nó de computação e adicionar novos nós. A proporção exata de núcleos por nó dependerá da família de processadores e da velocidade de interconexão.

Os nós são alocados exclusivamente para o trabalho e não podem ser compartilhados com outros trabalhos. Como você pode observar, o trabalho não será iniciado como um trabalho MPI diretamente, o iniciador MPI será iniciado no script de shell **runstarccm.sh**.

O modelo de entrada e o **runstarccm.sh** devem ser armazenados no compartilhamento **/hpcdata** montado anteriormente.

Os arquivos de log são nomeados com a ID do trabalho e são armazenados no **compartilhamento /hpcdata**, bem como os arquivos de saída do StarCCM+.


#### Script SubmitStarccmJob.ps1 de exemplo
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # create a new job, this will give us the JobId used to identify the name of the uploaded package in azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # submit job 	
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Substitua o **runner.java** por seu código de registro em log e iniciador de modelo java do StarCCM+ preferidos.

#### Script runstarccm.sh de exemplo
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
    	echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
    	let "I=${I}+2"
    	let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run the starccm with hostfile arg
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
    	-power -podkey "<yourkey>" -rsh ssh \
    	-mpi intel -fabric UDAPL -cpubind bandwidth,v \
    	-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
    	-batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

Em nosso teste, estamos usando um token de licença de energia sob demanda para o qual você precisa definir a variável de ambiente **$CDLMD\_LICENSE\_FILE** como ****1999@flex.cd-adapco.com** e a chave na opção **-podkey** da linha de comando.

Após uma inicialização, o script extrai do **CCP\_NODES\_CORES $** as variáveis de ambiente definidas pelo HPC Pack, a lista de nós para criar um arquivo de host usado pelo iniciador do MPI. Esse arquivo de host conterá a lista de nomes dos nós de computação usados para o trabalho, um nome por linha.

O formato de **$CCP\_NODES\_CORES** segue este padrão:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

onde

* `<Number of nodes>`: o número de nós alocados para esse trabalho.  
* `<Name of node_n_...>`: o nome de cada nó alocado para esse trabalho.
* `<Cores of node_n_...>`: o número de núcleos no nó alocado para esse trabalho.

O número de núcleos **$NBCORES** também é calculado com base no número de nós **$NBNODES** e o número de núcleos por nó fornecido como parâmetro **$NBCORESPERNODE**.

Para as opções de MPI, as usadas com o Intel MPI no Azure são:

*   -mpi intel => para especificar o IntelMPI
*   -fabric UDAPL => para usar Verbos do Infiniband do Azure
*   -cpubind bandwidth,v => isso é para otimizar a largura de banda para o MPI com StarCCM+
*   -mppflags "-ppn $NBCORESPERNODE -genv I\_MPI\_DAPL\_PROVIDER=ofa-v2-ib0 -genv I\_MPI\_DAPL\_UD=0 -genv I\_MPI\_DYNAMIC\_CONNECTION=0" => essas são configurações do Intel MPI para trabalhar com o Infiniband do Azure e também define o número necessário de núcleos por nó
*   -batch => para iniciar o StarCCM+ no modo de lote sem nenhuma interface do usuário


Por fim, para iniciar um trabalho, certifique-se de que os nós estejam em funcionamento e estejam online no Gerenciador de Cluster. Em seguida, de uma janela de comando do Powershell execute isso:

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## Parando os nós
No futuro, depois de concluir os testes, para parar e iniciar os nós, você pode usar os seguintes comandos do Powershell do HPC Pack:

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## Próximas etapas
Tente executar outras cargas de trabalho do Linux, por exemplo, veja:

* [Executar o NAMD com o Microsoft HPC Pack em nós de computação do Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md).

* [Executar o OpenFOAM com o Microsoft HPC Pack em um cluster de RDMA do Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md).


<!--Image references-->
[hndeploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/hndeploy.png
[clustermanager]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/ClusterManager.png

<!---HONumber=AcomDC_0427_2016-->