---
title: Introdução às máquinas virtuais do Azure Stack
description: Saiba mais sobre máquinas virtuais do Azure Stack
services: azure-stack
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: cd42a347de236de2e3374d7ac854779f4c222a00
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768139"
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Introdução às máquinas virtuais do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O Azure Stack oferece VMs (máquinas virtuais) como um tipo de um recurso de computação sob demanda e escalonável. Você pode escolher uma VM quando precisar de mais controle sobre o ambiente de computação. Este artigo fornece detalhes antes de criar sua primeira VM.

Uma VM do Azure Stack lhe dá a flexibilidade da virtualização sem a necessidade de gerenciar clusters ou máquinas individuais. No entanto, você ainda precisa manter a VM executando tarefas como configurar, corrigir e instalar o software que será executado nela.

Você pode usar máquinas virtuais do Azure Stack de várias maneiras. Por exemplo: 

- **Desenvolvimento e teste**  
    As VMs de pilha do Azure permitem que você criar um computador com uma configuração específica necessária para o código e testar um aplicativo.

- **Aplicativos na nuvem**  
    Porque a demanda por seu aplicativo pode flutuar, pode fazer sentido econômico para executá-lo em uma VM no Azure Stack. Você paga por VMs extras quando precisa deles e desligá-las quando você não fizer isso.

- **Datacenter estendido**  
    Máquinas virtuais em uma rede virtual do Azure Stack pode ser conectadas à rede da sua organização ou para o Azure.

As VMs que o aplicativo usa pode escalar verticalmente ou escalar horizontalmente, para tudo o que é necessário para atender às suas necessidades.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>O que é necessário pensar antes de criar uma VM

Sempre há considerações de design quando você cria uma infraestrutura de aplicativo no Azure Stack. Esses aspectos de uma VM são importantes a considerar antes de começar a criar sua infraestrutura:

- Os nomes dos recursos do aplicativo.
- O tamanho da VM.
- O número máximo de VMs que podem ser criadas.
- O sistema operacional que executa a VM.
- A configuração da VM depois de ter começado.
- Os recursos relacionados que a VM precisa.

### <a name="naming"></a>Nomenclatura

Uma máquina virtual tem um nome atribuído a ele e tem um nome de computador configurado como parte do sistema operacional. O nome de uma VM pode ter até 15 caracteres.

Se você usar o Azure Stack para criar o disco do sistema operacional, o nome do computador e o nome da máquina virtual são os mesmos. Se você carregar e usa sua própria imagem que contém um sistema operacional previamente configurado e usá-lo para criar uma máquina virtual, os nomes podem ser diferentes. Quando você carrega seu próprio arquivo de imagem, como uma prática recomendada, verifique se o nome do computador no sistema operacional e o nome da máquina virtual são iguais.

### <a name="vm-size"></a>Tamanho da VM

O tamanho da VM que você usa é determinado pela carga de trabalho que você deseja executar. O tamanho que você escolhe, em seguida, determina fatores como capacidade de processamento, memória e armazenamento. O Azure Stack oferece uma variedade de tamanhos para dar suporte a muitos tipos de usos.

### <a name="vm-limits"></a>Limites de VM

Sua assinatura tem limites de cota em vigor que pode afetar a implantação de VMs para seu projeto. O limite atual por assinatura é de 20 VMs por região.

### <a name="operating-system-disks-and-images"></a>Imagens e discos de sistema operacional

As máquinas virtuais usam discos rígidos virtuais (VHDs) para armazenar seus dados e sistema operacional (SO). Os VHDs também são usados para as imagens que você pode optar por instalar um sistema operacional. O Azure Stack fornece um marketplace para usar com várias versões e tipos de sistemas operacionais. Imagens do Marketplace são identificadas por Editor de imagem, oferta, SKU e versão (normalmente a versão é especificada como **mais recente**.)

A tabela a seguir mostra como localizar as informações de uma imagem:

