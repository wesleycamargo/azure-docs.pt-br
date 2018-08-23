---
title: DPDK em uma VM do Azure Linux | Microsoft Docs
description: Aprenda como configurar o DPDK em uma máquina virtual Linux.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: 205a1e399eadd268ffaa390a7ebb4397fda9feff
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444646"
---
# <a name="setup-dpdk-in-a-linux-virtual-machine"></a>Configurar o DPDK em uma máquina virtual Linux

O Data Plane Development Kit (DPDK) no Azure oferece uma estrutura de processamento de pacotes de espaço do usuário mais rápida para aplicativos com uso intenso de desempenho que ignoram a pilha de rede do kernel da máquina virtual.

O processamento típico de pacotes usando a pilha de rede do kernel é controlado por interrupção. Cada vez que a interface de rede recebe pacotes de entrada, há uma interrupção do kernel para processar o pacote e o comutador de contexto do espaço do kernel para o espaço do usuário. O DPDK elimina a alternância de contexto e o método acionado por interrupção em favor de uma implementação de espaço do usuário usando drivers de modo de pesquisa para processamento rápido de pacotes.

O DPDK consiste em um conjunto de bibliotecas de espaço de usuário que fornece acesso a recursos de nível inferior, como hardware, núcleos lógicos, gerenciamento de memória e drivers de modo de pesquisa para placas de interface de rede.

O DPDK pode ser executado em máquinas virtuais do Azure, suportando várias distribuições do sistema operacional. O DPDK fornece uma diferenciação de desempenho chave na condução de implementações de virtualização de funções de rede, na forma de dispositivos virtuais de rede (NVA), como roteador virtual, firewall, VPN, balanceador de carga, núcleo de pacote evoluído e aplicativos de negação de serviço (DDoS). 

## <a name="benefit"></a>Benefício

**Pacotes superiores por segundo (PPS)**: Ignorando o kernel e controlando os pacotes no espaço do usuário reduz a contagem de ciclos eliminando a alternância de contexto e melhorando a taxa de pacotes processados por segundo nas máquinas virtuais do Azure Linux.


## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

As seguintes distribuições da Galeria do Azure são suportadas:

| Sistema operacional Linux     | Versão do kernel        |
|--------------|----------------       |
| Ubuntu 16.04 | 4.15.0-1015-azure     |
| Ubuntu 18.04 | 4.15.0-1015-azure     |
| SLES 15      | 4.12.14-5.5-azure     |
| RHEL 7.5     | 3.10.0-862.9.1.el7    |
| CentOS 7.5   | 3.10.0-862.3.3.el7    |

**Suporte a kernel personalizado**

