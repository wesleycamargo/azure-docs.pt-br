---
title: Criar e carregar um VHD Linux baseado em CentOS no Azure
description: "Saiba como criar e carregar um VHD (disco rígido virtual) do Azure que contenha um sistema operacional Linux baseado em CentOS."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 0e518e92-e981-43f4-b12c-9cba1064c4bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/09/2016
ms.author: szarkos
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 6919a8c2147db4f8c86230e336540c8c88c714e1


---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Preparar uma máquina virtual baseada em CentOS para o Azure
* [Preparar uma máquina virtual CentOS 6.x para o Azure](#centos-6x)
* [Preparar uma máquina virtual CentOS 7.0 ou posterior para o Azure](#centos-70)

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já instalou um sistema operacional Linux CentOS (ou derivado similar) em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd, por exemplo, uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação do CentOS**

* Veja também [Notas de instalação gerais do Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* O formato VHDX não tem suporte no Azure, somente o **VHD fixo**.  Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.
* Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas.  Se você preferir, é possível usar [LVM](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) em discos de dados.
* Não há suporte para NUMA para tamanhos de máquinas virtuais maiores devido a um bug nas versões do kernel Linux abaixo de 2.6.37. Esse problema afeta principalmente distribuições que usam o kernel upstream do Red Hat 2.6.32. A instalação manual do agente Linux do Azure (waagent) desabilita a NUMA automaticamente na configuração do GRUB para o kernel do Linux. Verifique as etapas a seguir para obter mais informações a esse respeito.
* Não configure uma partição de permuta no disco do SO. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários.  Verifique as etapas a seguir para obter mais informações a esse respeito.
* Todos os VHDs devem ter tamanhos que sejam múltiplos de 1 MB.

## <a name="centos-6x"></a>CentOS 6.x
1. No Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **Conectar** para abrir a janela do console para a máquina virtual.
3. Desinstale o NetworkManager executando o seguinte comando:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Observação:** se o pacote ainda não estiver instalado, esse comando irá falhar com uma mensagem de erro. Isso é esperado.
4. Crie um arquivo chamado **network** in the `/etc/sysconfig/` que contém o seguinte texto:
   
     NETWORKING=yes   HOSTNAME=localhost.localdomain
5. Crie um arquivo chamado **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` que contém o seguinte texto:
   
     DEVICE=eth0   ONBOOT=yes   BOOTPROTO=dhcp   TYPE=Ethernet   USERCTL=no   PEERDNS=yes   IPV6INIT=no
6. Modifique as regras de udev para evitar a geração de regras estáticas das interfaces Ethernet. Essas regras podem provocar problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:
   
   # <a name="sudo-ln--s-devnull-etcudevrulesd75-persistent-net-generatorrules"></a>sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
   # <a name="sudo-rm--f-etcudevrulesd70-persistent-netrules"></a>sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Certifique-se de que o serviço de rede será iniciado na inicialização executando o seguinte comando:
   
        # sudo chkconfig network on
8. **Somente CentOS 6.3**: instale os drivers dos LIS (Serviços de Integração do Linux).
   
    **Importante: essa etapa só é válida para o CentOS 6.3 e versões anteriores.**  Os serviços de integração do Linux nas versões 6.4 ou posteriores do CentOS *já estão disponíveis no kernel padrão*.
   
   * Siga as instruções de instalação na [página de download do LIS](https://www.microsoft.com/en-us/download/details.aspx?id=46842) e instale o RPM em sua imagem.  
9. Execute o comando a seguir para instalar o pacote python-pyasn1:
   
        # sudo yum install python-pyasn1
10. Se quiser usar os espelhos OpenLogic hospedados em datacenters Azure, substitua o arquivo /etc/yum.repos.d/CentOS-Base.repo com os repositórios mencionados a seguir.  Essa ação adiciona o repositório **[openlogic]** que inclui pacotes para o agente Linux do Azure:
    
        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0
    
        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
    
        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
    
        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
    
        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
    
        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
    
    **Observação:** O restante deste guia parte do pressuposto de que você está usando, no mínimo, o repositório [openlogic], que será usado para instalar o agente Linux do Azure abaixo.
11. Adicione a seguinte linha a /etc/yum.conf:
    
     http_caching=packages
    
    E **no CentOS 6.3** adicione apenas a seguinte linha:
    
     exclude=kernel*
12. Desabilite o módulo yum "fastestmirror", editando o arquivo "/etc/yum/pluginconf.d/fastestmirror.conf" e digite o seguinte em `[main]`:
    
        set enabled=0
13. Execute o comando a seguir para limpar os metadados atuais do yum:
    
    # <a name="yum-clean-all"></a>yum clean all
14. **Somente no CentOS 6.3**, atualize o kernel usando este comando:
    
        # sudo yum --disableexcludes=all install kernel
15. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra "/boot/grub/menu.lst" em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:
    
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
    
    Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração. Essa ação desabilita a NUMA devido a um bug na versão do kernel usada pelo CentOS 6.
    
    Além disso, recomendamos que você *remova* os seguintes parâmetros:
    
     rhgb quiet crashkernel=auto
    
    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.
    
    Você pode deixar configurada a opção `crashkernel` , mas esse parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.
16. Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.  Geralmente, esse é o padrão.
17. Instale o Agente Linux do Azure executando o seguinte comando:
    
        # sudo yum install WALinuxAgent
    
    Observe que a instalação do pacote WALinuxAgent removerá o NetworkManager e os pacotes NetworkManager-gnome se eles já não tiverem sido removidos conforme descrito na etapa 2.
18. Não crie espaço de permuta no disco do SO.
    
    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:
    
     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## OBSERVAÇÃO: defina isso para o que quer que você precise que seja.
19. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:
    
    # <a name="sudo-waagent--force--deprovision"></a>sudo waagent -force -deprovision
    # <a name="export-histsize0"></a>export HISTSIZE=0
    # <a name="logout"></a>logout
20. Clique em **Ação -> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

- - -
## <a name="centos-70"></a>CentOS 7.0+
**Alterações no CentOS 7 (e em derivativos similares)**

A preparação de uma máquina virtual CentOS 7 para o Azure é muito parecida com a preparação das máquinas virtuais CentOS 6, mas há diversas diferenças que merecem atenção:

* O pacote do NetworkManager não entra mais em conflito com o agente Linux do Azure. Esse pacote é instalado por padrão e recomendamos que você não o remova.
* O GRUB2 agora é usado como carregador de inicialização padrão. Com isso, o procedimento de edição de parâmetros do kernel mudou (confira abaixo).
* O XFS agora é o sistema de arquivos padrão. Ainda é possível usar o sistema de arquivos ext4 se você preferir.

**Etapas da configuração**

1. No Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **Conectar** para abrir a janela do console para a máquina virtual.
3. Crie um arquivo chamado **network** in the `/etc/sysconfig/` que contém o seguinte texto:
   
     NETWORKING=yes   HOSTNAME=localhost.localdomain
4. Crie um arquivo chamado **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` que contém o seguinte texto:
   
     DEVICE=eth0   ONBOOT=yes   BOOTPROTO=dhcp   TYPE=Ethernet   USERCTL=no   PEERDNS=yes   IPV6INIT=no
5. Modifique as regras de udev para evitar a geração de regras estáticas das interfaces Ethernet. Essas regras podem provocar problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:
   
   # <a name="sudo-ln--s-devnull-etcudevrulesd75-persistent-net-generatorrules"></a>sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Certifique-se de que o serviço de rede será iniciado na inicialização executando o seguinte comando:
   
        # sudo chkconfig network on
7. Execute o comando a seguir para instalar o pacote python-pyasn1:
   
        # sudo yum install python-pyasn1
8. Se quiser usar os espelhos OpenLogic hospedados em datacenters Azure, substitua o arquivo /etc/yum.repos.d/CentOS-Base.repo com os repositórios mencionados a seguir.  Essa ação adiciona o repositório **[openlogic]** que inclui pacotes para o agente Linux do Azure:
   
        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0
   
        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   
        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   
        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   
        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   
        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

    **Observação:** O restante deste guia parte do pressuposto de que você está usando, no mínimo, o repositório [openlogic], que será usado para instalar o agente Linux do Azure abaixo.

1. Execute o comando a seguir para limpar os metadados atuais do yum e instalar atualizações:
   
   # <a name="sudo-yum-clean-all"></a>sudo yum clean all
   # <a name="sudo-yum--y-update"></a>sudo yum -y update
2. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra "/etc/default/grub" em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX` , por exemplo:
   
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração. Ele também desativa novas convenções de nomenclatura do CentOS 7 para NICs. Além disso, recomendamos que você *remova* os seguintes parâmetros:
   
    rhgb quiet crashkernel=auto
   
   As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.
   
   Você pode deixar configurada a opção `crashkernel` , mas esse parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.
3. Depois de editar "/etc/default/grub" como mostramos acimo, execute o comando a seguir para recompilar a configuração do grub:
   
       # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
4. Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.  Geralmente, esse é o padrão.
5. **Somente se estiver criando a imagem a partir do VMWare, VirtualBox ou KVM:** adicione módulos Hyper-V no initramfs:
   
   Edite `/etc/dracut.conf`e adicione o conteúdo:
   
    add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”
   
   Recompile o initramfs:
   
   # <a name="dracut-f--v"></a>dracut –f -v
6. Instale o Agente Linux do Azure executando o seguinte comando:
   
       # sudo yum install WALinuxAgent
       # sudo systemctl enable waagent
7. Não crie espaço de permuta no disco do SO.
   
   O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:
   
    ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## OBSERVAÇÃO: defina isso para o que quer que você precise que seja.
8. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:
   
   # <a name="sudo-waagent--force--deprovision"></a>sudo waagent -force -deprovision
   # <a name="export-histsize0"></a>export HISTSIZE=0
   # <a name="logout"></a>logout
9. Clique em **Ação -> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

## <a name="next-steps"></a>Próximas etapas
Agora, você está pronto para usar o disco rígido virtual CentOS Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que você estiver carregando o arquivo .vhd para o Azure, veja as etapas 2 e 3 em [Criando e carregando um disco rígido virtual que contém o sistema operacional Linux](virtual-machines-linux-classic-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).




<!--HONumber=Nov16_HO3-->


