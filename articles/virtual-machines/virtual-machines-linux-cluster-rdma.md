<properties
 pageTitle="Cluster de RDMA do Linux para executar aplicativos MPI | Microsoft Azure"
 description="Crie um cluster Linux de VMs de tamanho A8 ou A9 para usar RDMA e executar aplicativos MPI."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2015"
 ms.author="danlep"/>

# Configurar um cluster de RDMA do Linux para executar aplicativos MPI

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


Este artigo mostra como configurar um cluster de RDMA do Linux no Azure com [máquinas virtuais de tamanho A8 e A9](virtual-machines-a8-a9-a10-a11-specs.md) para executar aplicativos MPI (Message Passing Interface) paralelamente. Quando você configura VMs baseadas em Linux de tamanho A8 e A9 para executar uma implementação MPI compatível, os aplicativos MPI se comunicam de forma eficiente em uma rede de alta taxa de transferência e de baixa latência no Azure que é baseada na tecnologia RDMA (Acesso Remoto Direto à Memória).

>[AZURE.NOTE]O RDMA do Linux no Azure atualmente é compatível com o Intel MPI Library versão 5 em execução no SUSE Linux Enterprise Server 12 (SLES 12). Este artigo se baseia no Intel MPI versão 5.0.3.048.
>
> O Azure também fornece instâncias com computação intensiva A10 e A11, com recursos de processamento idênticos às instâncias A8 e A9, mas sem uma conexão a uma rede de back-end RDMA. Para executar cargas de trabalho MPI no Azure, você geralmente terá melhor desempenho com as instâncias A8 e A9.


## Opções de implantação de cluster do Linux

Estes são métodos que é possível usar para criar um cluster de RDMA do Linux com ou sem um agendador de trabalhos.

* **HPC Pack**: crie um cluster do Microsoft HPC Pack no Azure e adicione nós de computação que executem distribuições Linux compatíveis (o suporte ao nó de computação do Linux começa com o HPC Pack 2012 R2 Update 2). Determinados nós do Linux podem ser configurados para acessar a rede RDMA. Consulte [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](virtual-machines-linux-cluster.md).

* **Scripts da CLI do Azure**: como mostrado nas etapas no restante deste artigo, use a [CLI (Interface de Linha de Comando) do Azure](../xplat-cli-install.md) para Mac, Linux e Windows para criar scripts de implantação de uma rede virtual e os outros componentes necessários para criar um cluster do Linux. A CLI no modo de implantação clássica (Gerenciamento de Serviços) cria os nós do cluster em série, de modo que se você estiver implantando muitos nós de computação, a implantação poderá demorar alguns minutos para ser concluída.

* **Modelos do Gerenciador de Recursos do Azure**: ao criar um arquivo de modelo JSON simples do Gerenciador de Recursos do Azure e executar comandos da CLI do Azure para o Gerenciador de Recursos, ou ao usar o portal de visualização do Azure, implante várias VMs A8 e A9 do Linux, bem como defina redes virtuais, endereços IP estáticos, configurações de DNS e outros recursos para criar um cluster de cálculo que possa aproveitar a rede RDMA e executar cargas de trabalho MPI. É possível [criar seu próprio modelo](../resource-group-authoring-templates.md) ou verificar a [página Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/) para obter modelos com os quais a Microsoft ou a comunidade contribuíram para implantar a solução desejada. Os modelos do Gerenciador de Recursos geralmente fornecem a maneira mais rápida e confiável para implantar um cluster do Linux.

## Implantação no Gerenciamento de Serviços do Azure com scripts da CLI do Azure

As etapas a seguir o ajudarão a usar a CLI do Azure para implantar uma VM SLES 12, instalar o Intel MPI Library e outras personalizações, criar uma imagem de VM personalizada e, em seguida, fazer um script da implantação de um cluster de VMs A8 ou A9.

### Pré-requisitos

*   **Computador cliente**: você precisará de um computador cliente baseado em Windows, Linux ou Mac para se comunicar com o Azure. Essas etapas pressupõem que você esteja usando um cliente Linux.

*   **Assinatura do Azure**: se não tiver uma conta, você poderá criar uma de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

