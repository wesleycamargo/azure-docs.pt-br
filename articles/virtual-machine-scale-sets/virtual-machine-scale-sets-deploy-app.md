---
title: "Implantar um aplicativo em conjuntos de dimensionamento de máquinas virtuais | Microsoft Docs"
description: "Implantar um aplicativo em Conjuntos de escala de máquina virtual"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: f13545d753690534e0e645af67efcf1b524837eb
ms.openlocfilehash: dad27b11b5f02ed41826b82882cc5089eb69cb04
ms.lasthandoff: 02/09/2017


---
# <a name="deploy-an-app-on-virtual-machine-scale-sets"></a>Implantar um aplicativo em Conjuntos de escala de máquina virtual
Um aplicativo em execução em um Conjunto de escala de VM normalmente é implantado usando uma destas três maneiras:

* Instalando um novo software em uma imagem de plataforma no momento da implantação. Uma imagem de plataforma neste contexto é uma imagem de sistema operacional do Azure Marketplace, como o Ubuntu 16.04, o Windows Server 2012 R2 etc.

Você pode instalar um novo software em uma imagem de plataforma usando uma [Extensão de VM](../virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Uma Extensão de VM é um software que é executado no momento da implantação de uma VM. Execute o código que quiser no momento da implantação usando uma extensão de script personalizado. [Confira aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) um exemplo de modelo do Azure Resource Manager com duas extensões de VM: uma Extensão de Script Personalizado do Linux para instalar o Apache e PHP, e uma Extensão de Diagnóstico para a emissão de dados de desempenho usados pelo Dimensionamento Automático do Azure.

Uma vantagem dessa abordagem é ter um nível de separação entre o código do aplicativo e o sistema operacional, podendo manter seu aplicativo separadamente. Isso também significa que também há mais partes móveis, e o tempo de implantação da VM pode ser maior se o script precisar baixar e configurar muita coisa.

**Se você passar informações confidenciais em seu comando da Extensão de Script Personalizado (como uma senha), especifique o `commandToExecute` no atributo `protectedSettings` da Extensão de Script Personalizado em vez do `settings`atributo.**

* Crie uma imagem de VM personalizada que inclui o sistema operacional e o aplicativo em um único VHD. Aqui, o conjunto de escala é composto por um conjunto de VMs copiado de uma imagem criada por você, e que você precisa manter. Essa abordagem não exige configuração adicional no momento da implantação da VM. No entanto, na versão `2016-03-30` dos Conjuntos de escala de VM (e em versões anteriores), os discos do sistema operacional para as VMs no conjunto de escala são limitados a uma única conta de armazenamento. Assim, você pode ter no máximo 40 VMs em um conjunto de escala, em vez do limite de 100 VMs por conjunto de escala com imagens de plataforma. Consulte [Visão geral do design do Conjunto de Dimensionamento](virtual-machine-scale-sets-design-overview.md) para obter mais detalhes.

    >[!NOTE]
    >A versão `2016-04-30-preview` da API de Conjuntos de Dimensionamento de VMs oferece suporte ao uso de Managed Disks do Azure para o disco de sistema operacional e discos de dados extras. Para obter mais informações, consulte [Visão Geral do Managed Disks](../storage/storage-managed-disks-overview.md) e [Usar discos de dados anexados](virtual-machine-scale-sets-attached-disks.md). 

* Implantar uma imagem personalizada ou de plataforma, que é basicamente um host do contêiner, e instalar o aplicativo como um ou mais contêineres que você gerencia com um orquestrador ou uma ferramenta de gerenciamento de configuração. Uma ponto positivo dessa abordagem é que você abstrai sua infraestrutura de nuvem da camada do aplicativo e as mantêm separadamente.

## <a name="what-happens-when-a-vm-scale-set-scales-out"></a>O que acontece quando um Conjunto de escala de VM é escalado horizontalmente?
Quando você adiciona uma ou mais VMs a um conjunto de escala aumentando a capacidade, manualmente ou por meio de dimensionamento automático, o aplicativo é instalado automaticamente. Por exemplo, se o conjunto de escala tiver extensões definidas, elas sempre serão executadas em uma nova VM no momento da criação. Se o conjunto de escala tiver base em uma imagem personalizada, qualquer VM nova será uma cópia da imagem personalizada de origem. Se as VMs do conjunto de escala forem hosts de contêiner, você poderá ter um código de inicialização para carregar os contêineres em uma Extensão de Script Personalizado, ou uma extensão pode instalar um agente registrado com um orquestrador de cluster (como o Serviço de Contêiner do Azure).

## <a name="how-do-you-manage-application-updates-in-vm-scale-sets"></a>Como gerenciar atualizações de aplicativos em Conjuntos de escala de VM?
Para atualizações de aplicativos em Conjuntos de escala de VM, três abordagens principais acompanham os três métodos de implantação de aplicativo descritos anteriormente:

* Atualizando com extensões de VM. Quaisquer extensões de VM definidas para um Conjunto de escala de VM são executadas sempre que uma nova VM é implantada, uma VM existente tem sua imagem refeita ou uma extensão de VM é atualizada. Se você precisar atualizar seu aplicativo, a atualização direta de um aplicativo por meio de extensões será uma abordagem viável, basta atualizar a definição da extensão. Uma forma simples de fazer isso é alterando o fileUris a fim de apontar para o novo software.

* A abordagem de imagem personalizada imutável. Quando você cria o aplicativo (ou componentes do aplicativo) em uma imagem de VM, é possível se concentrar na criação de um pipeline confiável a fim de automatizar a compilação, teste e implantação das imagens. Você pode projetar sua arquitetura para facilitar a troca rápida de um conjunto de escala de teste para produção. Um bom exemplo dessa abordagem é o [trabalho de driver do Azure Spinnaker](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/).

Packer e Terraform também oferecem suporte ao Azure Resource Manager, assim também é possível definir suas imagens “como código” e criá-las no Azure, e depois usar o VHD em seu conjunto de escala. No entanto, isso seria problemático para imagens do Marketplace, nas quais extensões/scripts personalizados se tornam mais importantes, já que você não manipula diretamente os bits do Marketplace.

* Atualizar contêineres. Abstrair o gerenciamento de ciclo de vida do aplicativo para um nível acima da infraestrutura de nuvem, por exemplo, encapsulando aplicativos e componentes de aplicativo em contêineres e gerenciando esses contêineres por meio de orquestradores de contêiner e gerenciadores de configuração como Chef/Puppet.

As VMs do conjunto de escala se tornam um substrato estável para os contêineres e exigem apenas atualizações ocasionais de segurança e de sistema operacional. Conforme mencionado, o Serviço de Contêiner do Azure é um bom exemplo de como usar essa abordagem e criar um serviço em torno dele.

## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>Como você implementa uma atualização de SO em domínios de atualização?
Vamos supor que você deseja atualizar sua imagem de SO, mantendo o Conjunto de escala de VM em execução. Uma maneira de fazer isso é atualizando as imagens da VM, uma VM por vez. Faça isso com o PowerShell ou a CLI do Azure. Há comandos separados para atualizar o modelo do Conjunto de Dimensionamento de Máquinas Virtuais (como a configuração é definida) e emitir chamadas de "atualização manual" em VMs individuais. O documento [Atualizar um conjunto de dimensionamento de máquinas virtuais](./virtual-machine-scale-sets-upgrade-scale-set.md) do Azure também fornece mais informações sobre quais opções estão disponíveis para executar atualizações de sistema operacional através de um conjunto de dimensionamento de VMs.


