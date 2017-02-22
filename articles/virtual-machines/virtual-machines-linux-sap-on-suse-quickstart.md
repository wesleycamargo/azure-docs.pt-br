---
title: Testando o SAP NetWeaver em VMs SUSE Linux no Microsoft Azure| Microsoft Docs
description: Testando o SAP NetWeaver em VMs SUSE Linux no Microsoft Azure
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 645e358b-3ca1-4d3d-bf70-b0f287498d7a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: 046f58eba906980f23ce1177794b28930e3314a1
ms.openlocfilehash: e7255c4123849f8f3fb29767308b433b5c0e511b


---
# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Executando o SAP NetWeaver em VMs do SUSE Linux no Microsoft Azure
Este artigo descreve fatores que devem ser levados em consideração quando você estiver executando o SAP NetWeaver em VMs (máquinas virtuais) do SUSE Linux no Microsoft Azure. A partir de 19 de maio de 2016, o SAP NetWeaver tem suporte oficial em VMs do SUSE Linux no Azure. Todos os detalhes relativos a versões do Linux, versões de kernel do SAP e assim por diante podem ser encontrados na nota do SAP 1928533 "Aplicativos SAP no Azure: produtos com suporte e tipos de VM do Azure".
Mais documentação sobre o SAP em VMs Linux podem ser encontrados aqui: [Usando o SAP em VMs (máquinas virtuais) do Linux](virtual-machines-linux-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

As informações a seguir devem ajudar você a evitar algumas possíveis armadilhas.

## <a name="suse-images-on-azure-for-running-sap"></a>Imagens do SUSE no Azure para executar o SAP
Para executar o SAP NetWeaver no Azure, use apenas o SUSE Linux Enterprise Server SLES 12 ( SPx ) - consulte também nota do SAP 1928533. Há uma imagem SUSE especial no Azure Marketplace: ("CAL do SLES 11 SP3 para SAP"), mas ela não se destina ao uso geral. Não use esta imagem porque ela é reservada para a solução [SAP Cloud Appliance Library](https://cal.sap.com/) .  

Use o Azure Resource Manager para todos os novos testes e instalações no Azure. Para procurar imagens e versões SLES do SUSE usando o Azure PowerShell ou a CLI (interface de linha de comando) do Azure, use os comandos a seguir. Você pode usar a saída, por exemplo, para definir a imagem do sistema operacional em um modelo JSON para a implantação de uma nova VM do SUSE Linux.
Estes comandos do PowerShell são válidos para a versão do Azure PowerShell 1.0.1 e posteriores.

Enquanto ainda é possível usar as imagens SLES padrão para instalações de SAP, recomendamos o uso do novo SLES para imagens SAP que já estão disponíveis na Galeria de imagens do Azure. Encontre mais informações sobre essas imagens na [página correspondente do Azure Marketplace]( https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SUSE.SLES-SAP ) ou na [página da Web de perguntas frequentes do SUSE sobre SLES para SAP]( https://www.suse.com/products/sles-for-sap/frequently-asked-questions/ ).


* Procure por editores existentes, incluindo o SUSE:
  
   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```
* Procure por ofertas existentes do SUSE:
  
   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
* Procure por ofertas do SLES SUSE:
  
   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP"
   CLI : azure vm image list-skus westeurope SUSE SLES
   CLI : azure vm image list-skus westeurope SUSE SLES-SAP
   ```
* Procure por uma versão específica do SKU do SLES:
  
   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12-SP2"
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP" -skus "12-SP2"
   CLI : azure vm image list westeurope SUSE SLES 12-SP2
   CLI : azure vm image list westeurope SUSE SLES-SAP 12-SP2
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>Instalando o WALinuxAgent em uma VM SUSE
O agente chamado WALinuxAgent faz parte das imagens do SLES no Azure Marketplace. Para saber mais sobre como instalá-lo manualmente (por exemplo, ao carregar um VHD (disco rígido virtual) do sistema operacional SLES no local), confira:

* [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)
* [As tabelas](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>"Monitoramento avançado" do SAP
O "monitoramento avançado" do SAP é um pré-requisito obrigatório para executar o SAP no Azure. Verifique os detalhes na nota do SAP 2191498 "SAP no Linux com o Azure: monitoramento aprimorado".

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Anexar discos de dados do Azure em uma VM Linux do Azure
Você nunca deve montar discos de dados do Azure em uma VM Linux do Azure usando a ID do dispositivo. Em vez disso, use o identificador universal exclusivo (UUID). Tenha cuidado ao usar, por exemplo, ferramentas gráficas para montar discos de dados do Azure. Verifique as entradas em /etc/fstab.

O problema com a ID do dispositivo é que ela pode mudar, e a VM do Azure pode travar no processo de inicialização. Você pode adicionar o parâmetro nofail em /etc/fstab para atenuar o problema. Mas tenha cuidado com o nofail, pois os aplicativos podem usar o ponto de montagem como antes e talvez gravar no sistema de arquivos raiz caso um disco de dados do Azure externo não tenha sido montado durante a inicialização.

A única exceção na montagem via UUID está relacionada a anexar um disco do sistema operacional para fins de solução de problemas, conforme descrito na seção a seguir.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>Solução de problemas de uma VM SUSE que não está mais acessível
Pode haver situações em que uma VM SUSE no Azure trava no processo de inicialização (por exemplo, com um erro relacionado à montagem de discos). Você pode verificar esse problema usando o recurso de diagnóstico de inicialização para Máquinas Virtuais v2 do Azure no portal do Azure. Para saber mais, consulte [Diagnóstico de inicialização](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Uma maneira de resolver o problema é anexar o disco do sistema operacional da VM danificada a outra VM SUSE no Azure. Faça as alterações apropriadas, como editar o /etc/fstab ou remover as regras de udev de rede, conforme descrito na próxima seção.

No entanto, há uma coisa importante a considerar. Implantar várias VMs SUSE da mesma imagem do Azure Marketplace (por exemplo, SLES 11 SP4) mostra que o disco do sistema operacional sempre será montado pelo mesmo UUID. Portanto, anexar um disco do sistema operacional de uma VM diferente por UUID que foi implantado usando a mesma imagem do Azure Marketplace resultará, portanto, em dois UUIDs idênticos. Isso causa problemas e poderia significar que, na verdade, a VM está destinada à solução de problemas do sistema operacional anexado e danificado no disco do SO em vez do original.

Há duas maneiras de evitar isso:

* Usar uma imagem diferente do Azure Marketplace para a VM para solução de problemas (por exemplo, SLES 11 SPx em vez do SLES 12).
* Não anexar o disco do sistema operacional danificado de outra VM por meio de UUID, mas usar algo.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Carregando uma VM SUSE do local no Azure
Para obter uma descrição das etapas para carregar uma VM SUSE do local para o Azure, veja [Preparar uma máquina virtual do SLES ou openSUSE para o Azure](virtual-machines-linux-suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Se você quiser carregar uma VM sem a etapa de desprovisionamento no final para manter, por exemplo, uma instalação existente do SAP, bem como o nome do host, verifique os itens a seguir:

* Verifique se o disco do sistema operacional foi montado com o UUID e não com a ID do dispositivo. Alterar para o UUID somente em /etc/fstab não é suficiente para o disco do sistema operacional. Além disso, não se esqueça de adaptar o carregador de inicialização por meio do YaST ou editando /boot/grub/menu.lst.
* Se você usa o formato VHDX para o disco do sistema operacional do SUSE e convertido em VHD para carregar no Azure, é muito provável que o dispositivo de rede tenha mudado de eth0 para eth1. Para evitar problemas quando você estiver inicializando o Azure, mude de volta para eth0, como descrito em [Como corrigir eth0 no VMware SLES 11 clonado](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Além do que está descrito neste artigo, também recomendamos a remoção do seguinte:

   /lib/udev/rules.d/75-persistent-net-generator.rules

Você também pode instalar o Agente Linux do Azure (waagent) para ajudar a evitar possíveis problemas, desde que não haja várias NICs.

## <a name="deploying-a-suse-vm-on-azure"></a>Implantando uma VM SUSE no Azure
Você deve criar novas VMs SUSE usando arquivos de modelo JSON no novo modelo do Azure Resource Manager. Após a criação do arquivo de modelo JSON, será possível implantar a VM usando o seguinte comando da CLI como uma alternativa ao PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Para obter mais detalhes sobre os arquivos de modelo JSON, veja [Criação de modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) e [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/).

Para obter mais detalhes sobre o CLI e o Azure Resource Manager, veja [Usar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager](../xplat-cli-azure-resource-manager.md).

## <a name="sap-license-and-hardware-key"></a>Chave de licença e hardware do SAP
Para a certificação oficial SAP-Azure, um novo mecanismo foi introduzido para calcular a chave de hardware do SAP usada para a licença do SAP. O kernel do SAP precisou ser adaptado para fazer uso dele. As versões anteriores de kernel do SAP para Linux não incluíam essa alteração de código. Portanto, pode acontecer que, em certas situações (por exemplo, no redimensionamento da VM do Azure), a chave de hardware do SAP tenha sido alterada e a licença do SAP não fosse mais válida. Isso é resolvido nos kernels do SAP para Linux mais recentes. Para obter detalhes, consulte nota do SAP 1928533.

## <a name="suse-sapconf-package--tuned-adm"></a>SUSE sapconf package / tuned-adm
O SUSE fornece um pacote chamado "sapconf", que gerencia um conjunto de configurações específicas do SAP. Para obter mais detalhes sobre o que este pacote faz e como instalá-lo e usá-lo, veja [Como usar sapconf para preparar o SUSE Linux Enterprise Server para executar sistemas SAP](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) e [Como preparar um SUSE Linux Enterprise Server para executar sistemas SAP ou o que é sapconf?](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

Enquanto isso, há uma nova ferramenta que substitui sapconf - tuned-adm. Há mais detalhes sobre essa ferramenta nos dois links a seguir.

A documentação do SLES sobre tuned-adm profile sap-hana pode ser encontrada [aqui](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

Ajustes de sistemas para cargas de trabalho do SAP com tuned-adm - podem ser encontrados [aqui](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) no capítulo 6.2

## <a name="nfs-share-in-distributed-sap-installations"></a>Compartilhamento de NFS em instalações SAP distribuídas
No caso de uma instalação distribuída na qual você deseja instalar, por exemplo, o banco de dados e os servidores de aplicativos SAP em VMs separadas, é possível compartilhar o diretório /sapmnt por meio dos Serviços de NFS (Network File System). Caso haja problemas com as etapas de instalação após a criação do compartilhamento de NFS para /sapmnt, verifique se "no_root_squash" está definido para o compartilhamento.

## <a name="logical-volumes"></a>Volumes lógicos
No passado, se alguém precisasse de um grande volume lógico em vários discos de dados do Azure (por exemplo, para o banco de dados SAP), seria recomendável usar o mdadm, pois o lvm ainda não estava totalmente validado no Azure. Para saber como configurar o RAID do Linux no Azure usando o mdadm, veja [Configurar o software RAID no Linux](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Enquanto isso, a partir do início de maio de 2016, o lvm também tem suporte total no Azure e pode ser usado como uma alternativa para o mdadm. Para obter informações adicionais sobre o lvm no Azure, veja [Configurar o LVM em uma VM do Linux no Azure](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-suse-repository"></a>Repositório SUSE do Azure
Se houver um problema com o acesso ao repositório SUSE padrão do Azure, você pode usar um comando simples para redefini-lo. Isso pode acontecer quando você cria uma imagem de sistema operacional particular em uma região do Azure e, depois, copia a imagem para uma região diferente em que você quer implantar novas VMs com base nesta imagem de sistema operacional particular. Basta executar o seguinte comando na VM:

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Área de trabalho Gnome
Se você deseja usar a área de trabalho Gnome para instalar um sistema de demonstração completo SAP em uma única VM, incluindo um SAP GUI, navegador, console de gerenciamento SAP e assim por diante, aqui vai uma dica para instalá-lo nas imagens SLES do Azure:

   Para o SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   Para o SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>Suporte de SAP para Oracle no Linux na nuvem
Há uma restrição de suporte da Oracle para Linux em ambientes virtualizados. Embora este não seja um tópico específico do Azure, é importante entender. O SAP não oferece suporte ao Oracle no SUSE ou ao Red Hat em uma nuvem pública como o Azure. Para discutir sobre este tópico, contate diretamente o Oracle.




<!--HONumber=Feb17_HO2-->


