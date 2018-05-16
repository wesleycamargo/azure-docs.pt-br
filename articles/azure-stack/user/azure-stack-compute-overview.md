---
title: Introdução às máquinas virtuais do Azure Stack
description: Saiba mais sobre máquinas virtuais de pilha do Azure
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 41e75a6806cc5ff13fad64fd415344376e0d6e88
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2018
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Introdução às máquinas virtuais do Azure Stack

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

## <a name="overview"></a>Visão geral
Uma pilha de máquina Virtual (VM) do Azure é um tipo de um recurso de computação sob demanda e dimensionável oferece pilha do Azure. Normalmente, você escolhe uma VM quando precisar de mais controle sobre o ambiente de computação do que as outras opções oferecem. Este artigo fornece informações sobre o que você deve considerar antes de criar uma VM, como criá-la e como gerenciá-la.

Uma VM de pilha do Azure oferece a flexibilidade da virtualização sem a necessidade de gerenciar clusters ou máquinas individuais. No entanto, você ainda precisa manter a VM, executando tarefas como configurar a aplicação de patch e instalar o software que será executado nele.

Você pode usar máquinas virtuais de pilha do Azure de várias maneiras. Por exemplo: 

* **Desenvolvimento e teste** – VMs de pilha do Azure oferecem uma rápida e a maneira fácil de criar um computador com uma configuração específica necessária para codificar e testar um aplicativo.

* **Aplicativos na nuvem** – porque a demanda para o seu aplicativo pode flutuar, pode fazer sentido econômico para executá-lo em uma VM na pilha do Azure. Você paga por VMs extras quando precisa delas e as desliga quando não são necessárias.

* **Estendido datacenter** – máquinas virtuais em uma rede virtual do Azure pilha facilmente pode ser conectado à rede da sua organização ou para o Azure.

As VMs que o aplicativo usa pode aumentar ou expandir para tudo o que é necessário para atender às suas necessidades.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>O que é necessário pensar antes de criar uma VM?

Sempre há várias considerações de design quando você cria uma infraestrutura de aplicativo na pilha do Azure. Esses aspectos de uma VM são importantes a considerar antes de começar a criar sua infraestrutura:

* Os nomes de recursos do aplicativo.
* O tamanho da VM.
* O número máximo de máquinas virtuais que podem ser criadas.
* O sistema operacional que executa a VM.
* A configuração da VM após ele ser iniciado.
* Os recursos relacionados a VM precisa.

### <a name="naming"></a>Nomenclatura

Uma máquina virtual tem um nome atribuído a ele e tem um nome de computador configurado como parte do sistema operacional. O nome de uma VM pode ter até 15 caracteres.

Se você usar a pilha do Azure para criar o disco do sistema operacional, o nome do computador e o nome da máquina virtual são iguais. Se você carrega e usar sua própria imagem que contém um sistema operacional configurado anteriormente e usá-lo para criar uma máquina virtual, os nomes podem ser diferentes. Quando você carrega seu próprio arquivo de imagem, verifique o nome do computador no sistema operacional e a máquina virtual Nomeie o mesmo como uma prática recomendada.

### <a name="vm-size"></a>Tamanho da VM

O tamanho da VM que você usa é determinado pela carga de trabalho que você deseja executar. O tamanho que você escolhe, em seguida, determina fatores como capacidade de processamento, memória e armazenamento. A pilha do Azure oferece uma ampla variedade de tamanhos para dar suporte a vários tipos de uso.

### <a name="vm-limits"></a>Limites VM

Sua assinatura tem limites de cota padrão que podem afetar a implantação de várias VMs para seu projeto. O limite atual por assinatura é de 20 VMs por região.

### <a name="operating-system-disks-and-images"></a>Imagens e discos de sistema operacional

As máquinas virtuais usam discos rígidos virtuais (VHDs) para armazenar seus dados e sistema operacional (SO). Os VHDs também são usados para as imagens que você pode optar por instalar um sistema operacional.
A pilha do Azure fornece um marketplace a ser usado com vários tipos de sistemas operacionais e versões. Imagens do Marketplace são identificadas pelo editor de imagem, oferta, sku e versão (normalmente versão é especificada como a versão mais recente.)

A tabela a seguir mostra algumas maneiras que você pode encontrar as informações de uma imagem:

