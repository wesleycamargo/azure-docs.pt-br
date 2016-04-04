<properties
 pageTitle="VMs de computação do Linux em um cluster de HPC Pack | Microsoft Azure"
 description="Saiba como fazer um script de implantação de um cluster de HPC Pack no Azure que contenha um nó principal que executa o Windows Server com nós de computação Linux."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="12/02/2015"
 ms.author="danlep"/>

# Introdução a nós de computação Linux em um cluster de HPC Pack no Azure

Este artigo mostra como usar um script do Azure PowerShell para configurar um cluster do Microsoft HPC Pack no Azure que contenha um nó de cabeçalho que executa o Windows Server e vários nós de computação que executam uma distribuição do Linux. Também vamos mostrar várias maneiras de mover os arquivos de dados para nós de computação Linux. Você pode usar este cluster para executar cargas de trabalho do HPC Linux no Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


Em um alto nível, o diagrama a seguir mostra o cluster de HPC Pack que você criará.

![Cluster de HPC com nós Linux][scenario]

## Implantar um cluster de HPC Pack com nós de computação Linux

Você usará o script de implantação do Microsoft HPC Pack IaaS (**New-HpcIaaSCluster.ps1**) para automatizar a implantação do cluster nos serviços de infraestrutura do Azure (IaaS). Esse script do Azure PowerShell usa uma imagem de VM do HPC Pack no Azure Marketplace para implantação rápida e fornece um conjunto abrangente de parâmetros de configuração para tornar a implantação fácil e flexível. O script implanta a rede virtual do Azure, as contas de armazenamento, os serviços de nuvem, o controlador de domínio, o servidor de banco de dados do SQL Server separado opcional, o nó principal do cluster, os nós de computação, os nós do agente, os nós do Azure PaaS ("intermitência") e os nós de computação Linux (suporte para Linux introduzido no [HPC Pack 2012 R2 Atualização 2](https://technet.microsoft.com/library/mt269417.aspx)).

Para obter uma visão geral das opções de implantação de cluster do HPC Pack, confira o [Guia de introdução do HPC Pack 2012 R2 e do HPC Pack 2012](https://technet.microsoft.com/library/jj884144.aspx) e as [Opções para criar e gerenciar um cluster de HPC (computação de alto desempenho ) no Azure com o Microsoft HPC Pack](virtual-machines-linux-hpcpack-cluster-options.md).

### Pré-requisitos

* **Computador cliente** – você precisa de um computador cliente com Windows para executar o script de implantação do cluster.

* **Azure PowerShell** - [Instale e configure o Azure PowerShell](../powershell-install-configure.md) (versão 0.8.10 ou posterior) no computador cliente.

* **Script de implantação de IaaS do HPC Pack** – baixe e descompacte a versão mais recente do script no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Verifique a versão do script ao executar `New-HPCIaaSCluster.ps1 –Version`. Este artigo se baseia na versão 4.4.0 ou posterior do script.

* **Assinatura do Microsoft Azure** – você pode usar a assinatura no serviço Azure Global ou Azure China. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Cota para núcleos** – talvez seja necessário aumentar a cota de núcleos, especialmente se você optar por implantar vários nós de cluster com tamanhos de VM de vários núcleos. No exemplo neste artigo, você precisará de pelo menos 12 núcleos. Para aumentar a cota, [abra uma solicitação de atendimento ao cliente online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitamente.

### Criar o arquivo de configuração

O script de implantação do HPC Pack IaaS usa um arquivo de configuração XML como entrada que descreve a infraestrutura do cluster do HPC. Para implantar um cluster pequeno que consiste em um nó principal e dois nós de computação Linux, substitua os valores para o seu ambiente no seguinte arquivo de configuração de exemplo. Para obter mais informações sobre o arquivo de configuração, veja o arquivo Manual.rtf na pasta scripts e [Criar um cluster HPC com o script de implantação de IaaS do HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
  <VMSize>A4</VMSize>
  <EnableRESTAPI />
  <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

Aqui estão as breves descrições dos elementos no arquivo de configuração.

* **IaaSClusterConfig** – o elemento raiz do arquivo de configuração.

* **Assinatura** – a assinatura do Azure usada para implantar o cluster HPC Pack. Use o comando abaixo para verificar se o nome da assinatura do Azure está configurado e é exclusivo em seu computador cliente. Neste exemplo, usamos a assinatura do Azure "Assinatura-1".

    ```
    PS > Get-AzureSubscription –SubscriptionName <SubscriptionName>
    ```

    >[AZURE.NOTE]Como alternativa, use a ID da assinatura para especificar a assinatura que deseja utilizar. Consulte o arquivo Manual.rtf na pasta do script.

* **StorageAccount** - todos os dados persistentes para o cluster HPC Pack serão armazenados na conta de armazenamento especificada (allvhdsje neste exemplo). Se a conta de armazenamento não existir, o script vai criá-la na região especificada em **Local**.

* **Local** - região do Azure onde você implantará o cluster HPC Pack (Leste do Japão neste exemplo).

* **Rede Virtual** – as configurações da rede virtual e da sub-rede em que o cluster HPC será criado. Você pode criar a rede virtual e sub-redes por conta própria antes de executar esse script ou o script cria uma rede virtual com espaço de endereço 192.168.0.0/20 e sub-rede com espaço de endereço 192.168.0.0/23. Neste exemplo, o script cria a rede virtual centos7rdmavnetje e a sub-rede CentOS7RDMACluster.

* **Domínio** – configurações de domínio do Active Directory para o cluster HPC Pack. Todas as VMs do Windows criadas pelo script ingressarão no domínio. Atualmente, o script oferece suporte a três opções de domínio: ExistingDC, NewDC e HeadNodeAsDC. Neste exemplo, configuramos o nó de cabeçalho como o controlador de domínio com um nome de domínio totalmente qualificado de hpc.local.

* **Banco de dados** – configurações do banco de dados para o cluster do HPC Pack. Atualmente, o script oferece suporte a três opções de banco de dados: ExistingDB, NewRemoteDB e LocalDB. Neste exemplo, criamos um banco de dados local no nó de cabeçalho.

* **HeadNode** – configurações para o nó de cabeçalho do HPC Pack. Neste exemplo, criamos um nó de cabeçalho de tamanho A7 chamado CentOS7RDMA-HN no serviço de nuvem centos7rdma-je. Para dar suporte ao envio de trabalho do HPC de computadores cliente remotos (que não ingressaram no domínio), o script habilita a API REST do agendador de trabalhos e o portal da Web do HPC.

* **LinuxComputeNodes** – configurações para os nós de computação do HPC Pack Linux. Neste exemplo, criamos dois nós de computação Linux CentOS 7 tamanho A7 (CentOS7RDMA-LN1 e CentOS7RDMA-LN2) no serviço de nuvem centos7rdma-je.

### Considerações adicionais para nós de computação Linux

* Atualmente, o HPC Pack suporta as seguintes distribuições Linux para nós de computação: Ubuntu Server 14.10, CentOS 6.6, CentOS 7.0 e SUSE Linux Enterprise Server 12.

* O exemplo neste artigo usa uma versão específica do CentOS disponível no Azure Marketplace para criar o cluster. Se preferir usar outras imagens disponíveis, use o cmdlet **get-azurevmimage** do Azure PowerShell para encontrar a que você precisa. Por exemplo, para listar todas as imagens do CentOS 7.0, execute o seguinte comando:

    ```
    get-azurevmimage | ?{$_.Label -eq "OpenLogic 7.0"}
    ```

    Encontre a que você precisa e substitua o valor de **ImageName** no arquivo de configuração.

* Imagens do Linux que oferecem suporte à conectividade RDMA para VMs de tamanho A8 e A9 estão disponíveis. Se você especificar uma imagem com drivers de RDMA Linux instalados e habilitados, o script de implantação do HPC Pack IaaS irá implantá-los. Por exemplo, especifique o nome da imagem `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` para o SUSE Linux Enterprise Server 12 atual – otimizado para imagem de computação de alto desempenho no Marketplace.

* Para habilitar o RDMA Linux nas VMs do Linux criadas a partir de imagens compatíveis para executar trabalhos MPI, instale e configure uma biblioteca específica de MPI em nós do Linux, após a implantação de cluster de acordo com o que o seu aplicativo precisa. Para ver um exemplo, confira [Executar o OpenFOAM com o Microsoft HPC Pack em um cluster de RDMA do Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md).

* Lembre-se de que implantar todos os nós do RDMA Linux em um serviço de modo que a conexão de rede RDMA funcione entre os nós.


## Execute o script de implantação do HPC Pack IaaS

1. Abra o console do PowerShell no computador cliente como administrador.

2. Altere o diretório para a pasta de scripts (E:\\IaaSClusterScript neste exemplo).

    ```
    cd E:\IaaSClusterScript
    ```

3. Execute o comando a seguir para implantar o cluster de HPC Pack. Este exemplo supõe que o arquivo de configuração esteja localizado em E:\\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

    O script gera um arquivo de log automaticamente, pois o parâmetro **-LogFile** não está especificado. Os logs não são gravados em tempo real, mas coletados no final da validação e da implantação. Portanto, se o processo do PowerShell for interrompido enquanto o script está sendo executado, alguns registros serão perdidos.

    a. Como a **AdminPassword** não é especificada no comando acima, você deverá inserir a senha para o usuário *MyAdminName*.

    b. Em seguida, o script começa a validar o arquivo de configuração. Demora de dezenas de segundos a vários minutos dependendo da conexão de rede.

    ![Validação][validate]

    c. Depois de passarem as validações, o script lista os recursos que serão criados para o cluster HPC. Digite *y* para continuar.

    ![Recursos][resources]

    d. O script começa a implantar o cluster de HPC Pack e concluirá a configuração sem outras etapas manuais. Isso pode levar vários minutos.

    ![Implantar][deploy]

4. Após a configuração ser concluída com êxito, a Área de Trabalho Remota para o nó principal e abra o Gerenciador de Cluster HPC para verificar o status do cluster HPC Pack. Você pode gerenciar e monitorar os nós de computação Linux da mesma maneira que faz trabalha com os nós de computação do Windows. Por exemplo, você verá os nós Linux listados no Gerenciamento de Nós.

    ![Gerenciamento de nós][management]

    Você também verá os nós do Linux na exibição do Mapa de Calor.

    ![Mapa de calor][heatmap]

## Como mover dados em um cluster com nós do Linux

Você tem várias opções para mover dados entre nós Linux e o nó principal do Windows do cluster. Aqui estão três métodos comuns.

* **Arquivo do Azure ** – expõe um compartilhamento de arquivos para armazenar arquivos de dados no armazenamento do Azure. Os nós de Windows e do Linux podem montar um compartilhamento de Arquivos do Azure como uma unidade ou pasta ao mesmo tempo, mesmo se eles estiverem implantados em diferentes redes virtuais.

* **Compartilhamento do nó de cabeçalho do SMB ** – monta uma pasta compartilhada do nó de cabeçalho em nós do Linux.

* **Servidor NFS do nó de cabeçalho** – fornece uma solução de compartilhamento de arquivos para um ambiente misto do Windows e do Linux.

### Armazenamento de arquivos do Azure

O serviço [Arquivo do Azure](https://azure.microsoft.com/services/storage/files/) expõe os compartilhamentos de arquivos usando o protocolo SMB 2.1 padrão. As VMs e os serviços de nuvem podem compartilhar dados de arquivos entre componentes de aplicativos por meio de compartilhamentos montados, e aplicativos locais podem acessar dados de arquivos em um compartilhamento por meio da API de armazenamento de arquivos. Para obter mais informações, consulte [Como usar o armazenamento de Arquivos do Azure com o PowerShell e .NET](../storage/storage-dotnet-how-to-use-files.md).

Para criar um compartilhamento de arquivos do Azure, consulte as etapas detalhadas em [Introdução ao serviço de arquivos do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx). Para configurar as conexões persistentes, confira [Persisting connections to Microsoft Azure Files](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

Neste exemplo, criamos um compartilhamento de Arquivos do Azure chamado rdma em nosso allvhdsje de conta de armazenamento. Para montar o compartilhamento no nó de cabeçalho, abra uma janela Comando e insira os seguintes comandos:

```
> cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>
> net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

Neste exemplo, allvhdsje é o nome da conta de armazenamento, storageaccountkey é a chave da conta de armazenamento e o rdma é o nome do compartilhamento de Arquivos do Azure. O compartilhamento de Arquivos do Azure será montado em z: no seu nó principal.

Para montar o compartilhamento de Arquivo do Azure em nós do Linux, execute um comando **clusrun** no nó de cabeçalho. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)** é uma ferramenta útil do HPC Pack para executar tarefas administrativas em vários nós. Consulte também [CLusrun para nós Linux](#CLusrun-for-Linux-nodes) neste artigo.

Abra uma janela do Windows PowerShell e digite os seguintes comandos:

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /rdma

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

O primeiro comando cria uma pasta chamada /rdma em todos os nós do grupo LinuxNodes. O segundo comando monta a pasta allvhdsjw.file.core.windows.net/rdma de compartilhamento de Arquivos do Azure para a pasta /rdma com dir e bits de modo de arquivo definido como 777. No segundo comando, allvhdsje é o nome da sua conta de armazenamento e storageaccountkey é a chave da conta de armazenamento.

>[AZURE.NOTE]O símbolo "`" no segundo comando é um símbolo de escape para o PowerShell. "`," significa que "," (o caractere de vírgula) é uma parte do comando.

### Compartilhamento do nó principal

Como alternativa, monte uma pasta compartilhada do nó de cabeçalho em nós do Linux. Essa é a maneira mais simples de compartilhar arquivos, mas o nó principal e todos os nós do Linux precisam ser implantados na mesma rede virtual. Siga estas etapas:

1. Crie uma pasta no nó principal e compartilhe-o com Todos com permissões de Leitura/Gravação. Por exemplo, compartilhe D:\\OpenFOAM no nó de cabeçalho como \\CentOS7RDMA-HN\\OpenFOAM. Nesse caso, o CentOS7RDMA-HN é o nome do host do nó de cabeçalho.

    ![Permissões de compartilhamento de arquivo][fileshareperms]

    ![Compartilhamento de arquivos][filesharing]

2. Abra uma janela do Windows PowerShell e execute os seguintes comandos para montar a pasta compartilhada.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
```

O primeiro comando cria uma pasta chamada /openfoam em todos os nós no grupo LinuxNodes. O segundo comando monta a pasta compartilhada //CentOS7RDMA-HN/OpenFOAM para a pasta com dir e bits de modo de arquivo definido como 777. O nome de usuário e a senha no comando devem ser o nome de usuário e a senha de um usuário de cluster no nó principal.

>[AZURE.NOTE]O símbolo "`" no segundo comando é um símbolo de escape para o PowerShell. "`," significa que "," (o caractere de vírgula) é uma parte do comando.


### Servidor NFS

O serviço NFS permite que os usuários compartilhem e migrem arquivos entre computadores com o sistema operacional Windows Server 2012 usando o protocolo SMB e computadores Linux usando o protocolo NFS. O servidor NFS e todos os outros nós devem ser implantados na mesma rede virtual. Ele fornece a melhor compatibilidade com nós do Linux em comparação com um compartilhamento SMB. Por exemplo, ele suporta links de arquivos.

1. Para instalar e configurar um servidor NFS, siga as etapas no [Servidor para primeiro compartilhamento do sistema de arquivos de rede ponta a ponta](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).

    Por exemplo, crie um compartilhamento NFS chamado nfs com as seguintes propriedades.

    ![Autorização de NFS][nfsauth]

    ![Permissões de compartilhamento NFS][nfsshare]

    ![Permissões de compartilhamento NFS NTFS][nfsperm]

    ![Propriedades de gerenciamento de NFS][nfsmanage]

2. Abra uma janela do Windows PowerShell e execute o seguinte comandos para montar o compartilhamento de NFS.

  ```
  PS > clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
  PS > clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
  ```

  O primeiro comando cria uma pasta chamada /nfsshared em todos os nós do grupo LinuxNodes. O segundo comando monta o compartilhamento CentOS7RDMA-HN:/nfs para a pasta. Aqui, CentOS7RDMA-HN:/nfss é o caminho remoto do seu compartilhamento de NFS.

## Como enviar trabalhos
Há várias maneiras de enviar trabalhos ao cluster HPC Pack.

* Gerenciador de Cluster HPC ou GUI do Gerenciador de Trabalhos HPC

* Portal da Web do HPC

* API REST

O envio de trabalho para o cluster no Azure por meio de ferramentas de GUI do HPC Pack e o portal da Web do HPC são os mesmas para nós de computação do Windows. Consulte [Gerenciador de trabalhos do HPC Pack](https://technet.microsoft.com/library/ff919691.aspx) e [Como enviar trabalhos de e em cliente local](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).

Para enviar trabalhos por meio da API REST, confira [Creating and Submitting Jobs by Using the REST API in Microsoft HPC Pack (Windows HPC Server)](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Consulte também o exemplo do Python no [SDK do HPC Pack](https://www.microsoft.com/download/details.aspx?id=47756) para enviar trabalhos de um cliente Linux.

## ClusRun para nós Linux

A ferramenta **clusrun** do HPC Pack pode ser usada para executar comandos em nós do Linux por meio de um prompt de comando ou do Gerenciador de Cluster de HPC. Veja alguns exemplos básicos.

* Mostre os nomes de usuário atuais em todos os nós no cluster.

    ```
    > clusrun whoami
    ```

* Instale a ferramenta de depuração **gdb** com o **yum** em todos os nós do grupo linuxnodes e os reinicie após 10 minutos.

    ```
    > clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```

* Crie um script de shell exibindo cada número de 1 a 10 durante um segundo em cada nó no cluster, execute-o e mostre imediatamente a saída dos nós.

    ```
    > clusrun /interleaved /nodegroup:linuxnodes echo "for i in {1..10}; do echo \\"\$i\\"; sleep 1; done" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

>[AZURE.NOTE] Talvez seja necessário usar determinados caracteres de escape em comandos **clusrun**. Conforme mostra este exemplo, use ^ em uma janela de comando para escapar o símbolo ">".

## Próximas etapas

* Tente dimensionar verticalmente o cluster para um maior número de nós ou executar uma carga de trabalho do Linux no cluster. Para ver um exemplo, confira [Executar o NAMD com o Microsoft HPC Pack em nós de computação do Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md).

* Experimente um cluster com nós de computação de tamanho [A8 ou A9](virtual-machines-windows-a8-a9-a10-a11-specs.md) para executar cargas de trabalho MPI. Para ver um exemplo, confira [Executar o OpenFOAM com o Microsoft HPC Pack em um cluster de RDMA do Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md).

* Experimente um [modelo de início rápido do Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/) com o Gerenciador de Recursos do Azure para acelerar as implantações do HPC Pack com um número maior de nós de computação do Linux.

<!--Image references-->
[scenario]: ./media/virtual-machines-linux-classic-hpcpack-cluster/scenario.png
[validate]: ./media/virtual-machines-linux-classic-hpcpack-cluster/validate.png
[resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster/resources.png
[deploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster/deploy.png
[management]: ./media/virtual-machines-linux-classic-hpcpack-cluster/management.png
[heatmap]: ./media/virtual-machines-linux-classic-hpcpack-cluster/heatmap.png
[fileshareperms]: ./media/virtual-machines-linux-classic-hpcpack-cluster/fileshare1.png
[filesharing]: ./media/virtual-machines-linux-classic-hpcpack-cluster/fileshare2.png
[nfsauth]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsauth.png
[nfsshare]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsshare.png
[nfsperm]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsperm.png
[nfsmanage]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsmanage.png

<!---HONumber=AcomDC_0323_2016-->