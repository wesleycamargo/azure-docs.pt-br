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


Esta é uma lista de itens a serem considerados ao testar o SAP NetWeaver em VMs SUSE Linux no Microsoft Azure. No momento, não há nenhuma declaração oficial de suporte do SAP para SAP-Linux-Azure. Ainda assim, os clientes podem fazer alguns testes, demonstrações ou protótipos, desde que não dependam de suporte oficial do SAP.

A lista a seguir deve apenas ajudar a evitar algumas possíveis armadilhas e tornar a vida mais fácil:



## Imagens do SUSE no Microsoft Azure para testar o SAP 

Para testar o SAP no Azure, somente o SLES 11SP4 e o SLES 12 devem ser usados. Uma imagem especial do SUSE pode ser encontrada na Galeria de imagens do Azure: "SLES 11 SP3 for SAP CAL", mas ela não é destinada a uso geral. Ela é reservada para a solução da Cloud Appliance Library do SAP chamada SAP "CAL" ( <https://cal.sap.com/> ). Não havia nenhuma opção para ocultar esta imagem do público. Portanto, basta não usá-la.

Todos os novos testes no Azure devem ser feitos com o Gerenciador de Recursos do Azure. Para procurar imagens e versões do SUSE SLES usando o Azure PowerShell ou a CLI, use os comandos a seguir. A saída pode ser usada, por exemplo, para definir a imagem de sistema operacional em um modelo JSON para implantar uma nova VM do SUSE Linux: Os comandos do PS abaixo são válidos para uma versão > = 1.0.1 do Azure PowerShell

* procurar por editores existentes, incluindo SUSE:

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* procurar por ofertas existentes do SUSE:
      
   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
      
* procurar por ofertas do SUSE SLES:
      
   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```
      
* procurar por uma versão específica do SKU do SLES:
      
   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```
     
## Instalando o WALinuxAgent em uma VM SUSE 
 
O agente faz parte das imagens do SLES na Galeria do Azure. Estes são os locais em que é possível encontrar informações sobre como instalá-lo manualmente (por exemplo, ao carregar o VHD de um sistema operacional de SLES local):

<http://software.opensuse.org/package/WALinuxAgent>

<https://azure.microsoft.com/documentation/articles/virtual-machines-linux-endorsed-distributions/>

<https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/>

## Anexar discos de dados do Azure em uma VM Linux do Azure

NUNCA monte discos de dados do Azure em uma VM Linux do Azure por meio da identificação do dispositivo. Em vez disso, use a UUID. Tenha cuidado ao usar, por exemplo, ferramentas gráficas para montar discos de dados do Azure. Verifique as entradas em /etc/fstab. O problema com a id do dispositivo é que ela pode mudar, e a VM do Azure pode travar no processo de inicialização. É possível adicionar o parâmetro nofail em /etc/fstab para atenuar o problema. Mas observe que, com o nofail, os aplicativos podem usar o ponto de montagem como antes e talvez gravar no sistema de arquivos raiz caso um disco de dados do Azure externo não tenha sido montado durante a inicialização.

## Carregando uma VM SUSE do local no Azure

O blog a seguir descreve as etapas:

<https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-suse/>

Se você quiser carregar uma VM sem a etapa de desprovisionamento no final para manter, por exemplo, uma instalação existente do SAP, bem como o nome do host, os seguintes itens devem ser verificados:

* certifique-se de que o disco do sistema operacional esteja montado via UUID e NÃO por meio de identificação do dispositivo. Alterar para a UUID apenas em /etc/fstab NÃO é suficiente para o disco do sistema operacional. Não se esqueça também de adaptar o carregador de inicialização, por exemplo, via yast ou editando /boot/grub/menu.lst
* Caso você tenha usado o formato vhdx para o disco de sistema operacional do SUSE e o converta em vhd para carregar no Azure, é muito provável que o dispositivo de rede tenha mudado de eth0 para eth1. Para evitar problemas durante a inicialização no Azure posteriormente, ele deve ser alterada para eth0, como descrito aqui:

<https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/>

Além do que está descrito no artigo, também é recomendado remover

   /lib/udev/rules.d/75-persistent-net-generator.rules

Instalar o waagent também deve evitar qualquer possível problema, desde que não haja vários nics.

## Implantar uma VM SUSE no Azure

Novas VMs devem ser criadas por meio de arquivos de modelo json no novo modelo do Gerenciador de Recursos do Azure. Após a criação do arquivo de modelo json, é possível implantar a VM usando o seguinte comando da CLI como uma alternativa ao Powershell:

   ``` azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"
   
   ``` Encontre mais detalhes sobre os arquivos de modelo json aqui:

<https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/>

<https://azure.microsoft.com/documentation/templates/>

Encontre mais detalhes sobre a CLI e Gerenciador de Recursos do Azure aqui:

<https://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/>

## Chave de licença e hardware do SAP

Para a certificação oficial do SAP-Windows-Azure, um novo mecanismo foi introduzido para calcular a chave de hardware do SAP que é usada para a licença do SAP. O kernel do SAP precisou ser adaptado para fazer uso dele. As versões atuais de kernel do SAP para Linux não incluem essa alteração de código. Portanto, pode acontecer de, em certas situações (por exemplo, no redimensionamento da VM do Azure), as alterações de chave de hardware do SAP e a licença do SAP deixarem de ser válidas

## Pacote sapconf do SUSE

O SUSE fornece um pacote chamado "sapconf" que cuida de um conjunto de configurações específicas do SAP. Encontre mais detalhes sobre este pacote, o que ele faz e como instalá-lo e usá-lo aqui:

<https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/>

<http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems>

## Compartilhamento de NFS em instalações SAP distribuídas

No caso de uma instalação distribuída em que você quer instalar, por exemplo, o banco de dados e os servidores de aplicativos do SAP em VMs separadas, é possível compartilhar o diretório /sapmnt por meio do NFS. Caso haja problemas com as etapas de instalação após a criação do compartilhamento de NFS para /sapmnt, verifique se "no\_root\_squash" está definido para compartilhar. Esta foi a solução em um caso de teste interno


## Volumes lógicos

A LVM não é totalmente validada no Azure. Se você precisar de um grande volume lógico em vários discos de dados do Azure (por exemplo, para o banco de dados SAP), mdadm deve ser usado. Este é um ótimo blog que descreve como configurar o Linux RAID no Azure usando mdadm:

<https://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-raid/>


## Repositório do SUSE no Azure

Caso haja algum problema com o acesso ao repositório padrão do SUSE no Azure, há um comando simples para redefini-lo. Isso pode acontecer ao criar uma imagem de sistema operacional particular em uma região do Azure e, depois, copiar a imagem para uma região diferente em que você quer implantar novas VMs com base nesta imagem de sistema operacional particular. Basta executar o seguinte comando na VM:

   ```
   service guestregister restart
   ```

## Área de trabalho Gnome

Se alguém quiser usar a área de trabalho Gnome para instalar um sistema completo de demonstração do SAP em uma única VM, incluindo a GUI, o navegador e o console de gerenciamento do SAP, aqui vai uma dica para instalá-la nas imagens do SLES Azure:

   SLES 11

   ```
   zypper in -t pattern gnome
   ```
      
   SLES 12
   
   ```
   zypper in -t pattern gnome-basic
   ```

## Suporte da Oracle para SAP no Linux na nuvem
 
Na verdade, este não é um tópico específico do Azure, mas um geral. No entanto, é importante entender. Há uma restrição de suporte da Oracle para Linux em ambientes virtualizados. No final, isso significa que o SAP não dará suporte ao Oracle no SUSE ou no RedHat em uma nuvem pública como o Azure. Os clientes devem contatar a Oracle diretamente para discutir este assunto.

<!---HONumber=AcomDC_1223_2015-->