|Método|DESCRIÇÃO|
|---------|---------|
|Portal do Azure de pilha|Os valores são especificados automaticamente quando você seleciona uma imagem a ser usada.|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|APIs REST     |[Listar editores de imagem](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Listar ofertas de imagem](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Listar SKUs de imagem](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Você pode optar por carregar e usar sua própria imagem. Se você fizer isso, o nome do fornecedor, a oferta e o sku não são usados.

### <a name="extensions"></a>Extensões

Extensões de VM oferecem os recursos adicionais de VM por meio de configuração de implantação de postagem e tarefas automatizadas.
Estas tarefas comuns podem ser realizadas usando extensões:

* Executar scripts personalizados – a extensão do Script personalizado ajuda você a configurar as cargas de trabalho na VM executando o script quando a VM é provisionada.
* Implantar e gerenciar configurações – extensão o PowerShell desejado configuração de estado (DSC) ajuda você a configurar DSC em uma máquina virtual para gerenciar configurações e ambientes.
* Dados de diagnóstico coleta – a extensão de diagnóstico do Azure ajudam você a configurar a VM para coletar dados de diagnóstico que podem ser usados para monitorar a integridade do seu aplicativo.

### <a name="related-resources"></a>Recursos relacionados

Os recursos na tabela a seguir são usados para a máquina virtual e precisam existir ou ser criada quando a VM é criada.


|Recurso|Obrigatório|DESCRIÇÃO|
|---------|---------|---------|
|Grupo de recursos|Sim|A VM deve estar contida em um grupo de recursos.|
|Conta de armazenamento|Sim|A VM precisa da conta de armazenamento para armazenar seus discos rígidos virtuais.|
|Rede virtual|Sim|A VM deve ser membro de uma rede virtual.|
|Endereço IP público|Não |A VM pode ter um endereço IP público atribuído a ela para acessá-la remotamente.|
|interface de rede|Sim|A VM precisa de interface de rede para se comunicar na rede.|
|Discos de dados|Não |A VM pode incluir discos de dados para expandir os recursos de armazenamento.|

## <a name="how-do-i-create-my-first-vm"></a>Como criar minha primeira VM?

Você tem várias opções para criar uma máquina virtual. Sua escolha depende do ambiente.
A tabela a seguir fornece informações para ajudá-lo a iniciar a criação de sua VM.


|Método|Artigo|
|---------|---------|
|Portal do Azure de pilha|Criar uma máquina virtual do Windows com o portal de pilha do Azure<br>[Criar uma máquina virtual do Linux usando o portal de pilha do Azure](azure-stack-quick-linux-portal.md)|
|Modelos|Modelos de início rápido da pilha do Azure estão localizados em:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Criar uma máquina virtual do Windows usando o PowerShell na pilha do Azure](azure-stack-quick-create-vm-windows-powershell.md)<br>[Criar uma máquina virtual Linux usando o PowerShell na pilha do Azure](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[Criar uma máquina virtual do Windows usando a CLI na pilha do Azure](azure-stack-quick-create-vm-windows-cli.md)<br>[Criar uma máquina virtual Linux usando a CLI na pilha do Azure](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Como gerenciar a VM que criei?

Você pode gerenciar máquinas virtuais usando um portal baseado em navegador, ferramentas de linha de comando com suporte para scripts ou diretamente por meio de APIs. Algumas tarefas típicas de gerenciamento que você pode executar são:

* Obtendo informações sobre uma máquina virtual
* Conectar-se a uma máquina virtual
* Gerenciar a disponibilidade
* Realização de backups

### <a name="get-information-about-a-vm"></a>Obter informações sobre uma VM

A tabela a seguir mostra algumas das maneiras como você pode obter informações sobre uma máquina virtual.


|Método|DESCRIÇÃO|
|---------|---------|
|Portal do Azure de pilha|No menu hub, clique em máquinas virtuais e, em seguida, selecione a VM na lista. Na página para a máquina virtual, você tem acesso às informações de visão geral, os valores de configuração e métricas de monitoramento.|
|Azure PowerShell|Gerenciamento de máquinas virtuais é semelhante no Azure e a pilha do Azure. Para obter mais informações sobre como usar o PowerShell, consulte o seguinte tópico do Azure:<br>[Criar e gerenciar máquinas virtuais do Windows com o módulo PowerShell do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|SDKs do cliente|Usando o c# para gerenciar VMs é semelhante no Azure e a pilha do Azure. Para obter mais informações, consulte o seguinte tópico do Azure:<br>[Criar e gerenciar máquinas virtuais do Windows no Azure usando o c#](https://docs.microsoft.com/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-the-vm"></a>Conectar-se à VM

Você pode usar o **conectar** botão no portal de pilha do Azure para conectar-se à VM.

## <a name="next-steps"></a>Próximas etapas

* [Considerações para máquinas virtuais na pilha do Azure](azure-stack-vm-considerations.md)
