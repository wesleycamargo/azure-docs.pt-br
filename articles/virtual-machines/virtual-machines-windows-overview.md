---
title: "Visão geral de máquinas virtuais do Windows | Microsoft Docs"
description: "Saiba mais sobre como criar e gerenciar máquinas virtuais do Windows no Azure."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: fbae9c8e-2341-4ed0-bb20-fd4debb2f9ca
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/20/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7c7995e7242b3e07692fa89d05c4ba1fa230bfd2


---
# <a name="overview-of-windows-virtual-machines-in-azure"></a>Visão geral das máquinas virtuais do Windows no Azure
VM (Máquinas Virtuais) do Azure é um dos vários tipos de [recursos de computação sob demanda escalonáveis](../app-service-web/choose-web-site-cloud-service-vm.md) oferecidos pelo Azure. Normalmente, você escolhe uma VM quando precisar de mais controle sobre o ambiente de computação do que as outras opções oferecem. Este artigo fornece informações sobre o que você deve considerar antes de criar uma VM, como criá-la e como gerenciá-la.

Uma VM do Azure oferece a flexibilidade da virtualização sem a necessidade de comprar e manter o hardware físico que a executa. No entanto, você ainda precisa manter a VM executando tarefas, como configurar, corrigir e instalar o software que será executado nela.

Máquinas virtuais do Azure podem ser usadas de várias maneiras. Alguns exemplos incluem:

* **Desenvolvimento e teste** – as VMs do Azure oferecem uma rápida e maneira fácil de criar um computador com configurações específicas, necessárias para codificar e testar um aplicativo.
* **Aplicativos na nuvem** – como a demanda por seu aplicativo pode flutuar, pode fazer sentido, em termos econômicos, executá-lo em uma VM no Azure. Você paga por VMs extras quando precisa delas e as desliga quando não são necessárias.
* **Datacenter estendido** – máquinas virtuais em uma rede virtual do Azure podem ser facilmente conectadas à rede de sua organização.

O número de VMs que o aplicativo usa pode ser escalado verticalmente e horizontalmente para atender às suas necessidades.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>O que é necessário pensar antes de criar uma VM?
Sempre há uma infinidade de [considerações de design](virtual-machines-windows-infrastructure-virtual-machine-guidelines.md) quando você cria uma infraestrutura de aplicativo no Azure. Estes aspectos de uma VM são importantes a considerar antes de começar:

* Os nomes dos recursos do aplicativo
* O local onde os recursos são armazenados
* O tamanho da VM
* O número máximo de VMs que podem ser criadas
* O sistema operacional que a VM executa
* A configuração da VM após ela ser iniciada
* Os recursos relacionados dos quais a VM precisa

### <a name="naming"></a>Nomenclatura
Uma máquina virtual tem um [nome](virtual-machines-windows-infrastructure-naming-guidelines.md) atribuído a ela e tem um nome de computador configurado como parte do sistema operacional. O nome de uma VM pode ter até 15 caracteres.

Se você usar o Azure para criar o disco do sistema operacional, o nome do computador e o nome da máquina virtual serão os mesmos. Se você [carregar e usar sua própria imagem](virtual-machines-windows-upload-image.md) que contém um sistema operacional previamente configurado e usá-la para criar uma máquina virtual, os nomes poderão ser diferentes. Recomendamos que, ao carregar seu próprio arquivo de imagem, você use o mesmo nome para o computador no sistema operacional e a máquina virtual.

