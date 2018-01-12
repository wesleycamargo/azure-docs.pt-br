---
title: Configurar DHCPv6 para VMs Linux | Microsoft Docs
description: Como configurar DHCPv6 para VMs Linux.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
keywords: "ipv6, azure load balancer, pilha dual, ip público, ipv6 nativo, móvel, iot"
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: b46c2107dcfda5f02407e08daf08bd42d722dfda
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2017
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Configurar DHCPv6 para VMs Linux

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Algumas das imagens de máquina virtual do Linux no Azure Marketplace não tem o Dynamic Host Configuration Protocol version 6 (DHCPv6) configurado por padrão. Para oferecer suporte a IPv6, o DHCPv6 deve ser configurado na distribuição do SO Linux que você está usando. Várias distribuições do Linux configuram o DHCPv6 em uma grande variedade de modos porque elas usam diferentes pacotes.

> [!NOTE]
> Imagens recentes do SUSE Linux e CoreOS no Azure Marketplace foram pré-configuradas com DHCPv6. Nenhuma alteração adicional é necessária ao usar essas imagens.

Este documento descreve como habilitar o DHCPv6 para que a sua máquina virtual Linux obtenha um endereço IPv6.

> [!WARNING]
> Editando inadequadamente arquivos de configuração de rede, você pode perder acesso à rede para sua VM. Recomendamos que você teste as alterações de configuração em sistemas de não produção. As instruções neste artigo foram testadas em versões mais recentes das imagens do Linux no Azure Marketplace. Para obter instruções detalhadas, consulte a documentação para a versão específica do Linux.

## <a name="ubuntu"></a>Ubuntu

1. Edite o arquivo */etc/dhcp/dhclient6.conf* e adicione a seguinte linha:

        timeout 10;

2. Edite a configuração de rede para a interface eth0 com a seguinte configuração:

   * Em **12.04 Ubuntu e 14.04**, edite o arquivo */etc/network/interfaces.d/eth0.cfg*. 
   * Em **Ubuntu 16.04**, edite o arquivo */etc/network/interfaces.d/50-cloud-init.cfg*.

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Edite o arquivo */etc/dhcp/dhclient6.conf* e adicione a seguinte linha:

        timeout 10;

2. Edite o arquivo */etc/network/interfaces* e adicione a configuração a seguir:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS e Oracle Linux

1. Edite o arquivo */etc/sysconfig/network* e adicione o parâmetro a seguir:

        NETWORKING_IPV6=yes

2. Edite o arquivo */etc/sysconfig/network-scripts/ifcfg-eth0* e adicione estes dois parâmetros:

        IPV6INIT=yes
        DHCPV6C=yes

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 e openSUSE 13

Imagens recentes de SUSE Linux Enterprise Server (SLES) e openSUSE no Azure foram pré-configuradas com DHCPv6. Nenhuma alteração adicional é necessária ao usar essas imagens. Se você tiver uma VM baseada em uma imagem SUSE mais antiga ou personalizada, faça o seguinte:

1. Instale o pacote `dhcp-client` , se for necessário:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Edite o arquivo */etc/sysconfig/network/ifcfg-eth0* e adicione o parâmetro a seguir:

        DHCLIENT6_MODE='managed'

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 e openSUSE Leap

Imagens recentes de SLES e openSUSE no Azure foram pré-configuradas com DHCPv6. Nenhuma alteração adicional é necessária ao usar essas imagens. Se você tiver uma VM baseada em uma imagem SUSE mais antiga ou personalizada, faça o seguinte:

1. Edite o arquivo */etc/sysconfig/network/ifcfg-eth0* e substitua o parâmetro `#BOOTPROTO='dhcp4'` pelo seguinte valor:

        BOOTPROTO='dhcp'

2. Para o arquivo */etc/sysconfig/network/ifcfg-eth0*, adicione o parâmetro a seguir:

        DHCLIENT6_MODE='managed'

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Imagens recentes de CoreOS no Azure foram pré-configuradas com DHCPv6. Nenhuma alteração adicional é necessária ao usar essas imagens. Se você tiver uma VM baseada em uma imagem CoreOS mais antiga ou personalizada, faça o seguinte:

1. Edite o arquivo */etc/systemd/network/10_dhcp.network*:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Renove o endereço IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