|Método|DESCRIÇÃO|
|---------|---------|
|Portal do Azure Stack|Os valores são especificados automaticamente quando você seleciona uma imagem a ser usada.|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|APIs REST     |[Listar editores de imagem](/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Listar ofertas de imagem](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Listar SKUs de imagem](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Você pode optar por carregar e usar sua própria imagem. Se você fizer isso, o nome do Editor, oferta e SKU não são usados.

### <a name="extensions"></a>Extensões

As extensões VM oferecem VM recursos adicionais por meio de configuração pós-implantação e tarefas automatizadas.
Estas tarefas comuns podem ser realizadas usando extensões:

- **Executar scripts personalizados**  
    A extensão de Script personalizado ajuda você a configurar cargas de trabalho na VM executando o script quando a VM é provisionada.

- **Implantar e gerenciar configurações**  
    A extensão do PowerShell Desired State Configuration (DSC) ajuda você a configurar o DSC em uma máquina virtual para gerenciar configurações e ambientes.

- **Coletar dados de diagnóstico**  
    A extensão de diagnóstico do Azure ajuda você a configurar a VM para coletar dados de diagnóstico que podem ser usados para monitorar a integridade do seu aplicativo.

### <a name="related-resources"></a>Recursos relacionados

Os recursos na tabela a seguir são usados pela VM e precisam existir ou ser criada quando a VM é criada:

|Recurso|Obrigatório|DESCRIÇÃO|
|---------|---------|---------|
|Grupo de recursos|Sim|A VM deve estar contida em um grupo de recursos.|
|Conta de armazenamento|Não |A VM não é necessário para a conta de armazenamento para armazenar seus discos rígidos virtuais, se usar o Managed Disks. <br>A VM precisará da conta de armazenamento para armazenar seus discos rígidos virtuais se usando discos não gerenciados.|
|Rede virtual|Sim|A VM deve ser membro de uma rede virtual.|
|Endereço IP público|Não |A VM pode ter um endereço IP público atribuído a ela para acessá-la remotamente.|
|interface de rede|Sim|A VM precisa de interface de rede para se comunicar na rede.|
|Discos de dados|Não |A VM pode incluir discos de dados para expandir os recursos de armazenamento.|

## <a name="create-your-first-vm"></a>Crie sua primeira VM

Você tem várias opções para criar uma VM. Sua escolha depende de seu ambiente. A tabela a seguir fornece informações para ajudar você a começar a criar sua VM:

|Método|Artigo|
|---------|---------|
|Portal do Azure Stack|Criar uma máquina virtual do Windows com o portal do Azure Stack<br>[Criar uma máquina virtual do Linux usando o portal do Azure Stack](azure-stack-quick-linux-portal.md)|
|Modelos|Modelos de início rápido da pilha do Azure estão localizados em:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Criar uma máquina virtual do Windows usando o PowerShell no Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)<br>[Criar uma máquina virtual Linux usando o PowerShell no Azure Stack](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[Criar uma máquina virtual do Windows, usando a CLI do Azure Stack](azure-stack-quick-create-vm-windows-cli.md)<br>[Criar uma máquina virtual Linux usando a CLI do Azure Stack](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>Gerenciar sua VM

Você pode gerenciar VMs usando um portal baseado em navegador, ferramentas de linha de comando com suporte para scripts ou diretamente por meio de APIs. Algumas tarefas de gerenciamento típicas que você pode realizar são:

- Obtendo informações sobre uma VM
- Conectar-se a uma VM
- Gerenciar a disponibilidade
- Realização de backups

### <a name="get-information-about-your-vm"></a>Obter informações sobre sua VM

A tabela a seguir mostra algumas das maneiras como você pode obter informações sobre uma VM.

|Método|DESCRIÇÃO|
|---------|---------|
|Portal do Azure Stack|No menu hub, clique em máquinas virtuais e, em seguida, selecione a VM na lista. Na página para a VM, você tem acesso a informações de visão geral, definir valores e métricas de monitoramento.|
|Azure PowerShell|Gerenciamento de VMs é semelhante no Azure e o Azure Stack. Para obter mais informações sobre como usar o PowerShell, consulte o tópico a seguir do Azure:<br>[Criar e gerenciar VMs do Windows com o módulo do PowerShell do Azure](../../virtual-machines/windows/tutorial-manage-vm.md#understand-vm-sizes)|
|SDKs do cliente|Usar c# para gerenciar VMs é semelhante no Azure e o Azure Stack. Para obter mais informações, consulte o tópico a seguir do Azure:<br>[Criar e gerenciar VMs Windows no Azure usando c#](../../virtual-machines/windows/csharp.md)|

### <a name="connect-to-your-vm"></a>Conectar-se à sua VM

Você pode usar o **Connect** botão no portal do Azure Stack para se conectar à sua VM.

## <a name="next-steps"></a>Próximas etapas

- [Considerações para máquinas virtuais no Azure Stack](azure-stack-vm-considerations.md)
