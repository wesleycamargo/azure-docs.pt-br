<properties
   pageTitle="Navegando e selecionando imagens de Máquina Virtual do Azure com o PowerShell e a CLI do Azure"
   description="Saiba como determinar o publicador, a oferta e SKU para imagens ao criar uma máquina virtual do Azure com o Gerenciador de recursos."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure"
   ms.date="05/29/2015"
   ms.author="rasquill"/>

# Navegando e selecionando imagens de Máquina Virtual do Azure com o PowerShell e a CLI do Azure

> [AZURE.NOTE]Quando você estiver procurando por imagens da VM neste tópico, você está usando o [Modo de gerenciamento de recursos do Azure](../resource-group-overview.md) com uma instalação recente de qualquer Interface do Azure de linha de comando para Mac, Linux e Windows ou do Windows PowerShell. Com a CLI do Azure, insira esse modo digitando `azure config mode arm`. Com o PowerShell, digite `Switch-AzureMode AzureResourceManager`. Consulte [CLI do Azure com gerenciamento de recursos](xplat-cli-azure-resource-manager.md) e [PowerShell com o gerenciamento de recursos do Azure](../powershell-azure-resource-manager.md) para obter detalhes mais completos de atualização e configuração.

## Tabela de imagens mais usados


| PublisherName | Oferta | Sku |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 |
| OpenLogic | CentOS | 7\.1 |
| CoreOS | CoreOS | Beta |
| CoreOS | CoreOS | Estável |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | Padrão |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | Enterprise |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-DW |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-OLTP |
| Canonical | UbuntuServer | 12\.04.5-LTS |
| Canônico | UbuntuServer | 14\.04.2-LTS |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |


## CLI do Azure

A maneira mais fácil e rápida de localizar uma imagem para usar com `azure vm quick-create` ou para criar um arquivo de modelo de grupo de recursos é chamar o comando `azure vm image list` e informar o local, o nome do editor (não diferencia maiúsculas de minúsculas!) e uma oferta – se você souber a oferta. Por exemplo, a lista a seguir é apenas uma pequena amostra - várias listas são bastante longas - se você souber que "Canonical" é um editor para a oferta "UbuntuServer".

    azure vm image list westus canonical ubuntuserver
    info:    Executing command vm image list
    warn:    The parameter --sku if specified will be ignored
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn                                               
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  12.04-DAILY  12.04.201504201  westus    canonical:ubuntuserver:12.04-DAILY:12.04.201504201
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201302250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201302250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201303250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201303250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201304150  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201304150
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305160  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305160
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305270  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305270
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306030  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306030
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306240  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306240
    
A coluna **Urn** será o formulário que você passa para `azure vm quick-create`.
    
Muitas vezes, no entanto, você ainda não saiba o que está disponível. Nesse caso, você pode navegar por imagens, descobrindo primeiro os editores usando `azure vm image list-publishers` e respondendo ao prompt de local com um local de data center que você pretende usar para o grupo de recursos. O exemplo a seguir lista todos os editores de imagem no local Oeste dos EUA (passar o argumento de local removendo os espaços dos locais padrão e escrevendo-os em minúsculas)

    azure vm image list-publishers
    info:    Executing command vm image list-publishers
    Location: westus
    + Getting virtual machine image publishers (Location: "westus")                
    data:    Publisher                                       Location
    data:    ----------------------------------------------  --------
    data:    a10networks                                     westus  
    data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
    data:    alertlogic                                      westus  
    data:    AlertLogic.Extension                            westus  

    
Essas listas podem ser muito longas, portanto, a lista de exemplo acima é simplesmente um trecho de código. Vamos supor que percebi que Canonical é, na verdade, um editor de imagem no local Oeste dos EUA. Agora você pode encontrar suas ofertas chamando “azure vm image list-offers” e passar o local e o editor em solicitações, como no exemplo a seguir:

    azure vm image list-offers           
    info:    Executing command vm image list-offers
    Location: westus
    Publisher: canonical
    + Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
    data:    Publisher  Offer         Location
    data:    ---------  ------------  --------
    data:    canonical  UbuntuServer  westus  
    info:    vm image list-offers command OK
    
