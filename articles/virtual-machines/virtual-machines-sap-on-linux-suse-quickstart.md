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
   ms.date="11/26/2015"
   ms.author="hermannd"/>

# Testando o SAP NetWeaver em VMs SUSE Linux no Microsoft Azure


Esta é uma lista de itens a serem considerados quando você testar o SAP NetWeaver em VMs SUSE Linux do Microsoft Azure. No momento, não há qualquer declaração oficial de suporte do SAP para SAP-Linux-Azure. Ainda assim, os clientes podem fazer alguns testes, demonstrações ou protótipos, desde que não dependam de suporte oficial da SAP.

As informações a seguir devem ajudar você a evitar algumas possíveis armadilhas.



## Imagens do SUSE no Microsoft Azure para testar o SAP

Para testar o SAP no Azure, use somente o SLES (SUSE Linux Enterprise Server) 11 SP4 e o SLES 12. Há uma imagem SUSE especial no Azure Marketplace: ("CAL do SLES 11 SP3 para SAP"), mas ela não se destina ao uso geral. Ela é reservada para a solução [SAP Cloud Appliance Library](https://cal.sap.com/). Não havia uma opção para ocultar essa imagem do público. Portanto, basta não usá-la.

Use o Gerenciador de Recursos do Azure para todos os novos testes no Azure . Para procurar imagens e versões SLES do SUSE usando o Azure PowerShell ou a CLI (interface de linha de comando) do Azure, use os comandos a seguir. Você pode usar a saída, por exemplo, para definir a imagem do sistema operacional em um modelo JSON para a implantação de uma nova VM do SUSE Linux. Os comandos do PowerShell abaixo são válidos para o Azure PowerShell 1.0.1 ou mais recente.

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

O agente chamado WALinuxAgent faz parte das imagens do SLES no Azure Marketplace. Estes são os locais em que é possível encontrar informações sobre como instalá-lo manualmente (por exemplo, ao carregar um VHD do sistema operacional SLES no local):

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [As tabelas](virtual-machines-linux-endorsed-distributions.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## Anexar discos de dados do Azure em uma VM Linux do Azure

Nunca monte discos de dados do Azure em uma VM Linux do Azure por meio da ID do dispositivo. Em vez disso, use o UUID. Tenha cuidado ao usar, por exemplo, ferramentas gráficas para montar discos de dados do Azure. Verifique as entradas em /etc/fstab.

O problema com a ID do dispositivo é que ela pode mudar, e a VM do Azure pode travar no processo de inicialização. Você pode adicionar o parâmetro nofail em /etc/fstab para atenuar o problema. Mas observe que, com o nofail, os aplicativos podem usar o ponto de montagem como antes e talvez gravar no sistema de arquivos raiz caso um disco de dados do Azure externo não tenha sido montado durante a inicialização.

## Carregando uma VM SUSE do local no Azure

[Este artigo](virtual-machines-linux-create-upload-vhd-suse.md) descreve as etapas para carregar uma VM SUSE do local para o Azure.

Se você quiser carregar uma VM sem a etapa de desprovisionamento no final para manter, por exemplo, uma instalação existente do SAP, bem como o nome do host, verifique os itens a seguir:

* Verifique se o disco do sistema operacional foi montado via UUID e não por meio da ID do dispositivo. Alterar para o UUID somente em /etc/fstab não é suficiente para o disco do sistema operacional. Além disso, não se esqueça de adaptar o carregador de inicialização por meio do YaST ou editando /boot/grub/menu.lst
* Caso você tenha usado o formato VHDX para o disco do sistema operacional do SUSE e convertido em VHD para carregar no Azure, é muito provável que o dispositivo de rede tenha mudado de eth0 para eth1. Para evitar problemas quando você estiver inicializando o Azure, mude de volta para eth0, como descrito [neste artigo](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/):

Além do que está descrito neste artigo, também recomendamos a remoção do seguinte:

   /lib/udev/rules.d/75-persistent-net-generator.rules

A instalação do Agente Linux do Azure (waagent) também deve ajudar você a evitar possíveis problemas, desde que não haja várias NICs.

## Implantando uma VM SUSE no Azure

As novas VMs SUSE devem ser criadas por meio de arquivos de modelo JSON no novo modelo do Gerenciador de Recursos do Azure. Após a criação do arquivo de modelo JSON, é possível implantar a VM usando o seguinte comando da CLI como uma alternativa ao PowerShell:

   ``` azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ``` Encontre mais detalhes sobre os arquivos de modelo JSON [neste artigo](resource-group-authoring-templates.md) e [nesta página da Web](https://azure.microsoft.com/documentation/templates/).

Encontre mais detalhes sobre a CLI e o Gerenciador de Recursos do Azure [neste artigo](xplat-cli-azure-resource-manager.md).

## Chave de licença e hardware do SAP

Para a certificação oficial SAP-Windows-Azure, um novo mecanismo foi introduzido para calcular a chave de hardware do SAP usada para a licença do SAP. O kernel do SAP precisou ser adaptado para fazer uso dele. As versões atuais de kernel do SAP para Linux não incluem essa alteração de código. Portanto, pode acontecer que, em certas situações (por exemplo, no redimensionamento da VM do Azure), as alterações de chave de hardware do SAP e a licença do SAP não sejam mais válidas

## Pacote sapconf do SUSE

O SUSE fornece um pacote chamado "sapconf", que cuida de um conjunto de configurações específicas do SAP. Veja mais detalhes sobre este pacote, por exemplo, o que ele faz e como instalá-lo e usá-lo, [nesta entrada de blog do SUSE](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) e [nesta entrada de blog do SAP](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

## Compartilhamento de NFS em instalações SAP distribuídas

No caso de uma instalação distribuída na qual você deseja instalar, por exemplo, o banco de dados e os servidores de aplicativos SAP em VMs separadas, é possível compartilhar o diretório /sapmnt por meio do NFS (sistema de arquivos de rede). Caso haja problemas com as etapas de instalação após a criação do compartilhamento de NFS para /sapmnt, verifique se "no\_root\_squash" está definido para o compartilhamento. Essa foi a solução em um caso de teste interno.


## Volumes lógicos

O LVM (Gerenciador de Volume lógico) não é completamente validado no Azure. Se você precisar de um grande volume lógico em vários discos de dados do Azure (por exemplo, para o banco de dados SAP), use mdadm. [Este artigo](virtual-machines-linux-configure-raid.md) descreve como configurar o RAID Linux no Azure usando mdadm.


## Repositório SUSE do Azure

Se houver um problema com o acesso ao repositório SUSE padrão do Azure, há um comando simples para redefini-lo. Isso pode acontecer quando você cria uma imagem de sistema operacional particular em uma região do Azure e, depois, copia a imagem para uma região diferente em que você quer implantar novas VMs com base nesta imagem de sistema operacional particular. Basta executar o seguinte comando na VM:

   ```
   service guestregister restart
   ```

## Área de trabalho Gnome

Se você deseja usar a área de trabalho Gnome para instalar um sistema de demonstração completo SAP em uma única VM - SAP GUI, navegador, console de gerenciamento SAP e assim por diante, aqui vai uma dica para instalá-lo nas imagens SLES do Azure:

   SLES 11

   ```
   zypper in -t pattern gnome
   ```

   SLES 12

   ```
   zypper in -t pattern gnome-basic
   ```

## Suporte de SAP-Oracle no Linux na nuvem

Há uma restrição de suporte da Oracle para Linux em ambientes virtualizados. Este é um tópico geral, não um tópico específico do Azure. No entanto, é importante entendê-lo. O SAP não dará suporte ao Oracle no SUSE ou ao Red Hat em uma nuvem pública como o Azure. Os clientes devem contatar a Oracle diretamente para discutir este assunto.

<!---HONumber=AcomDC_0114_2016-->