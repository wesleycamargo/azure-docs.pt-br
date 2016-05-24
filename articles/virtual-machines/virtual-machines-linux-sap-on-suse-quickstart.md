<properties
   pageTitle="Testando o SAP NetWeaver em VMs SUSE Linux no Microsoft Azure| Microsoft Azure"
   description="Testando o SAP NetWeaver em VMs SUSE Linux no Microsoft Azure"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="hermanndms"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="na"
   ms.date="05/04/2016"
   ms.author="hermannd"/>

# Teste do SAP NetWeaver em VMs SUSE Linux no Microsoft Azure


Este artigo descreve o que deve ser considerado quando você estiver testando o SAP NetWeaver em máquinas virtuais (VMs) SUSE Linux no Microsoft Azure. Atualmente, não há nenhuma instrução de suporte oficial da SAP para essas máquinas virtuais. No entanto, você pode fazer alguns testes, demonstrações ou protótipos, desde que eles não dependam de suporte oficial da SAP.

As informações a seguir devem ajudar você a evitar algumas possíveis armadilhas.

## Imagens do SUSE no Azure para testar o SAP

Para testar o SAP no Azure, use somente o SLES (SUSE Linux Enterprise Server) 11 SP4 e o SLES 12. Há uma imagem SUSE especial no Azure Marketplace: ("CAL do SLES 11 SP3 para SAP"), mas ela não se destina ao uso geral. Não use esta imagem porque ela é reservada para a solução [SAP Cloud Appliance Library](https://cal.sap.com/).

Use o Gerenciador de Recursos do Azure para todos os novos testes no Azure . Para procurar imagens e versões SLES do SUSE usando o Azure PowerShell ou a CLI (interface de linha de comando) do Azure, use os comandos a seguir. Você pode usar a saída, por exemplo, para definir a imagem do sistema operacional em um modelo JSON para a implantação de uma nova VM do SUSE Linux. Estes comandos do PowerShell são válidos para a versão do Azure PowerShell 1.0.1 e posteriores.

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
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* Procure por uma versão específica do SKU do SLES:

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## Instalando o WALinuxAgent em uma VM SUSE

O agente chamado WALinuxAgent faz parte das imagens do SLES no Azure Marketplace. Para saber mais sobre como instalá-lo manualmente (por exemplo, ao carregar um VHD (disco rígido virtual) do sistema operacional SLES no local), confira:

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [As tabelas](virtual-machines-linux-endorsed-distros.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## Anexar discos de dados do Azure em uma VM Linux do Azure

Você nunca deve montar discos de dados do Azure em uma VM Linux do Azure usando a ID do dispositivo. Em vez disso, use o identificador universal exclusivo (UUID). Tenha cuidado ao usar, por exemplo, ferramentas gráficas para montar discos de dados do Azure. Verifique as entradas em /etc/fstab.

O problema com a ID do dispositivo é que ela pode mudar, e a VM do Azure pode travar no processo de inicialização. Você pode adicionar o parâmetro nofail em /etc/fstab para atenuar o problema. Mas tenha cuidado com o nofail, pois os aplicativos podem usar o ponto de montagem como antes e talvez gravar no sistema de arquivos raiz caso um disco de dados do Azure externo não tenha sido montado durante a inicialização.

A única exceção na montagem via UUID está relacionada a anexar um disco do sistema operacional para fins de solução de problemas, conforme descrito na seção a seguir.

## Solução de problemas de uma VM SUSE que não está mais acessível

Pode haver situações em que uma VM SUSE no Azure trava no processo de inicialização (por exemplo, com um erro relacionado à montagem de discos). Você pode verificar esse problema usando o recurso de diagnóstico de inicialização para Máquinas Virtuais v2 do Azure no portal do Azure. Para saber mais, confira [Diagnóstico de inicialização](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Uma maneira de resolver o problema é anexar o disco do sistema operacional da VM danificada a outra VM SUSE no Azure. Faça as alterações apropriadas, como editar o /etc/fstab ou remover as regras de udev de rede, conforme descrito na próxima seção.

No entanto, há uma coisa importante a considerar. Implantar várias VMs SUSE da mesma imagem do Azure Marketplace (por exemplo, SLES 11 SP4) mostra que o disco do sistema operacional sempre será montado pelo mesmo UUID. Portanto, anexar um disco do sistema operacional de uma VM diferente por UUID que foi implantado usando a mesma imagem do Azure Marketplace resultará, portanto, em dois UUIDs idênticos. Isso causa problemas e poderia significar que, na verdade, a VM está destinada à solução de problemas do sistema operacional anexado e danificado no disco do SO em vez do original.

Há duas maneiras de evitar isso:

* Usar uma imagem diferente do Azure Marketplace para a VM para solução de problemas (por exemplo, SLES 12 em vez do SLES 11 SP4).
* Não anexar o disco do sistema operacional danificado de outra VM por meio de UUID, mas usar algo.

## Carregando uma VM SUSE do local no Azure

Para obter uma descrição das etapas para carregar uma VM SUSE do local do Azure, confira [Preparar uma máquina virtual do SLES ou openSUSE para o Azure](virtual-machines-linux-create-upload-vhd-suse.md).

Se você quiser carregar uma VM sem a etapa de desprovisionamento no final para manter, por exemplo, uma instalação existente do SAP, bem como o nome do host, verifique os itens a seguir:

* Verifique se o disco do sistema operacional foi montado com o UUID e não com a ID do dispositivo. Alterar para o UUID somente em /etc/fstab não é suficiente para o disco do sistema operacional. Além disso, não se esqueça de adaptar o carregador de inicialização por meio do YaST ou editando /boot/grub/menu.lst.
* Se você usa o formato VHDX para o disco do sistema operacional do SUSE e convertido em VHD para carregar no Azure, é muito provável que o dispositivo de rede tenha mudado de eth0 para eth1. Para evitar problemas quando você estiver inicializando o Azure, mude de volta para eth0, como descrito em [Como corrigir eth0 no VMware SLES 11 clonado](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Além do que está descrito neste artigo, também recomendamos a remoção do seguinte:

   /lib/udev/rules.d/75-persistent-net-generator.rules

Você também pode instalar o Agente Linux do Azure (waagent) para ajudar a evitar possíveis problemas, desde que não haja várias NICs.

## Implantando uma VM SUSE no Azure

Você deve criar novas VMs SUSE usando arquivos de modelo JSON no novo modelo do Azure Resource Manager. Após a criação do arquivo de modelo JSON, será possível implantar a VM usando o seguinte comando da CLI como uma alternativa ao PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Para obter mais detalhes sobre os arquivos de modelo JSON, confira [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md) e [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/).

Para obter mais detalhes sobre o CLI e o Azure Resource Manager, confira [Usar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager](xplat-cli-azure-resource-manager.md).

## Chave de licença e hardware do SAP

Para a certificação oficial SAP-Azure, um novo mecanismo foi introduzido para calcular a chave de hardware do SAP usada para a licença do SAP. O kernel do SAP precisou ser adaptado para fazer uso dele. As versões atuais de kernel do SAP para Linux não incluem essa alteração de código. Portanto, pode acontecer que, em certas situações (por exemplo, no redimensionamento da VM do Azure), as alterações de chave de hardware do SAP e a licença do SAP não sejam mais válidas.

## Pacote sapconf do SUSE

O SUSE fornece um pacote chamado "sapconf", que gerencia um conjunto de configurações específicas do SAP. Para obter mais detalhes sobre o que este pacote faz e como instalá-lo e usá-lo, confira [Como usar sapconf para preparar o SUSE Linux Enterprise Server para executar sistemas SAP](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) e [Como preparar um SUSE Linux Enterprise Server para executar sistemas SAP ou o que é sapconf?](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

## Compartilhamento de NFS em instalações SAP distribuídas

No caso de uma instalação distribuída na qual você deseja instalar, por exemplo, o banco de dados e os servidores de aplicativos SAP em VMs separadas, é possível compartilhar o diretório /sapmnt por meio dos Serviços de NFS (Network File System). Caso haja problemas com as etapas de instalação após a criação do compartilhamento de NFS para /sapmnt, verifique se "no\_root\_squash" está definido para o compartilhamento.

## Volumes lógicos

O LVM (Gerenciador de Volume lógico) não é completamente validado no Azure. Se você precisar de um grande volume lógico em vários discos de dados do Azure (por exemplo, para o banco de dados SAP), deverá usar mdadm. Para saber como configurar o RAID do Linux no Azure usando mdadm, confira [Configurar o software RAID no Linux](virtual-machines-linux-configure-raid.md).


## Repositório SUSE do Azure

Se houver um problema com o acesso ao repositório SUSE padrão do Azure, você pode usar um comando simples para redefini-lo. Isso pode acontecer quando você cria uma imagem de sistema operacional particular em uma região do Azure e, depois, copia a imagem para uma região diferente em que você quer implantar novas VMs com base nesta imagem de sistema operacional particular. Basta executar o seguinte comando na VM:

   ```
   service guestregister restart
   ```

## Área de trabalho Gnome

Se você deseja usar a área de trabalho Gnome para instalar um sistema de demonstração completo SAP em uma única VM, incluindo um SAP GUI, navegador, console de gerenciamento SAP e assim por diante, aqui vai uma dica para instalá-lo nas imagens SLES do Azure:

   Para o SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   Para o SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## Suporte de SAP para Oracle no Linux na nuvem

Há uma restrição de suporte da Oracle para Linux em ambientes virtualizados. Embora este não seja um tópico específico do Azure, é importante entender. O SAP não oferece suporte ao Oracle no SUSE ou ao Red Hat em uma nuvem pública como o Azure. Para discutir sobre este tópico, contate diretamente o Oracle.

<!---HONumber=AcomDC_0511_2016-->