### <a name="locations"></a>Locais
Todos os recursos criados no Azure são distribuídos entre várias [regiões geográficas](https://azure.microsoft.com/regions/) em todo o mundo. Normalmente, a região é chamada **local** quando você cria uma VM. Para uma VM, a localização especifica onde os discos rígidos virtuais são armazenados.

Esta tabela mostra algumas das maneiras de obter uma lista dos locais disponíveis.

| Método | Descrição |
| --- | --- |
| Portal do Azure |Selecione um local na lista quando você criar uma VM. |
| Azure PowerShell |Use o comando [Get-AzureRmLocation](https://msdn.microsoft.com/library/mt619449.aspx). |
| API REST |Use a operação [Listar locais](https://msdn.microsoft.com/library/dn790540.aspx). |

### <a name="vm-size"></a>Tamanho da VM
O [tamanho](virtual-machines-windows-sizes.md) da VM que você usa é determinado pela carga de trabalho que deseja executar. O tamanho que você escolhe, em seguida, determina fatores como capacidade de processamento, memória e armazenamento. O Azure oferece uma grande variedade de tamanhos para oferecer suporte a muitos tipos de usos.

O Azure cobra um [preço por hora](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) com base no tamanho da VM e do sistema operacional. Para horas parciais, o Azure cobrará somente os minutos usados. O armazenamento terá o preço e será cobrado separadamente.

### <a name="vm-limits"></a>Limites de VM
Sua assinatura do Azure tem [limites de cota](../azure-subscription-service-limits.md) padrão que podem afetar a implantação de muitas VMs para seu projeto. O limite atual por assinatura é de 20 VMs por região. Os limites podem ser aumentados preenchendo um tíquete de suporte para solicitar um aumento.

### <a name="operating-system-disks-and-images"></a>Imagens e discos de sistema operacional
As máquinas virtuais usam [VHDs (discos rígidos virtuais)](virtual-machines-windows-about-disks-vhds.md) para armazenar seus dados e sistema operacional (SO). Os VHDs também são usados para as imagens que você pode optar por instalar um sistema operacional. 

O Azure fornece muitas [imagens do marketplace](https://azure.microsoft.com/marketplace/virtual-machines/) para usar com várias versões e tipos de sistemas operacionais Windows Server. As imagens do Marketplace são identificadas por editor de imagem, oferta, sku e versão (normalmente, a versão é especificada como a versão mais recente). 

Esta tabela mostra algumas maneiras de encontrar as informações de uma imagem.

| Método | Descrição |
| --- | --- |
| Portal do Azure |Os valores são especificados automaticamente quando você seleciona uma imagem a ser usada. |
| Azure PowerShell |[Get-AzureRMVMImagePublisher](https://msdn.microsoft.com/library/mt603484.aspx) -local "local"<BR>[Get-AzureRMVMImageOffer](https://msdn.microsoft.com/library/mt603824.aspx) -local "local"-"publisherName" do editor<BR>[Get-AzureRMVMImageSku](https://msdn.microsoft.com/library/mt619458.aspx) -Location "location" -Publisher "publisherName" -Offer "offerName" |
| APIs REST |[Listar editores de imagem](https://msdn.microsoft.com/library/mt743702.aspx)<BR>[Listar ofertas de imagem](https://msdn.microsoft.com/library/mt743700.aspx)<BR>[Listar skus de imagem](https://msdn.microsoft.com/library/mt743701.aspx) |

Você pode optar por [carregar e usar sua própria imagem](virtual-machines-windows-upload-image.md) e, quando faz isso, o nome do editor, da oferta e da sku não são usados.

### <a name="extensions"></a>Extensões
As [extensões](virtual-machines-windows-extensions-features.md) de VM dão à VM recursos adicionais por meio de configuração pós-implantação e tarefas automatizadas.

Estas tarefas comuns podem ser realizadas usando extensões:

* **Executar scripts personalizados** – a [Extensão de Script Personalizado](virtual-machines-windows-extensions-customscript.md) o ajuda a configurar as cargas de trabalho na VM executando o script quando a VM é provisionada.
* **Implantar e gerenciar configurações** – a [Extensão de Configuração de Estado Desejado (DSC) do PowerShell](virtual-machines-windows-extensions-dsc-overview.md) ajuda a configurar o DSC em uma VM para gerenciar configurações e ambientes.
* **Coletar dados de diagnóstico** – a [Extensão de Diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) o ajuda a configurar a VM para coletar dados de diagnóstico que podem ser usados para monitorar a integridade do aplicativo.

### <a name="related-resources"></a>Recursos relacionados
Os recursos nesta tabela são usados por VM e precisam existir ou ser criados quando a VM é criada.

| Recurso | Obrigatório | Descrição |
| --- | --- | --- |
| [Grupo de recursos](../azure-resource-manager/resource-group-overview.md) |Sim |A VM deve estar contida em um grupo de recursos. |
| [Conta de armazenamento](../storage/storage-create-storage-account.md) |Sim |A VM precisa da conta de armazenamento para armazenar seus discos rígidos virtuais. |
| [Rede virtual](../virtual-network/virtual-networks-overview.md) |Sim |A VM deve ser membro de uma rede virtual. |
| [Endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |Não |A VM pode ter um endereço IP público atribuído a ela para acessá-la remotamente. |
| [Interface de rede](../virtual-network/virtual-network-network-interface-overview.md) |Sim |A VM precisa de interface de rede para se comunicar na rede. |
| [Discos de dados](virtual-machines-windows-attach-disk-portal.md) |Não |A VM pode incluir discos de dados para expandir os recursos de armazenamento. |

## <a name="how-do-i-create-my-first-vm"></a>Como criar minha primeira VM?
Você tem várias opções para criar a VM. A opção que você escolherá depende do ambiente em que está. 

Esta tabela fornece informações para ajudá-lo a criar a VM.

| Método | Artigo |
| --- | --- |
| Portal do Azure |[Criar uma máquina virtual que executa o Windows usando o portal](virtual-machines-windows-hero-tutorial.md) |
| Modelos |[Criar uma máquina virtual do Windows com um modelo do Gerenciador de Recursos](virtual-machines-windows-ps-template.md) |
| Azure PowerShell |[Criar uma VM do Windows usando o PowerShell](virtual-machines-windows-ps-create.md) |
| SDKs do cliente |[Implantar recursos do Azure usando C#](virtual-machines-windows-csharp.md) |
| APIs REST |[Criar ou atualizar uma VM](https://msdn.microsoft.com/library/mt163591.aspx) |

Você espera que isso nunca aconteça, mas ocasionalmente algo dá errado. Se essa situação ocorrer, examine as informações em [Solucionar problemas de implantação do Resource Manager com a criação de uma máquina virtual do Windows no Azure](virtual-machines-windows-troubleshoot-deployment-new-vm.md).

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Como gerenciar a VM que criei?
As VMs podem ser gerenciadas usando um portal baseado em navegador, ferramentas de linha de comando com suporte para scripts ou diretamente por meio de APIs. Algumas tarefas de gerenciamento típicas que você pode realizar são obter informações sobre uma VM, fazer logon em uma VM, gerenciar a disponibilidade e realizar backups.

### <a name="get-information-about-a-vm"></a>Obter informações sobre uma VM
Esta tabela mostra algumas das maneiras de obter informações sobre uma VM.

| Método | Descrição |
| --- | --- |
| Portal do Azure |No menu de hub, clique em **Máquinas Virtuais** e, em seguida, selecione a VM na lista. Na folha da VM, você tem acesso a informações de visão geral, define valores e métricas de monitoramento. |
| Azure PowerShell |Para obter informações sobre como usar o PowerShell para gerenciar VMs, confira [Gerenciar máquinas virtuais do Azure usando o Resource Manager e o PowerShell](virtual-machines-windows-ps-manage.md). |
| API REST |Use a operação [Obter informações da VM](https://msdn.microsoft.com/library/mt163682.aspx) para obter informações sobre uma VM. |
| SDKs do cliente |Para obter informações sobre como usar c# para gerenciar VMs, confira [Gerenciar máquinas virtuais do Azure usando o Azure Resource Manager e C#](virtual-machines-windows-csharp-manage.md). |

### <a name="log-on-to-the-vm"></a>Faça logon na VM
Você usa o botão [Conectar](virtual-machines-windows-connect-logon.md) no portal do Azure para iniciar uma sessão da Área de Trabalho Remota (RDP). Às vezes, podem ocorrer problemas ao se tentar usar uma conexão remota. Se essa situação ocorrer, verifique as informações da Ajuda em [Solucionar problemas de conexões de Área de Trabalho Remota para uma máquina virtual do Azure executando o Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

### <a name="manage-availability"></a>Gerenciar disponibilidade
É importante entender como [garantir a alta disponibilidade](virtual-machines-windows-manage-availability.md) para o aplicativo. Essa configuração envolve a criação de várias VMs para garantir que pelo menos uma esteja em execução.

Para sua implantação se qualificar para nosso Contrato de Nível de Serviço de 99,95 de VM, você precisará implantar duas ou mais VMs que executem sua carga de trabalho dentro de um [conjunto de disponibilidade](virtual-machines-windows-infrastructure-availability-sets-guidelines.md). Essa configuração garante que as VMs sejam distribuídas entre vários domínios de falha e implantadas em hosts com janelas de manutenção diferentes. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) explica a disponibilidade garantida do Azure como um todo.

### <a name="back-up-the-vm"></a>Fazer backup da VM
Um [dos serviços de recuperação de cofre](../backup/backup-introduction-to-azure-backup.md) é usado para proteger dados e ativos nos serviços de Backup do Azure e do Azure Site Recovery. Você pode usar um cofre de serviços de recuperação para [implantar e gerenciar backups para VMs implantadas pelo Gerenciador de Recursos usando o PowerShell](../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Próximas etapas
* Se seu objetivo é trabalhar com VMs do Linux, confira [Azure e Linux](virtual-machines-linux-azure-overview.md).
* Saiba mais sobre as diretrizes para configurar sua infraestrutura no [Passo a passo de infraestrutura do Azure de exemplo](virtual-machines-windows-infrastructure-example.md).
* Siga as [Práticas recomendadas para executar uma VM do Windows no Azure](virtual-machines-windows-guidance-compute-single-vm.md).




<!--HONumber=Nov16_HO2-->


