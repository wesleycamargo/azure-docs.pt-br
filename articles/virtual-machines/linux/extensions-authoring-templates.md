---
title: "Criando modelos com extensões de VM do Linux | Microsoft Docs"
description: "Saiba mais sobre como criar modelos do Azure Resource Manager com extensões de VMs do Linux"
services: virtual-machines-linux
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 322f8f0b-6697-4acb-b5f3-b3f58d28358b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8b017306474670bf8dde1440128e16ec35146f24
ms.lasthandoff: 04/03/2017


---
# <a name="authoring-azure-resource-manager-templates-with-linux-vm-extensions"></a>Criando modelos do Azure Resource Manager com extensões de VM do Linux
[!INCLUDE [virtual-machines-common-extensions-authoring-templates](../../../includes/virtual-machines-common-extensions-authoring-templates.md)]

Na CLI do Azure, execute o seguinte comando:

      Azure VM extension list

Esse comando retorna o nome do editor, o nome da extensão e a versão da seguinte maneira:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Essas três propriedades são mapeadas para “publisher”, “type” e “typeHandlerVersion”, respectivamente, no trecho de código do modelo acima.

> [!NOTE]
> É sempre recomendado usar a versão mais recente da extensão para obter a funcionalidade mais atualizada.
> 
> 

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>Identificando o esquema dos parâmetros de configuração da extensão
A próxima etapa da criação de um modelo de extensão é identificar o formato para fornecer os parâmetros de configuração. Cada extensão dá suporte a seu próprio conjunto de parâmetros.

Para ver configurações de exemplo das extensões do Linux, clique na documentação de [Exemplos de eExtensions do Linux](extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consulte o seguinte para obter um modelo completo com Extensões de VM.

[Extensão do Script Personalizado em uma VM do Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Depois de criar o modelo, é possível implantá-lo usando a CLI do Azure.


