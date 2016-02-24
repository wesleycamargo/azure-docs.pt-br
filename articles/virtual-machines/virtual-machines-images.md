<properties
	pageTitle="Sobre imagens de máquinas virtuais | Microsoft Azure"
	description="Saiba mais sobre como as imagens são usadas com máquinas virtuais no Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/15/2016"
	ms.author="cynthn"/>

# Sobre imagens de máquinas virtuais

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.

As imagens são usadas no Azure para fornecer uma nova máquina virtual com um sistema operacional. Uma imagem também pode ter um ou mais discos de dados. As imagens estão disponíveis de várias fontes:

-	O Azure oferece imagens no [Marketplace](https://azure.microsoft.com/gallery/virtual-machines/). Você encontrará as versões recentes do Windows Server e as distribuições do sistema operacional Linux. Algumas imagens também contêm aplicativos, como o SQL Server. Assinantes de MSDN Benefício e Pré-pago têm acesso a imagens adicionais.
-	A comunidade de software livre oferece imagens por meio do [VM Depot](http://vmdepot.msopentech.com/List/Index).
-	Você também pode armazenar e usar suas próprias imagens no Azure, seja capturando uma máquina virtual do Azure para usar como uma imagem ou carregando uma imagem.

## Sobre imagens de VM e de SO

Dois tipos de imagens podem ser usadas no Azure: *imagem da VM* e *imagem do SO*. Uma imagem de VM inclui um sistema operacional e todos os discos anexados a uma máquina virtual quando a imagem é criada. Esse é o tipo mais recente da imagem. Antes das imagens de VM serem introduzidas, uma imagem no Azure podia ter apenas um sistema operacional generalizado e nenhum disco adicional. Uma imagem de VM que contém apenas um sistema operacional generalizado é basicamente o mesmo que o tipo original da imagem, ou seja, a imagem do SO.

Você pode criar suas próprias imagens com base em uma máquina virtual no Azure ou uma máquina virtual em execução em outro lugar que você copiar e carregar. Se você quiser usar uma imagem para criar mais de uma máquina virtual, você precisará prepará-la para uso como uma imagem, generalizando-a. Para criar uma imagem do Windows Server, execute o comando Sysprep no servidor para generalizá-la antes de carregar o arquivo .vhd. Para obter detalhes sobre o Sysprep, consulte [Como usar o Sysprep: uma introdução](http://go.microsoft.com/fwlink/p/?LinkId=392030). Para criar uma imagem do Linux, dependendo da distribuição de software, você precisará executar um conjunto de comandos que são específicos à distribuição, bem como executar o Agente Linux do Azure.

## Trabalhando com imagens

Você pode usar a CLI (Interface de Linha de Comando) para Mac, Linux e Windows, ou ainda um módulo do Azure PowerShell, para gerenciar as imagens disponíveis para sua assinatura do Azure. Você também pode usar o portal clássico do Azure para algumas tarefas de imagem, porém a linha de comando oferece mais opções.

Para obter informações sobre como usar essas ferramentas com implantações do Gerenciador de Recursos, consulte [Como navegar e selecionar imagens de Máquina Virtual do Azure com o PowerShell e a CLI do Azure](resource-groups-vm-searching.md).

Para obter exemplos de como usar as ferramentas em uma implantação clássica:

- Para a CLI, consulte "Comandos para gerenciar suas imagens de máquina virtual do Azure" em [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Serviços do Azure](virtual-machines-command-line-tools.md)
- Consulte a seguinte lista de comandos a seguir para o Azure PowerShell. Para obter um exemplo de localização de uma imagem para criar uma VM, consulte "Etapa 3: determinar o ImageFamily" em [Usar o Azure PowerShell para criar e pré-configurar Máquinas Virtuais baseadas em Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

-	**Obter todas as imagens**:`Get-AzureVMImage`retorna uma lista de todas as imagens disponíveis na sua assinatura atual: as imagens, incluindo aquelas fornecidos pelo Azure ou parceiros. Isso significa que você pode obter uma lista grande. Os exemplos a seguir mostram como obter uma lista menor.
-	**Obter as famílias de imagem**:`Get-AzureVMImage | select ImageFamily` obtém uma lista das famílias de imagem, mostrando as cadeias de caracteres da propriedade **ImageFamily**.
-	**Obtenha todas as imagens em uma família específica**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-	**Localizar imagens da VM**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` isso funciona com a filtragem da propriedade DataDiskConfiguration, que só se aplica a imagens de VM. Este exemplo também filtra a saída para apenas o nome do rótulo e da imagem.
-	**Salvar uma imagem generalizada**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-	**Salvar uma imagem especializada**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip] O parâmetro OSState é obrigatório se você quiser criar uma imagem VM, que inclui discos de dados e o disco do sistema operacional. Se você não usar o parâmetro, o cmdlet criará uma imagem do SO. O valor do parâmetro indica se a imagem é generalizada ou especializada, com base em se o disco do sistema operacional foi preparado para reutilização.
-	**Excluir uma imagem**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`


## Recursos adicionais

[Diferentes maneiras de criar uma Máquina Virtual Linux](virtual-machines-linux-choices-create-vm.md)

[Diferentes maneiras de criar uma máquina virtual Windows](virtual-machines-windows-choices-create-vm.md)

<!---HONumber=AcomDC_0128_2016-->