Consulte [Correções para construir um kernel Linux ajustado ao Azure](https://github.com/microsoft/azure-linux-kernel) para qualquer versão do kernel Linux não listada ou, para obter mais informações, entre em contato com [azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com). 

## <a name="region-support"></a>Suporte de regiões

Todas as regiões do Azure dão suporte a DPDK.

## <a name="prerequisites"></a>Pré-requisitos

A rede acelerada deve ser habilitada em uma máquina virtual Linux. A máquina virtual deve ter pelo menos dois adaptadores de rede, com uma interface de gerenciamento. Saiba como [criar uma máquina virtual Linux com rede acelerada habilitada](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>Instalar dependências DPDK

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15"></a>SLES 15

**Kernel do Azure**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Kernel padrão**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="setup-virtual-machine-environment-once"></a>Configurar o ambiente de máquina virtual (uma vez)

1. [Baixar a mais recente DPDK](https://core.dpdk.org/download). Version 18.02 or higher is required for Azure.
2. Primeiro, crie a configuração padrão com `make config T=x86_64-native-linuxapp-gcc`.
3. Habilitar Mellanox PMDs a configuração gerada com `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`.
4. Compilar com `make`.
5. Instalar com `make install DESTDIR=<output folder>`.

# <a name="configure-runtime-environment"></a>Configurar o ambiente de tempo de execução

Execute os seguintes comandos de uma vez, após a reinicialização:

1. Hugepages

   * Configure a página de abra executando o seguinte comando, uma vez para todos os numanodes:

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   *  Crie um diretório para montagem com `mkdir /mnt/huge`.
   *  Monte as hugepages com `mount -t hugetlbfs nodev /mnt/huge`.
   *  Verifique se as páginas do abraço estão reservadas com `grep Huge /proc/meminfo`.

     > [!NOTE]
     > Existe uma maneira de modificar o arquivo grub para que as páginas grandes sejam reservadas na inicialização seguindo as [instruções](http://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) para o DPDK. A instrução está na parte inferior da página. Ao executar em uma máquina virtual do Azure Linux, modifique os arquivos em /etc/config/grub.d em vez disso, para reservar páginas amplas nas reinicializações.

2. Endereços MAC e IP: use `ifconfig –a` para visualizar o endereço MAC e IP das interfaces de rede. A interface de rede *VF* e a interface de rede *NETVSC* têm o mesmo endereço MAC, mas apenas a interface de rede *NETVSC* tem um endereço IP. As interfaces VF estão sendo executadas como interfaces escravas das interfaces NETVSC.

3. Endereços PCI

   * Descubra qual endereço PCI usar para *VF* com `ethtool -i <vf interface name>`.
   * Certifique-se de que o testpmd não assuma acidentalmente o dispositivo VF pci para *eth0*, se *eth0* tiver acelerado a rede ativada. Se o aplicativo DPDK tiver assumido acidentalmente a interface de rede de gerenciamento e causar perda de sua conexão SSH, use o console serial para eliminar o aplicativo DPDK ou parar ou iniciar a máquina virtual.

4. Carga *ibuverbs* em cada reinicialização com `modprobe -a ib_uverbs`. Para apenas 15 SLES, também carrega *mlx4_ib* com `modprobe -a mlx4_ib`.

## <a name="failsafe-pmd"></a>PMD à prova de falhas

Os aplicativos DPDK devem ser executados sobre o PMD à prova de falhas exposto no Azure. Se o aplicativo for executado diretamente sobre o PMD de VF, ele não receberá **todos** os pacotes destinados à VM, pois alguns pacotes aparecerão na interface sintética. A execução do PMD à prova de falhas garante que o aplicativo receba todos os pacotes destinados a ele e também garante que o aplicativo continuará sendo executado no modo DPDK, mesmo se a VF for revogada quando o host estiver sendo atendido. Para obter mais informações sobre o PMD à prova de falhas, consulte a [biblioteca de drivers do modo de pesquisa à prova de falhas](http://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Executar testpmd

Use `sudo` antes do comando *testpmd* para executar no modo raiz.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Basic: Verificação de integridade, a inicialização do adaptador à prova de falhas

1. Execute os seguintes comandos para iniciar um aplicativo testpmd de porta única:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Execute os seguintes comandos para iniciar um aplicativo testpmd de porta dupla:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Se estiver executando com mais de 2 NICs, o argumento `--vdev` segue este padrão: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`.

3.  Depois de iniciado, execute `show port info all` para verificar as informações de porta. Você deve ver uma ou duas portas DPDK que são net_failsafe (não *net_mlx4*).
4.  Use `start <port> /stop <port>` para iniciar o tráfego.

Os comandos anteriores iniciam *testpmd* no modo interativo, o que é recomendado, para testar alguns comandos testpmd.

### <a name="basic-single-sendersingle-receiver"></a>Básico: Receptor de único remetente/única

Os seguintes comandos periodicamente imprimem as estatísticas de pacotes por segundo:

1. No lado do TX, execute o seguinte comando:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. No lado do RX, execute o seguinte comando:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Ao executar os comandos anteriores em uma máquina virtual, altere *IP_SRC_ADDR* e *IP_DST_ADDR* em `app/test-pmd/txonly.c` para corresponder ao endereço IP real das máquinas virtuais antes de compilar. Caso contrário, os pacotes são descartados antes de chegar ao receptor.

### <a name="advanced-single-sendersingle-forwarder"></a>Avançado: Encaminhador de único remetente/único
Os seguintes comandos periodicamente imprimem as estatísticas de pacotes por segundo:

1. No lado do TX, execute o seguinte comando:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. No lado do FWD, execute o seguinte comando:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

Ao executar os comandos anteriores em uma máquina virtual, altere *IP_SRC_ADDR* e *IP_DST_ADDR* em `app/test-pmd/txonly.c` para corresponder ao endereço IP real das máquinas virtuais antes de compilar. Caso contrário, os pacotes são descartados antes de alcançar o encaminhador. Você não poderá fazer com que um terceiro computador receba o tráfego encaminhado, porque o encaminhador *testpmd* não modifica os endereços da camada 3, a menos que você faça algumas alterações no código.

## <a name="references"></a>Referências

* [Opções EAL](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Comandos de Testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
