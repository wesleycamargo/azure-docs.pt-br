---
title: "Otimizar a taxa de transferência de rede de VM | Microsoft Docs"
description: "Aprenda a otimizar a taxa de transferência de rede de máquina virtual do Azure."
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: steveesp
ms.openlocfilehash: d77440fe62bbd0e720e5ae60b15574dacc4180c0
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Otimizar a taxa de transferência de rede para máquinas virtuais do Azure

Máquinas virtuais do Azure (VM) têm configurações de rede padrão que podem ser mais otimizadas para taxa de transferência de rede. Este artigo descreve como otimizar a taxa de transferência de rede para VMs Windows e Linux do Microsoft Azure, incluindo as distribuições principais como o Ubuntu, CentOS e Red Hat.

## <a name="windows-vm"></a>VM Windows

Se sua VM do Windows tiver suporte para [Rede Acelerada](virtual-network-create-vm-accelerated-networking.md), habilitar esse recurso será a configuração ideal para a taxa de transferência. Para todas as outras VMs do Windows, usar RSS (Receive Side Scaling) pode alcançar uma taxa de transferência máxima maior que uma VM sem RSS. RSS pode ser desabilitado por padrão em uma VM do Windows. Conclua as seguintes etapas para determinar se o RSS está habilitado e habilitá-lo se ele estiver desabilitado.

1. Insira o `Get-NetAdapterRss` comando do PowerShell para ver se o RSS está habilitado para um adaptador de rede. Na saída do exemplo seguinte retornada do `Get-NetAdapterRss`, RSS não está habilitado.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : False
    ```
2. Digite o comando a seguir para habilitar o RSS:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    O comando anterior não tem uma saída. O comando alterou configurações de NIC, causando uma perda de conectividade temporária por aproximadamente um minuto. Aparece uma caixa de diálogo Reconnecting durante a perda de conectividade. Normalmente, a conectividade for restaurada após a terceira tentativa.
3. Confirme se o RSS está habilitado na VM inserindo o `Get-NetAdapterRss` comando novamente. Se for bem-sucedido, será retornada a seguinte saída de exemplo:

    ```powershell
    Name                    :Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : True
    ```

## <a name="linux-vm"></a>VM Linux

RSS está sempre habilitado por padrão em uma VM do Linux do Azure. Kernels do Linux liberados desde outubro de 2017 incluem novas opções de otimização de rede que permitem que uma VM Linux obtenha maior taxa de transferência de rede.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu para novas implantações

Para obter a otimização, primeiro instale a última versão com suporte do 16.04-LTS, da seguinte maneira:
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
Depois que a atualização for concluída, digite os seguintes comandos para obter o kernel mais recente:

```bash
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
```

Comando opcional:

`apt-get -y dist-upgrade`
#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Atualização de kernel do Azure no Ubuntu para VMs existentes

Um desempenho de taxa de transferência significativo pode ser obtido com o upgrade para o kernel do Linux no Azure. Para verificar se você tem esse kernel, verifique a versão do kernel.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.11.0-1014-azure
```

Em seguida, execute estes comandos como raiz.
```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Para obter as últimas otimizações, primeiro atualize para a última versão com suporte, da seguinte maneira:
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```
Depois que a atualização for concluída, instale o LIS (Linux Integration Services) mais recente.
A otimização de taxa de transferência está no LIS, começando a partir de 4.2.2-2, embora as versões posteriores contenham mais melhorias. Insira os seguintes comandos para instalar o último LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Para obter a otimização, primeiro atualize para a última versão com suporte, da seguinte maneira:
```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```
Depois que a atualização for concluída, instale o LIS (Linux Integration Services) mais recente.
A otimização de taxa de transferência é feita no LIS, a partir da versão 4.2. Digite os comandos a seguir para baixar e instalá-los:

```bash
mkdir lis4.2.3-1
cd lis4.2.3-1
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-1.tar.gz
tar xvzf lis-rpms-4.2.3-1.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Saiba mais sobre o Linux Integration Services versão 4.2 para o Hyper-V exibindo a [página de download](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Próximas etapas
* Agora que a VM está otimizada, veja o resultado com [Teste de Largura de Banda/Taxa de Transferência da VM do Azure](virtual-network-bandwidth-testing.md) para seu cenário.
* Saiba mais com as [Perguntas frequentes sobre a rede virtual do Azure](virtual-networks-faq.md)