Agora sabemos que, na região Oeste dos EUA, Canonical publica a oferta **UbuntuServer** no Azure. Mas quais skus? Para saber quais, você chama `azure vm image list-skus` e responde à solicitação com o local, editor e oferta que você descobriu.

    azure vm image list-skus           
    info:    Executing command vm image list-skus
    Location: westus
    Publisher: canonical
    Offer: ubuntuserver
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         sku          Location
    data:    ---------  ------------  -----------  --------
    data:    canonical  ubuntuserver  12.04-DAILY  westus  
    data:    canonical  ubuntuserver  12.04.2-LTS  westus  
    data:    canonical  ubuntuserver  12.04.3-LTS  westus  
    data:    canonical  ubuntuserver  12.04.4-LTS  westus  
    data:    canonical  ubuntuserver  12.04.5-LTS  westus  
    data:    canonical  ubuntuserver  12.10        westus  
    data:    canonical  ubuntuserver  14.04-beta   westus  
    data:    canonical  ubuntuserver  14.04-DAILY  westus  
    data:    canonical  ubuntuserver  14.04.0-LTS  westus  
    data:    canonical  ubuntuserver  14.04.1-LTS  westus  
    data:    canonical  ubuntuserver  14.04.2-LTS  westus  
    data:    canonical  ubuntuserver  14.10        westus  
    data:    canonical  ubuntuserver  14.10-beta   westus  
    data:    canonical  ubuntuserver  14.10-DAILY  westus  
    data:    canonical  ubuntuserver  15.04        westus  
    data:    canonical  ubuntuserver  15.04-beta   westus  
    data:    canonical  ubuntuserver  15.04-DAILY  westus  
    info:    vm image list-skus command OK
    
Com essas informações, agora você pode localizar exatamente a imagem que deseja invocando a chamada original na parte superior.

    azure vm image list westus canonical ubuntuserver 14.04.2-LTS
    info:    Executing command vm image list
    + Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "14.04.2-LTS" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn                                               
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201503090  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201503090
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.20150422   westus    canonical:ubuntuserver:14.04.2-LTS:14.04.20150422 
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201504270  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201504270
    info:    vm image list command OK
    
Agora você pode escolher exatamente a imagem que deseja usar. Para criar uma vm rapidamente usando as informações de URN que você acabou de encontrar, ou para usar um modelo com essas informações URN, consulte [Usando a CLI do Azure para Mac, Linux e Windows no Gerenciamento de Recursos do Azure](xplat-cli-azure-resource-manager.md).

## PowerShell

Ao criar uma nova máquina virtual com o Gerenciador de Recursos do Azure, em alguns casos é necessário especificar uma imagem com a combinação das propriedades de imagem a seguir:

- Editor
- Oferta
- SKU

Por exemplo, esses valores são necessários para o cmdlet **Set-AzureVMSourceImage** do PowerShell ou com um arquivo de modelo de grupo de recursos no qual você deve especificar o tipo de máquina virtual a ser criado.

Se você precisar determinar esses valores, você pode navegar pelas imagens para determinar esses valores:

1. Listando os editores de imagem.
2. Para um determinado editor, listando suas ofertas.
3. Para uma determinada oferta, listando seus SKUs.

Para fazer isso no PowerShell, primeiro alterne para o modo do Gerenciador de Recursos do Azure PowerShell.

	Switch-AzureMode AzureResourceManager 

A primeira etapa acima, lista os editores com esses comandos.

	$locName="<Azure location, such as West US>"
	Get-AzureVMImagePublisher -Location $locName | Select PublisherName

Preencha o nome do editor escolhido e execute esses comandos.

	$pubName="<publisher>"
	Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Preencha o nome da oferta escolhida e execute esses comandos.

	$offerName="<offer>"
	Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Na exibição do comando **Get-AzureVMImageSku**, você tem todas as informações de que precisa para especificar a imagem para uma nova máquina virtual.

Aqui está um exemplo.

	PS C:\> $locName="West US"
	PS C:\> Get-AzureVMImagePublisher -Location $locName | Select PublisherName
	
	PublisherName
	-------------
	a10networks
	aiscaler-cache-control-ddos-and-url-rewriting-
	alertlogic
	AlertLogic.Extension
	Barracuda.Azure.ConnectivityAgent
	barracudanetworks
	basho
	boxless
	bssw
	Canonical
	...

Para o editor "MicrosoftWindowsServer":

	PS C:\> $pubName="MicrosoftWindowsServer"
	PS C:\> Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer
	
	Offer
	-----
	WindowsServer

Para a oferta de "Windows Server":

	PS C:\> $offerName="WindowsServer"
	PS C:\> Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
	
	Skus
	----
	2008-R2-SP1
	2012-Datacenter
	2012-R2-Datacenter
	Windows-Server-Technical-Preview

Nesta lista, copie o nome do SKU escolhido e você terá todas as informações para o cmdlet **Set-AzureVMSourceImage** do PowerShell ou para um arquivo de modelo de grupo de recursos que exige que você especifique o editor, a oferta e o SKU para uma imagem.


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/
 

<!---HONumber=August15_HO6-->