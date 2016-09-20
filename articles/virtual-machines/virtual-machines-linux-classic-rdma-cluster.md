<properties
 pageTitle="Cluster de RDMA do Linux para executar aplicativos MPI | Microsoft Azure"
 description="Crie um cluster Linux de VMs de tamanho A8 ou A9 para usar o RDMA para executar aplicativos MPI."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="08/17/2016"
 ms.author="danlep"/>

# Configurar um cluster de RDMA do Linux para executar aplicativos MPI

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Saiba como configurar um cluster de RDMA do Linux no Azure com VMs de tamanho A8 e A9 para executar aplicativos MPI (Message Passing Interface) paralelamente. Quando as VMs A8 e A9 do Azure executam uma distribuição Linux HPC com suporte e uma implementação MPI com suporte, os aplicativos MPI se comunicam de forma eficiente em uma rede de alta taxa de transferência e de baixa latência que é baseada na tecnologia RDMA (Acesso Remoto Direto à Memória).

>[AZURE.NOTE] O suporte à RDMA Linux do Azure atualmente requer o Intel MPI Library versão 5 em VMs criadas a partir de imagens do HPC do SUSE Linux Enterprise Server 12 ou do HPC do CentOS 6.5 ou 7.1 no Azure Marketplace. Consulte [Sobre as instâncias A8, A9, A10, e A11](virtual-machines-linux-a8-a9-a10-a11-specs.md) para mais informações e considerações.



## Opções de implantação de cluster

Estes são métodos que é possível usar para criar um cluster de RDMA do Linux com ou sem um agendador de trabalhos.

