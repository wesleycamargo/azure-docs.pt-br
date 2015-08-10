<properties
 pageTitle="Introdução a nós de computação Linux em um cluster de HPC Pack | Microsoft Azure"
 description="Saiba como fazer um script de implantação de um cluster de HPC Pack no Azure que contenha um nó principal que executa o Windows Server com nós de computação Linux."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/27/2015"
 ms.author="danlep"/>

# Introdução a nós de computação Linux em um cluster de HPC Pack no Azure

Este artigo mostra como usar um script do Azure PowerShell para configurar um cluster do Microsoft HPC Pack no Azure que contém um nó principal que executa o Windows Server e vários nós de computação em execução de uma distribuição CentOS Linux. Também vamos mostrar várias maneiras de mover os arquivos de dados para nós de computação Linux. Você pode usar este cluster para executar cargas de trabalho do HPC Linux no Azure.

Em um alto nível, o diagrama a seguir mostra o cluster de HPC Pack que você criará.

![Cluster de HPC com nós Linux][scenario]

## Implantar um cluster de HPC Pack com nós de computação Linux

Você usará o script de implantação do Microsoft HPC Pack IaaS (\*\*New-HpcIaaSCluster.ps1\*\*) para automatizar a implantação de cluster nos serviços de infraestrutura do Azure (IaaS). Esse script do Azure PowerShell usa uma imagem de VM do HPC Pack no Azure Marketplace para implantação rápida e fornece um conjunto abrangente de parâmetros de configuração para tornar a implantação fácil e flexível. Você pode usar o script para implantar a rede virtual do Azure, contas de armazenamento, serviços de nuvem, controlador de domínio, servidor de banco de dados do SQL Server separado opcional, nó principal do cluster, nós de computação, nós de computação, nós do agente, nós do Azure PaaS ("disparar") e nós de computação Linux (suporte para Linux introduzido no [HPC Pack 2012 R2 Update 2](https://technet.microsoft.com/library/mt269417.aspx)).

Para obter uma visão geral das opções de implantação de cluster de HPC Pack, consulte o [Guia de Introdução do for HPC Pack 2012 R2 e HPC Pack 2012](https://technet.microsoft.com/library/jj884144.aspx).

### Pré-requisitos

* **Computador cliente** - você precisará de um computador cliente baseado em Windows para executar o script de implantação do cluster.

* **Azure PowerShelle** - [Instale e configure o Azure PowerShell](../powershell-install-configure.md) (versão 0.8.10 ou posterior) no computador cliente.

* **Script de implantação do HPC Pack IaaS**- Baixe e descompacte a versão mais recente do script no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Verifique a versão do script executando `New-HPCIaaSCluster.ps1 –Version`. Este artigo se baseia na versão 4.4.0 ou posterior do script.

* **Assinatura do Azure** - você pode usar a assinatura no serviço Azure Global ou Azure China. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

* **Cota de núcleos** - talvez seja necessário aumentar a cota de núcleos, especialmente se você optar por implantar vários nós de cluster com tamanhos de VM de vários núcleos. Para o exemplo neste artigo, você precisará de pelo menos 24 núcleos. Para aumentar a cota, [abra uma solicitação de atendimento ao cliente online](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitamente.

### Criar o arquivo de configuração
O script de implantação do HPC Pack IaaS usa um arquivo de configuração XML como entrada que descreve a infraestrutura do cluster do HPC. Para implantar um cluster pequeno que consiste em um nó principal e dois nós de computação Linux, substitua os valores para o seu ambiente no seguinte arquivo de configuração de exemplo. Para obter mais informações sobre o arquivo de configuração, consulte o arquivo Manual.rtf na pasta de script ou a [documentação do script](https://msdn.microsoft.com/library/azure/dn864734.aspx).

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

* **IaaSClusterConfig** - o elemento raiz do arquivo de configuração.

* **Assinatura** - assinatura do Azure usada para implantar o cluster de HPC Pack. Use o comando abaixo para verificar se o nome da assinatura do Azure está configurado e é exclusivo em seu computador cliente. Neste exemplo, usamos a assinatura do Azure "Assinatura-1".

    ```
    PS > Get-AzureSubscription –SubscriptionName <SubscriptionName>
    ```

    >[AZURE.NOTE]Como alternativa, você pode usar a ID da assinatura para especificar a assinatura que deseja usar. Consulte o arquivo Manual.rtf na pasta do script.

* **StorageAccount** - todos os dados persistentes para o cluster HPC Pack serão armazenados na conta de armazenamento especificada (allvhdsje neste exemplo). Se a conta de armazenamento não existir, o script irá criá-la na região especificada em **Local**.

* **Local** - região do Azure onde você implantará o cluster HPC Pack (Leste do Japão neste exemplo).

* **VNet** - configurações de rede virtual e sub-rede onde o cluster HPC será criado. Você pode criar a rede virtual e sub-redes por conta própria antes de executar esse script ou o script cria uma rede virtual com espaço de endereço 192.168.0.0/20 e sub-rede com espaço de endereço 192.168.0.0/23. Neste exemplo, o script cria a rede virtual centos7rdmavnetje e a sub-rede CentOS7RDMACluster.

* **Domínio** - configurações de domínio do Active Directory para o cluste HPC Pack. Todas as VMs do Windows criadas pelo script ingressarão no domínio. Atualmente, o script oferece suporte a três opções de domínio: ExistingDC, NewDC e HeadNodeAsDC. Neste exemplo, configuraremos o nó principal como controlador de domínio. O nome de domínio totalmente qualificado é hpc.local.

* **Banco de dados** - configurações do banco de dados para o cluster de HPC Pack. Atualmente, o script oferece suporte a três opções de banco de dados: ExistingDB, NewRemoteDB e LocalDB. Neste exemplo, criaremos um banco de dados local no nó principal.

* **HeadNode** - configurações para o nó principal do HPC Pack. Neste exemplo, criaremos um nó principal de tamanho A7 chamado CentOS7RDMA-HN no serviço de nuvem centos7rdma-je. Para oferecer suporte ao envio de trabalho do HPC de computadores cliente remotos (sem domínio), o script permitirá que a API REST do agendador de trabalhos HPC e o portal Web HPC.

* **LinuxComputeNodes** - Configurações para os nós de computação HPC Pack Linux. Neste exemplo, criaremos dois nós de computação Linux CentOS 7 tamanho A7 (CentOS7RDMA-LN1 e CentOS7RDMA-LN2) no serviço de nuvem centos7rdma-je.

### Considerações adicionais para nós de computação Linux

* Atualmente, o HPC Pack suporta as seguintes distribuições Linux para nós de computação: Ubuntu Server 14.10, CentOS 6.6, CentOS 7.0 e SUSE Linux Enterprise Server 12.

* O exemplo neste artigo usa uma versão específica do CentOS disponível no Azure Marketplace para criar o cluster. Se você quiser usar outras imagens disponíveis, use o cmdlet do Azure PowerShell **get-azurevmimage** para encontrar aquele que você precisa. Por exemplo, para listar todas as imagens do CentOS 7.0, execute o seguinte comando: ```
    get-azurevmimage | ?{$_.Label -eq "OpenLogic 7.0"}
    ```

    Encontre o necessário e substitua o valor **ImageName** no arquivo de configuração.

* Imagens do Linux que oferecem suporte à conectividade RDMA para VMs de tamanho A8 e A9 estão disponíveis. Se você especificar uma imagem com drivers de RDMA Linux instalados e habilitados, o script de implantação do HPC Pack IaaS irá implantá-los. Por exemplo, você pode especificar o nome da imagem `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` para o SUSE Linux Enterprise Server 12 atual – otimizado para imagem de computação de alto desempenho no Marketplace.


* Para habilitar o RDMA Linux nas VMs do Linux criadas a partir de imagens compatíveis para executar trabalhos MPI, instale e configure uma biblioteca específica de MPI em nós do Linux, após a implantação de cluster de acordo com o que o seu aplicativo precisa. Para obter mais informações sobre como usar o RDMA em nós do Linux no Azure, consulte [Configurar um cluster do RDMA Linux para executar aplicativos MPI](virtual-machines-linux-cluster-rdma.md).

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

    a. Como a **AdminPassword** não é especificado no comando acima, você será solicitado a inserir a senha para o usuário *MyAdminName*.

    b. Em seguida, o script começa a validar o arquivo de configuração. Demora de dezenas de segundos a vários minutos dependendo da conexão de rede.

    ![Validação][validate]

    c. Depois de passarem as validações, o script lista os recursos que serão criados para o cluster HPC. Digite *y* para continuar.

    ![Recursos][resources]

    d. Em seguida, o script começa a implantar o cluster de HPC Pack e concluirá a configuração sem mais etapas manuais. Isso pode levar vários minutos.

    ![Implantar][deploy]

4. Após a configuração ser concluída com êxito, a Área de Trabalho Remota para o nó principal e abra o Gerenciador de Cluster HPC para verificar o status do cluster HPC Pack. Você pode gerenciar e monitorar os nós de computação Linux da mesma maneira que faz trabalha com os nós de computação do Windows. Por exemplo, você verá os nós Linux listados no Gerenciamento de Nós.

    ![Gerenciamento de nós][management]

    Você também verá os nós do Linux na exibição do Mapa de Calor.

    ![Mapa de calor][heatmap]

## Como mover dados em um cluster com nós do Linux

Você tem várias opções para mover dados entre nós Linux e o nó principal do Windows do cluster. Aqui estão três métodos comuns.

* **Arquivo azure** - expõe um compartilhamento de arquivos para armazenar arquivos de dados no armazenamento do Azure. Os nós de Windows e do Linux podem montar um compartilhamento de Arquivos do Azure como uma unidade ou pasta ao mesmo tempo, mesmo se eles estiverem implantados em diferentes redes virtuais.

* **Compartilhamento do nó principal do SMB** - monta uma pasta compartilhada do nó principal em nós do Linux.

* **Servidor NFS do nó principal** - fornece uma solução de compartilhamento de arquivos para um ambiente misto de Windows e Linux.

### Arquivos do Azure

O serviço de [Arquivos do Azure](https://azure.microsoft.com/services/storage/files/) expõe os compartilhamentos de arquivos usando o protocolo SMB 2.1 padrão. As VMs e os serviços de nuvem podem compartilhar dados de arquivos entre componentes de aplicativos por meio de compartilhamentos montados, e aplicativos locais podem acessar dados de arquivos em um compartilhamento por meio da API de armazenamento de arquivos. Para obter mais informações, consulte [Como usar o armazenamento de Arquivos do Azure com o PowerShell e .NET](../storage/storage-dotnet-how-to-use-files.md).

Para criar um compartilhamento de arquivos do Azure, consulte as etapas detalhadas em [Introdução ao serviço de arquivos do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx). Para configurar as conexões persistentes, consulte [Conexões persistentes aos Arquivos do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

Neste exemplo, criamos um compartilhamento de Arquivos do Azure chamado rdma em nosso allvhdsje de conta de armazenamento. Para montar o compartilhamento no nó principal, vamos abrir uma janela de Comando e digitar os seguintes comandos:

```
> cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>
> net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

Neste exemplo, allvhdsje é o nome da conta de armazenamento, storageaccountkey é a chave da conta de armazenamento e o rdma é o nome do compartilhamento de Arquivos do Azure. O compartilhamento de Arquivos do Azure será montado em z: no seu nó principal.

Para montar o compartilhamento de Arquivos do Azure em nós Linux, execute um comando **clusrun** no nó principal. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)** é uma ferramenta útil de HPC Pack para executar tarefas administrativas em vários nós. Consulte também[CLusrun para nós Linux](#CLusrun-for-Linux-nodes)neste artigo.

Abra uma janela do Windows PowerShell e digite os seguintes comandos:

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /rdma

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

O primeiro comando cria uma pasta chamada /rdma em todos os nós do grupo LinuxNodes. O segundo comando monta a pasta allvhdsjw.file.core.windows.net/rdma de compartilhamento de Arquivos do Azure para a pasta /rdma com dir e bits de modo de arquivo definido como 777. No segundo comando, allvhdsje é o nome da sua conta de armazenamento e storageaccountkey é a chave da conta de armazenamento.

>[AZURE.NOTE]O símbolo "`" no segundo comando é um símbolo de escape para o PowerShell. "`," significa que "," (uma vírgula) é uma parte do comando.

### Compartilhamento do nó principal

Como alternativa, você pode montar uma pasta compartilhada do nó principal em nós do Linux. Essa é a maneira mais simples de compartilhar arquivos, mas o nó principal e todos os nós do Linux precisam ser implantados na mesma rede virtual. Siga estas etapas:

1. Crie uma pasta no nó principal e compartilhe-o com Todos com permissões de Leitura/Gravação. Por exemplo, podemos compartilhar D:\\OpenFOAM no nó principal como \\CentOS7RDMA-HN\\OpenFOAM. Aqui, HN CentOS7RDMA é o nome do host do nosso nó principal.

    ![Permissões de compartilhamento de arquivo][fileshareperms]

    ![Compartilhamento de arquivos][filesharing]

2. Abra uma janela do Windows PowerShell e execute os seguintes comandos para montar a pasta compartilhada.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>,password='<password>’,dir_mode=0777`,file_mode=0777
```

O primeiro comando cria uma pasta chamada /openfoam em todos os nós do grupo LinuxNodes. O segundo comando monta a pasta compartilhada //CentOS7RDMA-HN/OpenFOAM para a pasta com dir e bits de modo de arquivo definido como 777. O nome de usuário e a senha no comando devem ser o nome de usuário e a senha de um usuário no nó principal.

>[AZURE.NOTE]O símbolo "`" no segundo comando é um símbolo de escape para o PowerShell. "`," significa que "," (uma vírgula) é uma parte do comando.


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

O envio de trabalho para o cluster no Azure por meio de ferramentas de GUI do HPC Pack e o portal da Web do HPC são os mesmas para nós de computação do Windows. Consulte [Gerenciador de trabalhos do HPC Pack](https://technet.microsoft.com/library/ff919691.aspx) e [Como enviar trabalhos de e em cliente local](https://msdn.microsoft.com/library/azure/dn689084.aspx).

Para enviar trabalhos por meio da API REST, consulte [Criar e enviar trabalhos usando a API REST no HPC Pack da Microsoft](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Consulte também o exemplo do Python no [SDK do HPC Pack](https://www.microsoft.com/download/details.aspx?id=47756) para enviar trabalhos de um cliente Linux.

## ClusRun para nós Linux

A ferramenta **clusrun** do HPC Pack pode ser usada para executar comandos em nós Linux por meio de uma janela de Comando ou do Gerenciador de Cluster de HPC. Estes são alguns exemplos:

* Mostrar nomes de usuário atuais de todos os nós no cluster

    ```
> clusrun whoami
```

* Instalar a ferramenta de depuração **gdb** de **yum** em todos os nós do grupo linuxnodes e reiniciá-los após 10 minutos

    ```
> clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
```

* Crie um script de shell exibindo 1 a 10 por segundo em nós de cluster, execute-o e mostre as saídas de cada nó imediatamente.

    ```
> clusrun /interleaved echo "for i in {1..10}; do echo \\"\$i\\"; sleep 1; done" ^> script.sh; chmod +x script.sh; ./script.sh```

>[AZURE.NOTE]Você pode precisa usar determinados caracteres de escape em comandos clusrun. Use ^ em uma janela de Comando e ` no PowerShell para transformar caracteres especiais. Por exemplo, no PowerShell, os caracteres de vírgula e ponto e vírgula precisam ser transformado por ', e ', respectivamente. Esses caracteres não necessitam de transformação em uma janela de Comando.




## Próximas etapas

* Use **clusrun** para instalar seu aplicativo Linux nos nós de computação Linux e enviar um trabalho para o cluster de HPC Pack.

* Tente escalar verticalmente o cluster para um número maior de nós ou implantar tamanho de nós de computação [A8 ou A9](virtual-machines-a8-a9-a10-a11-specs.md) para executar cargas de trabalho MPI.


<!--Image references-->
[scenario]: ./media/virtual-machines-linux-cluster-hpcpack/scenario.png
[validate]: ./media/virtual-machines-linux-cluster-hpcpack/validate.png
[resources]: ./media/virtual-machines-linux-cluster-hpcpack/resources.png
[deploy]: ./media/virtual-machines-linux-cluster-hpcpack/deploy.png
[management]: ./media/virtual-machines-linux-cluster-hpcpack/management.png
[heatmap]: ./media/virtual-machines-linux-cluster-hpcpack/heatmap.png
[fileshareperms]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare1.png
[filesharing]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare2.png
[nfsauth]: ./media/virtual-machines-linux-cluster-hpcpack/nfsauth.png
[nfsshare]: ./media/virtual-machines-linux-cluster-hpcpack/nfsshare.png
[nfsperm]: ./media/virtual-machines-linux-cluster-hpcpack/nfsperm.png
[nfsmanage]: ./media/virtual-machines-linux-cluster-hpcpack/nfsmanage.png

<!---HONumber=July15_HO5-->