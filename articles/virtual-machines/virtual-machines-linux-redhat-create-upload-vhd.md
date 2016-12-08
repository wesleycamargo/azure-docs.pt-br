---
title: Criar e carregar um VHD Linux do Red Hat Enterprise para uso no Azure
description: "Saiba como criar e carregar um disco rígido virtual (VHD) do Microsoft Azure, que contém um sistema operacional Red Hat Linux."
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/17/2016
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: 968b7af74977428e12a109fab382efeeeffae9de
ms.openlocfilehash: 5127e61dc45ccf3048dd18cf11d5b0915125f814


---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Preparar uma máquina virtual baseada no Red Hat para o Azure
Neste artigo, você aprenderá como preparar uma máquina virtual do Red Hat Enterprise Linux (RHEL) para usar no Azure. As versões do RHEL que são abordadas neste artigo são 6.7, 7.1 e 7.2. Os hipervisores de preparação que são abordados neste artigo são Hyper-V, máquina virtual baseada em kernel (KVM) e VMware. Para saber mais sobre os requisitos de qualificação para participação no programa de Acesso à Nuvem do Red Hat, confira o [site Red Hat's Cloud Access](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) e [Running RHEL on Azure](https://access.redhat.com/articles/1989673).

[Preparar uma máquina virtual RHEL 6.7 do Gerenciador do Hyper-V](#rhel67hyperv)

[Preparar uma máquina virtual RHEL 7.1/7.2 do Gerenciador do Hyper-V](#rhel7xhyperv)

[Preparar uma máquina virtual RHEL 6.7 do KVM](#rhel67kvm)

[Preparar uma máquina virtual RHEL 7.1/7.2 do KVM](#rhel7xkvm)

[Preparar uma máquina virtual RHEL 6.7 do VMware](#rhel67vmware)

[Preparar uma máquina virtual RHEL 7.1/7.2 do VMware](#rhel7xvmware)

[Preparar uma máquina virtual RHEL 7.1/7.2 de um arquivo de início rápido](#rhel7xkickstart)

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Preparar uma máquina virtual baseada em Red Hat a partir do Gerenciador do Hyper-V
### <a name="prerequisites"></a>Pré-requisitos
Esta seção pressupõe que você já instalou uma imagem RHEL (a partir de um arquivo ISO obtido no site do Red Hat) em um disco rígido virtual (VHD). Para obter mais detalhes sobre como usar o Gerenciador do Hyper-V para instalar uma imagem do sistema operacional, consulte [Instalar a função Hyper-V e configurar uma máquina virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação do RHEL**

* Veja também [Notas gerais de instalação do Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* Não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco para o formato VHD usando o Gerenciador do Hyper-V ou o cmdlet **convert-vhd** do PowerShell.
* Os VHDs devem ser criados como "fixed" (fixos), VHDs dinâmicos não são suportados.
* Ao instalar o sistema Linux, é recomendável utilizar partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. Se você preferir, é possível usar LVM ou [RAID](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) em discos de dados.
* Não configure uma partição de permuta no disco do SO. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários. Verifique as etapas a seguir para obter mais informações a esse respeito.
* Todos os VHDs devem ter tamanhos que sejam múltiplos de 1 MB.
* Ao usar **qemu-img** para converter as imagens de disco em formato VHD, observe que há um bug conhecido nas versões 2.2.1 ou posteriores do qemu-img. Esse bug resulta em um VHD formatado incorretamente. O problema será corrigido em uma versão futura do qemu-img. Por enquanto, é recomendável usar o qemu-img versão 2.2.0 ou anterior.

### <a id="rhel67hyperv"> </a>Preparar uma máquina virtual RHEL 6.7 do Gerenciador do Hyper-V
1. No Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **Conectar** para abrir a janela do console para a máquina virtual.
3. Desinstale o NetworkManager executando o seguinte comando:

   # <a name="sudo-rpm--e---nodeps-networkmanager"></a>sudo rpm -e --nodeps NetworkManager
   Observe que se o pacote ainda não estiver instalado, esse comando irá falhar com uma mensagem de erro. Isso é esperado.
4. Crie um arquivo chamado **network** in the `/etc/sysconfig/` que contém o seguinte texto:

     NETWORKING=yes   HOSTNAME=localhost.localdomain
5. Crie um arquivo chamado **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` que contém o seguinte texto:

     DEVICE=eth0   ONBOOT=yes   BOOTPROTO=dhcp   TYPE=Ethernet   USERCTL=no   PEERDNS=yes   IPV6INIT=no
6. Mova (ou remova) as regras de udev para evitar a geração de regras estáticas da interface Ethernet. Essas regras causam problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:

   # <a name="sudo-mkdir--m-0700-varlibwaagent"></a>sudo mkdir -m 0700 /var/lib/waagent
   # <a name="sudo-mv-libudevrulesd75-persistent-net-generatorrules-varlibwaagent"></a>sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
   # <a name="sudo-mv-etcudevrulesd70-persistent-netrules-varlibwaagent"></a>sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/
7. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

   # <a name="sudo-chkconfig-network-on"></a>sudo chkconfig network on
8. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

   # <a name="sudo-subscription-manager-register---auto-attach---usernamexxx---passwordxxx"></a>sudo subscription-manager register --auto-attach --username=XXX --password=XXX
9. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

   # <a name="subscription-manager-repos---enablerhel-6-server-extras-rpms"></a>subscription-manager repos --enable=rhel-6-server-extras-rpms
10. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra `/boot/grub/menu.lst` em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:

     console=ttyS0  earlyprintk=ttyS0  rootdelay=300  numa=off

    Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Essa ação desabilitará o NUMA devido a um bug na versão do kernel usada pelo RHEL 6.

    Além da ação acima, recomendamos a remoção dos seguintes parâmetros:

     rhgb quiet crashkernel=auto

    As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.

    A opção crashkernel pode ser deixada configurada se desejado, mas é importante lembrar que esse parâmetro reduzirá a quantidade de memória disponível na VM em 128 MB ou mais. Isso pode ser problemático em tamanhos de VM menores.
11. Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização. Geralmente, esse é o padrão. Modifique o /etc/ssh/sshd_config para incluir a seguinte linha:

     ClientAliveInterval 180
12. Instale o Agente Linux do Azure executando o seguinte comando:

    # <a name="sudo-yum-install-walinuxagent"></a>sudo yum install WALinuxAgent
    # <a name="sudo-chkconfig-waagent-on"></a>sudo chkconfig waagent on
    Observe que a instalação do pacote WALinuxAgent removerá o NetworkManager e os pacotes NetworkManager-gnome se eles já não tiverem sido removidos conforme descrito na etapa 2.
13. Não crie espaço de permuta no disco do SO.
    O Agente Linux do Azure pode configurar automaticamente o espaço de troca usando o disco de recurso local que é anexado à VM após o provisionamento da mesma no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## OBSERVAÇÃO: defina isso para o que quer que você precise que seja.
14. Cancele o registro da assinatura (se necessário) executando o seguinte comando:

    # <a name="sudo-subscription-manager-unregister"></a>sudo subscription-manager unregister
15. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    # <a name="sudo-waagent--force--deprovision"></a>sudo waagent -force -deprovision
    # <a name="export-histsize0"></a>export HISTSIZE=0
    # <a name="logout"></a>logout
16. Clique em **Ação > Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.  

### <a id="rhel7xhyperv"> </a>Preparar uma máquina virtual RHEL 7.1/7.2 do Gerenciador do Hyper-V
1. No Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **Conectar** para abrir a janela do console para a máquina virtual.
3. Crie um arquivo chamado **network** in the `/etc/sysconfig/` que contém o seguinte texto:

     NETWORKING=yes   HOSTNAME=localhost.localdomain
4. Crie um arquivo chamado **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` que contém o seguinte texto:

     DEVICE=eth0   ONBOOT=yes   BOOTPROTO=dhcp   TYPE=Ethernet   USERCTL=no   PEERDNS=yes   IPV6INIT=no
5. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

   # <a name="sudo-chkconfig-network-on"></a>sudo chkconfig network on
6. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

   # <a name="sudo-subscription-manager-register---auto-attach---usernamexxx---passwordxxx"></a>sudo subscription-manager register --auto-attach --username=XXX --password=XXX
7. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra `/etc/default/grub` em um editor de texto e edite o parâmetro **GRUB_CMDLINE_LINUX**. Por exemplo:

     GRUB_CMDLINE_LINUX="rootdelay=300   console=ttyS0   earlyprintk=ttyS0"

   Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Além da ação acima, recomendamos a remoção dos seguintes parâmetros:

     rhgb quiet crashkernel=auto

   As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.
   A opção crashkernel pode ser deixada configurada se desejado, mas é importante lembrar que esse parâmetro reduzirá a quantidade de memória disponível na VM em 128 MB ou mais. Isso pode ser problemático em tamanhos de VM menores.
8. Depois de editar `/etc/default/grub`, execute o comando a seguir para recompilar a configuração do grub:

   # <a name="sudo-grub2-mkconfig--o-bootgrub2grubcfg"></a>sudo grub2-mkconfig -o /boot/grub2/grub.cfg
9. Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização. Geralmente, esse é o padrão. Modifique o `/etc/ssh/sshd_config` para incluir a seguinte linha:

     ClientAliveInterval 180
10. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

    # <a name="subscription-manager-repos---enablerhel-7-server-extras-rpms"></a>subscription-manager repos --enable=rhel-7-server-extras-rpms
11. Instale o Agente Linux do Azure executando o seguinte comando:

    # <a name="sudo-yum-install-walinuxagent"></a>sudo yum install WALinuxAgent
    # <a name="sudo-systemctl-enable-waagentservice"></a>sudo systemctl enable waagent.service
12. Não crie espaço de permuta no disco do SO. O Agente Linux do Azure pode configurar automaticamente o espaço de troca usando o disco de recurso local que é anexado à VM após o provisionamento da mesma no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique adequadamente os seguintes parâmetros em `/etc/waagent.conf` :

     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## OBSERVAÇÃO: defina isso para o que quer que você precise que seja.
13. Se você quiser cancelar o registro da assinatura, execute o seguinte comando:

    # <a name="sudo-subscription-manager-unregister"></a>sudo subscription-manager unregister
14. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    # <a name="sudo-waagent--force--deprovision"></a>sudo waagent -force -deprovision
    # <a name="export-histsize0"></a>export HISTSIZE=0
    # <a name="logout"></a>logout
15. Clique em **Ação > Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.  

## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Preparar uma máquina virtual baseada no Red Hat para KVM
### <a id="rhel67kvm"> </a>Preparar uma máquina virtual RHEL 6.7 do KVM
1. Baixe a imagem KVM do RHEL 6.7 do site do Red Hat.
2. Definir uma senha raiz.

   Gere uma senha criptografada e copie a saída do comando:

   # <a name="openssl-passwd--1-changeme"></a>openssl passwd -1 changeme
   Defina uma senha raiz com guestfish:

   # <a name="guestfish---rw--a-image-name"></a>guestfish --rw -a <image-name>
   > <fs> run <fs> list-filesystems <fs> mount /dev/sda1 / <fs> vi /etc/shadow <fs> exit
   >
   >

   Altere o segundo campo do usuário raiz de “!!” para a senha criptografada.
3. Crie uma máquina virtual no KVM a partir da imagem qcow2, defina o tipo de disco como **qcow2** e defina o modelo do dispositivo da interface da rede virtual como **virtio**. Em seguida, inicie a máquina virtual e faça logon como raiz.
4. Crie um arquivo chamado **network** in the `/etc/sysconfig/` que contém o seguinte texto:

     NETWORKING=yes   HOSTNAME=localhost.localdomain
5. Crie um arquivo chamado **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` que contém o seguinte texto:

     DEVICE=eth0   ONBOOT=yes   BOOTPROTO=dhcp   TYPE=Ethernet   USERCTL=no   PEERDNS=yes   IPV6INIT=no
6. Mova (ou remova) as regras de udev para evitar a geração de regras estáticas da interface Ethernet. Essas regras causam problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:

   # <a name="mkdir--m-0700-varlibwaagent"></a>mkdir -m 0700 /var/lib/waagent
   # <a name="mv-libudevrulesd75-persistent-net-generatorrules-varlibwaagent"></a>mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
   # <a name="mv-etcudevrulesd70-persistent-netrules-varlibwaagent"></a>mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/
7. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

   # <a name="chkconfig-network-on"></a>chkconfig network on
8. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

   # <a name="subscription-manager-register---auto-attach---usernamexxx---passwordxxx"></a>subscription-manager register --auto-attach --username=XXX --password=XXX
9. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra `/boot/grub/menu.lst` em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:

     console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

   Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Essa ação desabilitará o NUMA devido a um bug na versão do kernel usada pelo RHEL 6.

   Além da ação acima, recomendamos a remoção dos seguintes parâmetros:

     rhgb quiet crashkernel=auto

   As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.
   A opção crashkernel pode ser deixada configurada se desejado, mas é importante lembrar que esse parâmetro reduzirá a quantidade de memória disponível na VM em 128 MB ou mais. Isso pode ser problemático em tamanhos de VM menores.
10. Adicione os módulos do Hyper-V em initramfs:  

    Edite `/etc/dracut.conf` e adicione conteúdo: add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Recrie initramfs:

        # dracut –f -v
11. Desinstale cloud-init:

    # <a name="yum-remove-cloud-init"></a>yum remove cloud-init
12. Verifique se o servidor SSH está instalado e configurado para começar na hora da inicialização:

    # <a name="chkconfig-sshd-on"></a>chkconfig sshd on
    Modifique o /etc/ssh/sshd_config para incluir as seguintes linhas:

     PasswordAuthentication yes  ClientAliveInterval 180

    Reinicie o sshd:

    # <a name="service-sshd-restart"></a>service sshd restart
13. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

    # <a name="subscription-manager-repos---enablerhel-6-server-extras-rpms"></a>subscription-manager repos --enable=rhel-6-server-extras-rpms
14. Instale o Agente Linux do Azure executando o seguinte comando:

    # <a name="yum-install-walinuxagent"></a>yum install WALinuxAgent
    # <a name="chkconfig-waagent-on"></a>chkconfig waagent on
15. O Agente Linux do Azure pode configurar automaticamente o espaço de troca usando o disco de recurso local que é anexado à VM após o provisionamento da mesma no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em **/etc/waagent.conf** de maneira apropriada:

     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## OBSERVAÇÃO: defina isso para o que quer que você precise que seja.
16. Cancele o registro da assinatura (se necessário) executando o seguinte comando:

    # <a name="subscription-manager-unregister"></a>subscription-manager unregister
17. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    # <a name="waagent--force--deprovision"></a>waagent -force -deprovision
    # <a name="export-histsize0"></a>export HISTSIZE=0
    # <a name="logout"></a>logout
18. Finalize a VM no KVM.
19. Converta a imagem qcow2 para o formato VHD.
    Primeiro, converta a imagem no formato bruto:

    # <a name="qemu-img-convert--f-qcow2-o-raw-rhel-67qcow2-rhel-67raw"></a>qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    Certifique-se de que o tamanho da imagem bruta é alinhado com 1 MB. Caso contrário, arredonde o tamanho para alinhar com 1 MB:

    # <a name="mb10241024"></a>MB=$((1024*1024))
    # <a name="sizeqemu-img-info--f-raw---output-json-rhel-67raw-"></a>size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
               gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    # <a name="roundedsizesizemb-1mb"></a>rounded_size=$((($size/$MB + 1)*$MB))
    # <a name="qemu-img-resize-rhel-67raw-roundedsize"></a>qemu-img resize rhel-6.7.raw $rounded_size
    Converta o disco bruto em um VHD de tamanho fixo:

    # <a name="qemu-img-convert--f-raw--o-subformatfixed--o-vpc-rhel-67raw-rhel-67vhd"></a>qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd

### <a id="rhel7xkvm"> </a>Preparar uma máquina virtual RHEL 7.1/7.2 do KVM
1. Baixe a imagem KVM do RHEL 7.1 (ou 7.2) no site do Red Hat. Usaremos RHEL 7.1 como exemplo aqui.
2. Definir uma senha raiz.

   Gere uma senha criptografada e copie a saída do comando:

   # <a name="openssl-passwd--1-changeme"></a>openssl passwd -1 changeme
   Definir uma senha raiz com guestfish.

   # <a name="guestfish---rw--a-image-name"></a>guestfish --rw -a <image-name>
   > <fs> run <fs> list-filesystems <fs> mount /dev/sda1 / <fs> vi /etc/shadow <fs> exit
   >
   >

   Altere o segundo campo do usuário raiz de “!!” para a senha criptografada.
3. Crie uma máquina virtual no KVM a partir da imagem qcow2, defina o tipo de disco como **qcow2** e defina o modelo do dispositivo da interface da rede virtual como **virtio**. Em seguida, inicie a máquina virtual e faça logon como raiz.
4. Crie um arquivo chamado **network** in the `/etc/sysconfig/` que contém o seguinte texto:

     NETWORKING=yes   HOSTNAME=localhost.localdomain
5. Crie um arquivo chamado **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` que contém o seguinte texto:

     DEVICE=eth0   ONBOOT=yes   BOOTPROTO=dhcp   TYPE=Ethernet   USERCTL=no   PEERDNS=yes   IPV6INIT=no
6. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

   # <a name="chkconfig-network-on"></a>chkconfig network on
7. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

   # <a name="subscription-manager-register---auto-attach---usernamexxx---passwordxxx"></a>subscription-manager register --auto-attach --username=XXX --password=XXX
8. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra `/etc/default/grub` em um editor de texto e edite o parâmetro **GRUB_CMDLINE_LINUX**. Por exemplo:

     GRUB_CMDLINE_LINUX="rootdelay=300   console=ttyS0   earlyprintk=ttyS0"

   Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Além da ação acima, recomendamos a remoção dos seguintes parâmetros:

     rhgb quiet crashkernel=auto

   As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.
   A opção crashkernel pode ser deixada configurada se desejado, mas é importante lembrar que esse parâmetro reduzirá a quantidade de memória disponível na VM em 128 MB ou mais. Isso pode ser problemático em tamanhos de VM menores.
9. Depois de editar `/etc/default/grub`, execute o comando a seguir para recompilar a configuração do grub:

   # <a name="grub2-mkconfig--o-bootgrub2grubcfg"></a>grub2-mkconfig -o /boot/grub2/grub.cfg
10. Adicione os módulos do Hyper-V em initramfs:

    Edite `/etc/dracut.conf` e adicione o conteúdo:

     add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Recrie initramfs:

    # <a name="dracut-f--v"></a>dracut –f -v
11. Desinstale cloud-init:

    # <a name="yum-remove-cloud-init"></a>yum remove cloud-init
12. Verifique se o servidor SSH está instalado e configurado para começar na hora da inicialização:

    # <a name="systemctl-enable-sshd"></a>systemctl enable sshd
    Modifique o /etc/ssh/sshd_config para incluir as seguintes linhas:

     PasswordAuthentication yes  ClientAliveInterval 180

    Reinicie o sshd:

     systemctl restart sshd
13. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

    # <a name="subscription-manager-repos---enablerhel-7-server-extras-rpms"></a>subscription-manager repos --enable=rhel-7-server-extras-rpms
14. Instale o Agente Linux do Azure executando o seguinte comando:

    # <a name="yum-install-walinuxagent"></a>yum install WALinuxAgent
    Habilite o serviço de waagent:

    # <a name="systemctl-enable-waagentservice"></a>systemctl enable waagent.service
15. Não crie espaço de permuta no disco do SO. O Agente Linux do Azure pode configurar automaticamente o espaço de troca usando o disco de recurso local que é anexado à VM após o provisionamento da mesma no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique adequadamente os seguintes parâmetros em `/etc/waagent.conf` :

     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## OBSERVAÇÃO: defina isso para o que quer que você precise que seja.
16. Cancele o registro da assinatura (se necessário) executando o seguinte comando:

    # <a name="subscription-manager-unregister"></a>subscription-manager unregister
17. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    # <a name="sudo-waagent--force--deprovision"></a>sudo waagent -force -deprovision
    # <a name="export-histsize0"></a>export HISTSIZE=0
    # <a name="logout"></a>logout
18. Finalize a máquina virtual no KVM.
19. Converta a imagem qcow2 para o formato VHD.

    Primeiro, converta a imagem no formato bruto:

    # <a name="qemu-img-convert--f-qcow2-o-raw-rhel-71qcow2-rhel-71raw"></a>qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw
    Certifique-se de que o tamanho da imagem bruta é alinhado com 1 MB. Caso contrário, arredonde o tamanho para alinhar com 1 MB:

    # <a name="mb10241024"></a>MB=$((1024*1024))
    # <a name="sizeqemu-img-info--f-raw---output-json-rhel-71raw-"></a>size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
               gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    # <a name="roundedsizesizemb-1mb"></a>rounded_size=$((($size/$MB + 1)*$MB))
    # <a name="qemu-img-resize-rhel-71raw-roundedsize"></a>qemu-img resize rhel-7.1.raw $rounded_size
    Converta o disco bruto em um VHD de tamanho fixo:

    # <a name="qemu-img-convert--f-raw--o-subformatfixed--o-vpc-rhel-71raw-rhel-71vhd"></a>qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd

## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Preparar uma máquina virtual baseada no Red Hat do VMware
### <a name="prerequisites"></a>Pré-requisitos
Esta seção pressupõe que você já instalou uma máquina virtual RHEL no VMware. Para saber mais sobre como instalar um sistema operacional no VMware, consulte [Guia de instalação do sistema operacional convidado VMware](http://partnerweb.vmware.com/GOSIG/home.html).

* Ao instalar o sistema operacional Linux, é recomendável utilizar partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. Se você preferir, é possível usar LVM ou RAID em discos de dados.
* Não configure uma partição de permuta no disco do SO. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários. Verifique as etapas a seguir para obter mais informações a esse respeito.
* Ao criar o disco rígido virtual, escolha **Armazenar disco virtual como um único arquivo**.

### <a id="rhel67vmware"> </a>Preparar uma máquina virtual RHEL 6.7 do VMware
1. Desinstale o NetworkManager executando o seguinte comando:

   # <a name="sudo-rpm--e---nodeps-networkmanager"></a>sudo rpm -e --nodeps NetworkManager
   Observe que se o pacote ainda não estiver instalado, esse comando irá falhar com uma mensagem de erro. Isso é esperado.
2. Crie um arquivo chamado **network** no diretório /etc/sysconfig/ que contém o seguinte texto:

     NETWORKING=yes   HOSTNAME=localhost.localdomain
3. Crie um arquivo chamado **ifcfg-eth0** no diretório /etc/sysconfig/network-scripts/ que contém o seguinte texto:

     DEVICE=eth0   ONBOOT=yes   BOOTPROTO=dhcp   TYPE=Ethernet   USERCTL=no   PEERDNS=yes   IPV6INIT=no
4. Mova (ou remova) as regras de udev para evitar a geração de regras estáticas da interface Ethernet. Essas regras causam problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:

   # <a name="sudo-mkdir--m-0700-varlibwaagent"></a>sudo mkdir -m 0700 /var/lib/waagent
   # <a name="sudo-mv-libudevrulesd75-persistent-net-generatorrules-varlibwaagent"></a>sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
   # <a name="sudo-mv-etcudevrulesd70-persistent-netrules-varlibwaagent"></a>sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/
5. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

   # <a name="sudo-chkconfig-network-on"></a>sudo chkconfig network on
6. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

   # <a name="sudo-subscription-manager-register---auto-attach---usernamexxx---passwordxxx"></a>sudo subscription-manager register --auto-attach --username=XXX --password=XXX
7. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

   # <a name="subscription-manager-repos---enablerhel-6-server-extras-rpms"></a>subscription-manager repos --enable=rhel-6-server-extras-rpms
8. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra "/boot/grub/menu.lst" em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:

     console=ttyS0   earlyprintk=ttyS0   rootdelay=300   numa=off

   Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Essa ação desabilitará o NUMA devido a um bug na versão do kernel usada pelo RHEL 6.
   Além da ação acima, recomendamos a remoção dos seguintes parâmetros:

     rhgb quiet crashkernel=auto

   As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.
   A opção crashkernel pode ser deixada configurada se desejado, mas é importante lembrar que esse parâmetro reduzirá a quantidade de memória disponível na VM em 128 MB ou mais. Isso pode ser problemático em tamanhos de VM menores.
9. Adicione os módulos do Hyper-V em initramfs:

     Edite `/etc/dracut.conf` e adicione o conteúdo:

         add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

     Recrie initramfs:

         # dracut –f -v
10. Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização. Geralmente, esse é o padrão. Modifique o `/etc/ssh/sshd_config` para incluir a seguinte linha:

     ClientAliveInterval 180
11. Instale o Agente Linux do Azure executando o seguinte comando:

    # <a name="sudo-yum-install-walinuxagent"></a>sudo yum install WALinuxAgent
    # <a name="sudo-chkconfig-waagent-on"></a>sudo chkconfig waagent on
12. Não crie um espaço de permuta no disco do sistema operacional:

    O Agente Linux do Azure pode configurar automaticamente o espaço de troca usando o disco de recurso local que é anexado à VM após o provisionamento da mesma no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique adequadamente os seguintes parâmetros em `/etc/waagent.conf` :

     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## OBSERVAÇÃO: defina isso para o que quer que você precise que seja.
13. Cancele o registro da assinatura (se necessário) executando o seguinte comando:

    # <a name="sudo-subscription-manager-unregister"></a>sudo subscription-manager unregister
14. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    # <a name="sudo-waagent--force--deprovision"></a>sudo waagent -force -deprovision
    # <a name="export-histsize0"></a>export HISTSIZE=0
    # <a name="logout"></a>logout
15. Desligue a VM e converta o arquivo VMDK para um arquivo .vhd.

    Primeiro, converta a imagem no formato bruto:

    # <a name="qemu-img-convert--f-vmdk-o-raw-rhel-67vmdk-rhel-67raw"></a>qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw
    Certifique-se de que o tamanho da imagem bruta é alinhado com 1 MB. Caso contrário, arredonde o tamanho para alinhar com 1 MB:

    # <a name="mb10241024"></a>MB=$((1024*1024))
    # <a name="sizeqemu-img-info--f-raw---output-json-rhel-67raw-"></a>size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
             gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    # <a name="roundedsizesizemb-1mb"></a>rounded_size=$((($size/$MB + 1)*$MB))
    # <a name="qemu-img-resize-rhel-67raw-roundedsize"></a>qemu-img resize rhel-6.7.raw $rounded_size
    Converta o disco bruto em um VHD de tamanho fixo:

    # <a name="qemu-img-convert--f-raw--o-subformatfixed--o-vpc-rhel-67raw-rhel-67vhd"></a>qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd

### <a id="rhel7xvmware"> </a>Preparar uma máquina virtual RHEL 7.1/7.2 do VMware
1. Crie um arquivo chamado **network** no diretório /etc/sysconfig/ que contém o seguinte texto:

     NETWORKING=yes   HOSTNAME=localhost.localdomain
2. Crie um arquivo chamado **ifcfg-eth0** no diretório /etc/sysconfig/network-scripts/ que contém o seguinte texto:

     DEVICE=eth0   ONBOOT=yes   BOOTPROTO=dhcp   TYPE=Ethernet   USERCTL=no   PEERDNS=yes   IPV6INIT=no
3. Certifique-se de que o serviço de rede será iniciado durante a inicialização executando o seguinte comando:

   # <a name="sudo-chkconfig-network-on"></a>sudo chkconfig network on
4. Registre a sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório RHEL ao executar o seguinte comando:

   # <a name="sudo-subscription-manager-register---auto-attach---usernamexxx---passwordxxx"></a>sudo subscription-manager register --auto-attach --username=XXX --password=XXX
5. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra `/etc/default/grub` em um editor de texto e edite o parâmetro **GRUB_CMDLINE_LINUX**. Por exemplo:

     GRUB_CMDLINE_LINUX="rootdelay=300   console=ttyS0   earlyprintk=ttyS0"

   Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Além da ação acima, recomendamos a remoção dos seguintes parâmetros:

     rhgb quiet crashkernel=auto

   As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.
   A opção crashkernel pode ser deixada configurada se desejado, mas é importante lembrar que esse parâmetro reduzirá a quantidade de memória disponível na VM em 128 MB ou mais. Isso pode ser problemático em tamanhos de VM menores.
6. Depois de editar `/etc/default/grub`, execute o comando a seguir para recompilar a configuração do grub:

   # <a name="sudo-grub2-mkconfig--o-bootgrub2grubcfg"></a>sudo grub2-mkconfig -o /boot/grub2/grub.cfg
7. Adicione os módulos do Hyper-V em initramfs:

   Edite `/etc/dracut.conf`e adicione o conteúdo:

     add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

   Recrie initramfs:

   # <a name="dracut-f--v"></a>dracut –f -v
8. Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização. Geralmente, esse é o padrão. Modifique o `/etc/ssh/sshd_config` para incluir a seguinte linha:

     ClientAliveInterval 180
9. O pacote WALinuxAgent `WALinuxAgent-<version>` foi enviado para o repositório de extras do Red Hat. Habilite o repositório de extras para a VM executando o seguinte comando:

   # <a name="subscription-manager-repos---enablerhel-7-server-extras-rpms"></a>subscription-manager repos --enable=rhel-7-server-extras-rpms
10. Instale o Agente Linux do Azure executando o seguinte comando:

    # <a name="sudo-yum-install-walinuxagent"></a>sudo yum install WALinuxAgent
    # <a name="sudo-systemctl-enable-waagentservice"></a>sudo systemctl enable waagent.service
11. Não crie espaço de permuta no disco do SO. O Agente Linux do Azure pode configurar automaticamente o espaço de troca usando o disco de recurso local que é anexado à VM após o provisionamento da mesma no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique adequadamente os seguintes parâmetros em `/etc/waagent.conf` :

     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## OBSERVAÇÃO: defina isso para o que quer que você precise que seja.
12. Se você quiser cancelar o registro da assinatura, execute o seguinte comando:

    # <a name="sudo-subscription-manager-unregister"></a>sudo subscription-manager unregister
13. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    # <a name="sudo-waagent--force--deprovision"></a>sudo waagent -force -deprovision
    # <a name="export-histsize0"></a>export HISTSIZE=0
    # <a name="logout"></a>logout
14. Finalize a VM e converta o arquivo VMDK no formato VHD.

    Primeiro, converta a imagem no formato bruto:

    # <a name="qemu-img-convert--f-vmdk-o-raw-rhel-71vmdk-rhel-71raw"></a>qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw
    Certifique-se de que o tamanho da imagem bruta é alinhado com 1 MB. Caso contrário, arredonde o tamanho para alinhar com 1 MB:

    # <a name="mb10241024"></a>MB=$((1024*1024))
    # <a name="sizeqemu-img-info--f-raw---output-json-rhel-71raw-"></a>size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    # <a name="roundedsizesizemb-1mb"></a>rounded_size=$((($size/$MB + 1)*$MB))
    # <a name="qemu-img-resize-rhel-71raw-roundedsize"></a>qemu-img resize rhel-7.1.raw $rounded_size
    Converta o disco bruto em um VHD de tamanho fixo:

    # <a name="qemu-img-convert--f-raw--o-subformatfixed--o-vpc-rhel-71raw-rhel-71vhd"></a>qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd

## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Preparar uma máquina virtual baseada em Red Hat de um arquivo ISO, usando um arquivo de início rápido automaticamente
### <a id="rhel7xkickstart"> </a>Preparar uma máquina virtual RHEL 7.1/7.2 de um arquivo de início rápido

1.  Crie o arquivo de início rápido com o conteúdo abaixo e salve o arquivo. Para saber mais sobre a instalação de início rápido, consulte o [Guia de instalação de início rápido](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

          # Kickstart for provisioning a RHEL 7 Azure VM

          # System authorization information
          auth --enableshadow --passalgo=sha512

          # Use graphical install
          text

          # Do not run the Setup Agent on first boot
          firstboot --disable

          # Keyboard layouts
          keyboard --vckeymap=us --xlayouts='us'

          # System language
          lang en_US.UTF-8

          # Network information
          network  --bootproto=dhcp

          # Root password
          rootpw --plaintext "to_be_disabled"

          # System services
          services --enabled="sshd,waagent,NetworkManager"

          # System timezone
          timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

          # Partition clearing information
          clearpart --all --initlabel

          # Clear the MBR
          zerombr

          # Disk partitioning information
          part /boot --fstype="xfs" --size=500
          part / --fstyp="xfs" --size=1 --grow --asprimary

          # System bootloader configuration
          bootloader --location=mbr

          # Firewall configuration
          firewall --disabled

          # Enable SELinux
          selinux --enforcing

          # Don't configure X
          skipx

          # Power down the machine after install
          poweroff

          %packages
          @base
          @console-internet
          chrony
          sudo
          parted
          -dracut-config-rescue

          %end

          %post --log=/var/log/anaconda/post-install.log

          #!/bin/bash

          # Register Red Hat Subscription
          subscription-manager register --username=XXX --password=XXX --auto-attach --force

          # Install latest repo update
          yum update -y

          # Enable extras repo
          subscription-manager repos --enable=rhel-7-server-extras-rpms

          # Install WALinuxAgent
          yum install -y WALinuxAgent

          # Unregister Red Hat subscription
          subscription-manager unregister

          # Enable waaagent at boot-up
          systemctl enable waagent

          # Disable the root account
          usermod root -p '!!'

          # Configure swap in WALinuxAgent
          sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
          sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

          # Set the cmdline
          sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

          # Enable SSH keepalive
          sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

          # Build the grub cfg
          grub2-mkconfig -o /boot/grub2/grub.cfg

          # Configure network
          cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
          DEVICE=eth0
          ONBOOT=yes
          BOOTPROTO=dhcp
          TYPE=Ethernet
          USERCTL=no
          PEERDNS=yes
          IPV6INIT=no
          NM_CONTROLLED=yes
          EOF

          # Deprovision and prepare for Azure
          waagent -force -deprovision

          %end
2. Coloque o arquivo de início rápido em um local acessível no sistema de instalação.
3. No Gerenciador do Hyper-V, crie uma nova VM. Na página **Conectar Disco Rígido Virtual**, selecione **Anexar um disco rígido virtual posteriormente** e conclua o Assistente de Nova Máquina Virtual.
4. Abra as configurações da VM: a.  Coloque um novo disco rígido virtual na VM. Selecione **Formato VHD** e **Tamanho Fixo**.
    b.  Anexe o ISO de instalação à unidade de DVD.
    c.  Configure o BIOS para inicializar do CD.
5. Inicie a VM. Quando o guia de instalação for exibida, pressione **Tab** para configurar as opções de inicialização.
6. Insira `inst.ks=<the location of the kickstart file>` no final das opções de inicialização e pressione **Enter**.
7. Aguarde a conclusão da instalação. Quando ela for concluída, a VM será desligada automaticamente. Agora, seu VHD Linux está pronto para ser carregado no Azure.

## <a name="known-issues"></a>Problemas conhecidos
Há problemas conhecidos quando você está usando o RHEL 7.1 no Hyper-V e no Microsoft Azure.

### <a name="disk-io-freeze"></a>Congelamento da E/S do disco
Esse problema pode ocorrer durante as atividades frequentes de E/S do disco de armazenamento com o RHEL 7.1 no Hyper-V e no Azure.   

Taxa de reprodução:

O problema é intermitente. No entanto, ocorre com mais frequência durante as operações de E/S do disco no Hyper-V e no Microsoft Azure.   

[AZURE.NOTE] Esse problema conhecido já foi abordado pelo Red Hat. Para instalar as correções associadas, execute o seguinte comando:

    # sudo yum update

### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>O driver do Hyper-V não foi incluído no disco de RAM inicial ao usar um hipervisor Hyper-V
Em alguns casos, os instaladores do Linux podem não incluem os drivers para Hyper-V no disco RAM inicial (initrd ou initramfs), a menos que ele detecte que está executando um ambiente Hyper-V.

Ao usar um sistema de virtualização diferente (ou seja, Virtualbox, Xen, etc.) para preparar a imagem do Linux, talvez seja necessário recompilar o initrd para garantir que pelo menos os módulos kernel hv_vmbus e hv_storvsc estejam disponíveis no disco RAM inicial. Isso é um problema conhecido pelo menos em sistemas com base na distribuição Red Hat upstream.

Para resolver esse problema, você precisa adicionar módulos do Hyper-V em initramfs e recriá-los:

Edite `/etc/dracut.conf` e adicione o conteúdo:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

Recrie initramfs:

        # dracut –f -v

Para obter mais detalhes, consulte as informações sobre [recriação de initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Próximas etapas
Agora, você está pronto para usar o disco rígido virtual Red Hat Enterprise Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que você estiver carregando o arquivo .vhd para o Azure, veja as etapas 2 e 3 em [Criando e carregando um disco rígido virtual que contém o sistema operacional Linux](virtual-machines-linux-classic-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

Para obter mais detalhes sobre os hipervisores certificados para execução do Red Hat Enterprise Linux, visite [o site da Red Hat](https://access.redhat.com/certified-hypervisors).



<!--HONumber=Nov16_HO3-->