* **HPC Pack** - cria um cluster do Microsoft HPC Pack no Azure e adiciona nós de computação do tamanho A8 ou A9 que executam distribuições Linux com suporte para acesso à rede RDMA. Consulte [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

* **Scripts de CLI do Azure** - Conforme mostrado posteriormente neste artigo, use a [Interface de linha de comando do Azure](../xplat-cli-install.md) (CLI) para o script de implantação de um cluster de VMs Linux de tamanho A8 ou A9. A CLI no modo Gerenciamento de Serviços cria os nós do cluster em série no modelo de implantação clássico, então implantar muitos nós de computação pode demorar vários minutos. No modelo de implantação clássico, as VMs A8 ou A9 devem ser implantadas no mesmo serviço de nuvem para se conectar por meio da rede RDMA.

* **Modelos do Azure Resource Manager** - Use o modelo de implantação do Resource Manager para implantar um cluster de VMs A8 e A9 que se conecte à rede RDMA para executar cargas de trabalho MPI. É possível [criar seu próprio modelo](../resource-group-authoring-templates.md) ou verificar os [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/) para obter modelos com os quais a Microsoft ou a comunidade contribuíram para implantar a solução desejada. Os modelos do Gerenciador de Recursos podem fornecer uma maneira rápida e confiável para implantar um cluster do Linux. No modelo de implantação clássico do Resource Manager, as VMs A8 ou A9 devem ser implantadas no mesmo conjunto de disponibilidade para se conectar por meio da rede RDMA.

## Implantação de exemplo no modelo clássico

As etapas a seguir ajudarão você a usar a CLI do Azure para implantar uma VM do HPC do SUSE Linux Enterprise Server (SLES) 12 do Azure Marketplace, instalar o Intel MPI Library e criar uma imagem de máquina virtual personalizada. Em seguida, use a imagem para o script de implantação de um cluster de VMs A8 ou A9.

>[AZURE.TIP]  Use etapas semelhantes para implantar um cluster de VMs A8 ou A9 baseado na imagem do HPC baseado em CentOS 6.5 ou 7.1 no Azure Marketplace. As diferenças são indicadas nas etapas. Por exemplo, como as imagens do HPC baseado em CentOS incluem a MPI Intel, você não precisará instalar a MPI Intel separadamente em VMs criadas a partir dessas imagens.

### Pré-requisitos

*   **Computador cliente**: você precisa de um computador cliente baseado em Windows, Linux ou Mac para se comunicar com o Azure. Essas etapas pressupõem que você esteja usando um cliente Linux.

*   **Assinatura do Azure** - se você não tiver uma assinatura, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos. Para clusters maiores, considere uma assinatura pré-paga ou outras opções de compra.

*   **Cota de núcleos**: talvez seja necessário aumentar a cota de núcleos para implantar um cluster de VMs A8 ou A9. Por exemplo, você precisa de pelo menos 128 núcleos para implantar 8 VMs A9, conforme mostrado neste artigo. Para aumentar a cota, [abra uma solicitação de atendimento ao cliente online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitamente.

*   **CLI do Azure** - [instale](../xplat-cli-install.md) a CLI do Azure e [conecte-se à sua assinatura do Azure](../xplat-cli-connect.md) no computador cliente.

*   **Intel MPI** - para personalizar uma imagem de VM do HPC do SLES 12 para seu cluster, você precisa baixar e instalar o tempo de execução do Intel MPI Library 5 do [site Intel.com](https://software.intel.com/pt-BR/intel-mpi-library/). Mais detalhes serão fornecidos posteriormente neste artigo. Para preparar a instalação, depois de se registrar na Intel, copie o link de download para o arquivo .tgz para a versão apropriada do Intel MPI. Este artigo se baseia no Intel MPI versão 5.0.3.048.

    >[AZURE.NOTE] Se você usar a imagem HPC do CentOS 6.5 ou CentOS 7.1 no Azure Marketplace para criar os nós de cluster, o Intel MPI versão 5.1.3.181 será pré-instalada.

### Provisionar uma VM SLES 12

Depois de entrar no Azure com a CLI do Azure, execute `azure config list` para confirmar que a saída mostra o modo Gerenciamento de Serviços do Azure. Se não estiver, defina o modo executando este comando:


    azure config mode asm


Digite o seguinte para listar todas as assinaturas que você está autorizado a usar:


    azure account list

A assinatura ativa atual é identificada com `Current` definido como `true`. Se esta não for a assinatura que você deseja usar para criar o cluster, defina a Id de assinatura apropriada como a assinatura ativa:

    azure account set <subscription-Id>

Para ver as imagens SLES 12 HPC publicamente disponíveis no Azure, execute um comando semelhante ao seguinte, supondo que o ambiente de shell seja compatível com **grep**:


    azure vm image list | grep "suse.*hpc"

Agora, provisione uma VM de tamanho A9 com uma imagem SLES 12 HPC executando um comando semelhante ao seguinte:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708

onde

* o tamanho (A9 neste exemplo) pode ser A8 ou A9

* o número da porta SSH externa (22 neste exemplo, que é o padrão SSH) é qualquer número de porta válido. O número da porta de SSH interna é definido como 22

* Um novo serviço de nuvem é criado na região do Azure especificada pelo local. Especifique um local em que as instâncias A8 e A9 estejam disponíveis.

* O nome da imagem SLES 12 atualmente pode ser `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` ou `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-priority-v20150708` para suporte de prioridade SUSE (serão cobradas taxas adicionais)

    >[AZURE.NOTE]Se você quiser usar uma imagem do HPC baseado em CentOS, os nomes de imagem atual serão `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-65-HPC-20160408` e `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-71-HPC-20160408`.

### Personalizar a VM

Depois que a VM concluir o provisionamento, faça SSH na VM usando o endereço IP externo da VM (ou o nome DNS) e o número da porta externa configurada, e personalize-a. Para obter detalhes de conexão, consulte [Como fazer logon em uma máquina virtual executando o Linux](virtual-machines-linux-mac-create-ssh-keys.md). Execute comandos como o usuário configurado na VM, a menos que o acesso à raiz seja necessário para concluir uma etapa.

>[AZURE.IMPORTANT]O Microsoft Azure não oferece acesso à raiz para VMs do Linux. Para obter acesso administrativo quando estiver conectado como um usuário à VM, execute comandos usando `sudo`.

* **Atualizações**: instale atualizações usando o **zypper**. Talvez você também queira instalar utilitários NFS.

    >[AZURE.IMPORTANT]Se você tiver implantado uma VM HPC SLES 12, é recomendável que você não aplique as atualizações de kernel, que podem causar problemas com os drivers de RDMA do Linux.
    >
    >Nas imagens do HPC baseado em CentOS do Marketplace, as atualizações de kernel estão desabilitadas no arquivo de configuração **yum**. Como os drivers RDMA do Linux são distribuídos como um pacote RPM as atualizações de driver poderão não funcionar caso o kernel seja atualizado.

* **Atualizações de drivers de RDMA Linux** -se você tiver implantado uma VM do HPC do SLES 12, precisará atualizar os drivers RDMA. Veja [Sobre as instâncias de computação intensiva A8, A9, A10 e A11](virtual-machines-linux-a8-a9-a10-a11-specs.md#rdma-driver-updates-for-sles-12) para obter detalhes.

* **MPI Intel** - se você tiver implantado uma VM do HPC do SLES 12, baixe e instale o tempo de execução do Intel MPI Library 5 do site Intel.com, executando comandos semelhantes aos que se seguem. Esta etapa não será necessária se você tiver implantado uma VM do HPC baseado em CentOS 6.5 ou 7.1.

        sudo wget <download link for your registration>

        sudo tar xvzf <tar-file>

        cd <mpi-directory>

        sudo ./install.sh

    Aceite as configurações padrão para instalar o Intel MPI na VM.

* **Bloquear memória**: para os códigos do MPI bloquearem a memória disponível para RDMA, você precisa adicionar ou alterar as configurações a seguir no arquivo /etc/security/limits.conf. (Você precisa de acesso à raiz para editar esse arquivo.) Se você tiver implantado uma VM do HPC baseado em CentOS 6.5 ou 7.1, as configurações já foram adicionadas a este arquivo.

        <User or group name> hard    memlock <memory required for your application in KB>

        <User or group name> soft    memlock <memory required for your application in KB>

    >[AZURE.NOTE]Para fins de teste, também é possível definir memlock como ilimitado. Por exemplo: `Bloqueio de memória física de <Nome do usuário ou do grupo> ilimitado.

* **Chaves SSH para VMs SLES 12** - gere chaves SSH para estabelecer confiança para sua conta de usuário entre os nós de computação no cluster HPC do SLES 12 ao executar trabalhos MPI. (Se você tiver implantado uma VM do HPC baseado em CentOS, não execute esta etapa. Veja as instruções posteriormente neste artigo para configurar a relação de confiança SSH sem senha entre os nós de cluster depois de capturar a imagem e implantar o cluster).

    Execute o comando a seguir para criar chaves SSH. Quando for solicitado, pressione Enter para gerar as chaves no local padrão, sem definir uma senha.

        ssh-keygen

    Acrescente a chave pública ao arquivo authorized\_keys para chaves públicas conhecidas.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    No diretório ~/.ssh, edite ou crie o arquivo “config”. Forneça o intervalo de endereços IP da rede privada que você pretende usar no Azure (neste exemplo, 10.32.0.0/16):

        host 10.32.0.*
        StrictHostKeyChecking no

    Como alternativa, liste o endereço IP da rede privada de cada VM no cluster da seguinte maneira:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]A configuração de `StrictHostKeyChecking no` pode criar um risco de segurança potencial quando um determinado endereço IP ou intervalo não for especificado.

* **Aplicativos**: instale todos os aplicativos necessários nessa VM ou execute outras personalizações antes de capturar a imagem.

### Capturar a imagem

Para capturar a imagem, primeiramente execute o comando a seguir na VM do Linux. Esse comando desprovisiona a VM, mas mantém as contas de usuário e chaves SSH que você configurou.

```
sudo waagent -deprovision
```

Em seguida, no computador cliente, execute os seguintes comandos da CLI do Azure para capturar a imagem. Consulte [Como capturar uma máquina virtual clássica do Linux como uma imagem](virtual-machines-linux-classic-capture-image.md) para obter detalhes.

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Depois de executar esses comandos, a imagem da VM é capturada para seu uso e a VM é excluída. Agora você tem a imagem personalizada pronta para implantar um cluster.

### Implantar um cluster com a imagem

Modifique o script Bash a seguir com os valores apropriados para seu ambiente e execute-o no computador cliente. Como o Azure implanta as VMs serialmente no modelo de implantação clássico, a implantação das oito VMs A9 sugeridas neste script demora um pouco.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Select a region where A8 and A9 VMs are available, such as West US
# See Azure Pricing pages for prices and availability of A8 and A9 VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the A8 and A9 instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## Relação de confiança SSH sem senha em um cluster do CentOS

Se você tiver implantado um cluster usando uma imagem do HPC baseado em CentOS, há dois métodos para estabelecer a relação de confiança entre os nós de computação: autenticação baseada no usuário e autenticação baseada em host. A autenticação baseada em host está fora do escopo deste artigo e geralmente deve ser feita por meio de um script de extensão durante a implantação. A autenticação baseada em usuário é conveniente para estabelecer a relação de confiança após a implantação e requer a geração e o compartilhamento de chaves SSH entre os nós de computação no cluster. Esse método é conhecido como logon SSH sem senha e é necessário na execução de trabalhos MPI.

Um exemplo de script da comunidade está disponível no [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) para facilitar a autenticação do usuário em um cluster HPC baseado em CentOS. Baixe e use esse script usando as etapas a seguir. Você também pode modificar esse script ou usar qualquer outro método para estabelecer a autenticação SSH sem senha entre os nós de computação do cluster.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh
    
Para executar o script, você precisa saber o prefixo para seus endereços IP de sub-rede. Obtenha o prefixo executando o comando a seguir em um dos nós do cluster. A saída deverá ser semelhante a 10.1.3.5, e o prefixo é a parte 10.1.3.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Agora execute o script usando três parâmetros: o nome de usuário comum nos nós de computação, a senha comum desse usuário nos nós de computação e o prefixo de sub-rede que foi retornado pelo comando anterior.


    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Esse script faz o seguinte:

* Cria um diretório no nó do host chamado .ssh, que é necessário para o logon sem senha.
* Cria um arquivo de configuração no diretório .ssh, que instrui o logon sem senha a permitir o logon de qualquer nó no cluster.
* Cria arquivos com os nomes de nó e os endereços IP de nó para todos os nós no cluster. Esses arquivos são deixados após a execução do script para posterior referência.
* Cria um par de chaves pública e privada para cada nó de cluster, incluindo o nó de host e cria entradas no arquivo authorized\_keys.

>[AZURE.WARNING]A execução desse script pode criar um potencial risco de segurança. Certifique-se de que as informações da chave pública em ~/.ssh não sejam distribuídas.


## Configurar e executar o Intel MPI

Para executar aplicativos MPI em RDMA do Linux no Azure, é preciso configurar determinadas variáveis de ambiente específicas para o Intel MPI. Veja um script Bash de exemplo para configurar as variáveis e executar um aplicativo. Altere o caminho para mpivars.sh como necessário para a instalação da MPI Intel.

```
#!/bin/bash -x

# For a SLES 12 HPC cluster

source /opt/intel/impi_latest/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh


export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

O formato do arquivo de host é o seguinte. Adicione uma linha para cada nó no cluster. Especifique os endereços IP privados da rede virtual definida anteriormente, não os nomes de DNS. Por exemplo, para dois hosts com endereços IP 10.32.0.1 e 10.32.0.2, o arquivo contém o seguinte:

```
10.32.0.1:16
10.32.0.2:16
```

## Verifique um cluster de dois nós básico após a instalação do Intel MPI

Se ainda não tiver feito isso, primeiramente configure o ambiente para o Intel MPI.

```
# For a SLES 12 HPC cluster

source /opt/intel/impi_latest/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### Executar um comando MPI simples

Execute um comando MPI simples em um dos nós de computação para mostrar que o MPI está instalado corretamente e pode se comunicar entre pelo menos dois nós de computação. O comando **mpirun** a seguir executa o comando **hostname** em dois nós.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
A saída deve listar os nomes de todos os nós passados como entrada para `-hosts`. Por exemplo, um comando **mpirun** com dois nós retorna uma saída semelhante à seguinte:

```
cluster11
cluster12
```

### Executar um parâmetro de comparação de MPI

O comando Intel MPI a seguir verifica a configuração do cluster e a conexão com a rede RDMA usando um parâmetro de comparação de pingpong.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

Você verá uma saída semelhante à seguinte em um cluster ativo com dois nós. Na rede RDMA do Azure, a latência em 3 microssegundos ou menos é esperada para até 512 bytes de tamanhos de mensagem.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## Próximas etapas

* Tente implantar e executar os aplicativos Linux MPI no cluster do Linux.

* Consulte a [Documentação do Intel MPI Library](https://software.intel.com/pt-BR/articles/intel-mpi-library-documentation/) para obter orientação sobre o Intel MPI.

* Experimente um [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) para criar um cluster Intel Lustre usando uma imagem HPC baseado em CentOS. Consulte esta [postagem de blog](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/) para obter detalhes.

<!---HONumber=AcomDC_0907_2016-->