*   **Cota de núcleos**: talvez seja necessário aumentar a cota de núcleos para implantar um cluster de VMs A8 ou A9. Por exemplo, você precisará de pelo menos 128 núcleos para implantar 8 VMs A9, conforme mostrado neste artigo. Para aumentar a cota, [abra uma solicitação de atendimento ao cliente online](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitamente.

*   **CLI do Azure**: [instale](../xplat-cli-install.md) a CLI do Azure e [configure-a](../xplat-cli-connect.md) para se conectar à assinatura do Azure no computador cliente.

*   **Intel MPI**: como parte da personalização de uma imagem de VM Linux para seu cluster (consulte os detalhes mais adiante neste artigo), você precisa baixar e instalar o tempo de execução do Intel MPI Library 5 do site [Intel.com](https://software.intel.com/pt-BR/intel-mpi-library/) em uma VM Linux no Azure provisionada por você. Para isso, depois de se registrar na Intel, siga o link no email de confirmação até a página da Web relacionada e copie o link de download para o arquivo .tgz a fim de obter a versão apropriada do Intel MPI. Este artigo se baseia no Intel MPI versão 5.0.3.048.

### Provisionar uma VM SLES 12

Depois de entrar no Azure com a CLI do Azure, execute `azure config list` para confirmar que a saída mostra o modo **asm**, indicando que a CLI está no modo Gerenciamento de Serviços do Azure. Se não estiver, defina o modo executando este comando:

```
azure config mode asm
```

Digite o seguinte para listar todas as assinaturas que você está autorizado a usar:

```
azure account list
```

A assinatura ativa atual será identificada com `Current` definido como `true`. Se esta não for a assinatura que você deseja usar para criar o cluster, defina o número de assinatura apropriado como a assinatura ativa:

```
azure account set <subscription-number>
```

Para ver as imagens SLES 12 HPC publicamente disponíveis no Azure, execute um comando semelhante ao seguinte, supondo que o ambiente de shell seja compatível com **grep**:

```
azure vm image list | grep "suse.*hpc"
```

>[AZURE.NOTE]As imagens SLES 12 HPC são pré-configuradas com os drivers de RDMA do Linux necessários para o Azure.

Agora, provisione uma VM de tamanho A9 com uma imagem SLES 12 HPC disponível executando um comando semelhante ao seguinte:

```
azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708
```

onde

* o tamanho (A9 neste exemplo) pode ser A8 ou A9

* o número da porta SSH externa (22 neste exemplo, que é o padrão SSH) é qualquer número de porta válido; o número da porta SSH interna será definido como 22

* será criado um novo serviço de nuvem na região do Azure especificada pelo local; especifique um local como "Oeste dos EUA" em que as instâncias A8 e A9 estejam disponíveis

* o nome da imagem atualmente pode ser `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` (gratuito) ou `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-priority-v20150708` para suporte de prioridade SUSE (taxas são cobradas)

### Personalizar a VM

Depois que a VM concluir o provisionamento, faça SSH na VM usando o endereço IP externo da VM (ou o nome DNS) e o número da porta externa configurada, e personalize-a. Para obter detalhes de conexão, consulte [Como fazer logon em uma máquina virtual executando o Linux](virtual-machines-linux-how-to-log-on.md). Você deve executar comandos como o usuário configurado na VM, a menos que o acesso à raiz seja necessário para concluir uma etapa.

>[AZURE.IMPORTANT]O Microsoft Azure não oferece acesso à raiz para VMs do Linux. Para obter acesso administrativo quando estiver conectado como um usuário à VM, execute comandos usando `sudo`.

*   **Atualizações**: instale atualizações usando o **zypper**. Talvez você também queira instalar utilitários NFS.  

    >[AZURE.IMPORTANT]Neste momento, é recomendável que você não aplique as atualizações de kernel, que podem causar problemas com os drivers de RDMA do Linux.

*   **Intel MPI**: baixe e instale o tempo de execução do Intel MPI Library 5 do site Intel.com, executando comandos semelhantes aos que se seguem.

    ```
    sudo wget <download link for your registration>

    sudo tar xvzf <tar-file>

    cd <mpi-directory>

    sudo ./install.sh

    ```

    Aceite as configurações padrão para instalar o Intel MPI na VM.

*   **Bloquear memória**: para os códigos do MPI bloquearem a memória disponível para RDMA, você precisa adicionar ou alterar as configurações a seguir no arquivo /etc/security/limits.conf. (Você precisará de acesso à raiz para editar esse arquivo.)

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

    >[AZURE.NOTE]Para fins de teste, também é possível definir memlock como ilimitado. Por exemplo: `<User or group name>    hard    memlock unlimited`.


*   **Chaves SSH**: gere chaves SSH para estabelecer confiança para sua conta de usuário entre todos os nós de computação no cluster ao executar trabalhos MPI. Execute o comando a seguir para criar chaves SSH. Basta pressionar Enter para gerar as chaves no local padrão, sem definir uma senha.

    ```
    ssh-keygen
    ```

    Acrescente a chave pública ao arquivo authorized\_keys para chaves públicas conhecidas.

    ```
    cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
    ```

    No diretório ~/.ssh, edite ou crie o arquivo “config”. Forneça o intervalo de endereços IP da rede privada que você usará no Azure (neste exemplo, 10.32.0.0/16):

    ```
    host 10.32.0.*
    StrictHostKeyChecking no
    ```

    Como alternativa, liste o endereço IP da rede privada de cada VM no cluster da seguinte maneira:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]A configuração de `StrictHostKeyChecking no` pode criar um risco de segurança potencial se um determinado endereço IP ou intervalo não for especificado como mostrado nesses exemplos.

* **Aplicativos**: instale todos os aplicativos necessários nessa VM ou execute outras personalizações antes de capturar a imagem.

### Capturar a imagem

Para capturar a imagem, primeiramente execute o comando a seguir na VM do Linux. Isso desprovisiona a VM, mas mantém as contas de usuário e chaves SSH que você configurou.

```
sudo waagent -deprovision
```

Em seguida, no computador cliente, execute os seguintes comandos da CLI do Azure para capturar a imagem. Consulte [Como capturar uma máquina virtual Linux para usar como um modelo](virtual-machines-linux-capture-image.md) para obter detalhes.

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Depois de executar esses comandos, a imagem da VM é capturada para seu uso e a VM é excluída. Agora você tem a imagem personalizada pronta para implantar um cluster.

### Implantar um cluster com a imagem

Modifique o script Bash a seguir com os valores apropriados para seu ambiente e execute-o no computador cliente. Como o método de implantação do Gerenciamento de Serviços implanta as VMs em série, a implantação das 8 VMs A9 sugeridas neste script demora um pouco.

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

# Save this script with a name like makecluster.sh and run it in your shell environnment to provision your cluster
```
## Configurar e executar o Intel MPI

Para executar aplicativos MPI em RDMA do Linux no Azure, é preciso configurar determinadas variáveis de ambiente específicas para o Intel MPI. Veja um script Bash de exemplo para configurar as variáveis e executar um aplicativo.

```
#!/bin/bash -x

source /opt/intel/impi_latest/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLEAND MUST BE SET BEFORE RUNNING ANY JOB
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
source /opt/intel/impi_latest/bin64/mpivars.sh
```

### Executar um comando MPI simples

Execute um comando MPI simples em um dos nós de computação para mostrar que o MPI está instalado corretamente e pode se comunicar entre pelo menos dois nós de computação. O comando **mpirun** a seguir executa o comando **hostname** em dois nós.

```
/opt/intel/impi_latest/bin64/mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
A saída deve listar os nomes de todos os nós passados como entrada para `-hosts`. Por exemplo, um comando **mpirun** com dois nós retornará uma saída semelhante à seguinte:

```
cluster11
cluster12
```

### Executar um parâmetro de comparação de MPI

O comando Intel MPI a seguir verifica a configuração do cluster e a conexão com a rede RDMA usando um parâmetro de comparação de pingpong.

```
/opt/intel/impi_latest/bin64/mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
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

## Considerações de topologia de rede

* Em VMs Linux, Eth1 é reservada para o tráfego de rede RDMA. Não altere as configurações de Eth1 ou as informações no arquivo de configuração que se refere a essa rede.

* IP sobre Infiniband (IB) não é compatível com o Azure. Apenas RDMA sobre IB é compatível.

* Em VMs Linux, Eth0 é reservado para o tráfego de rede do Azure regular.

## Próximas etapas

* Tente implantar e executar os aplicativos Linux MPI no cluster do Linux.

* Consulte a [Documentação do Intel MPI Library](https://software.intel.com/pt-BR/articles/intel-mpi-library-documentation/) para obter orientação sobre o Intel MPI.

<!---HONumber=Nov15_HO3-->