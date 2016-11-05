---
title: Criando modelos com extensões de VM do Linux | Microsoft Docs
description: Saiba mais sobre como criar modelos do Azure Resource Manager com extensões de VMs do Linux
services: virtual-machines-linux
documentationcenter: ''
author: kundanap
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap

---
# Criando modelos do Azure Resource Manager com extensões de VM do Linux
[!INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

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

## Identificando o esquema dos parâmetros de configuração da extensão
A próxima etapa da criação de um modelo de extensão é identificar o formato para fornecer os parâmetros de configuração. Cada extensão dá suporte a seu próprio conjunto de parâmetros.

Para ver configurações de exemplo das extensões do Linux, clique na documentação de [Exemplos de extensões eletrônicas do Linux](virtual-machines-linux-extensions-configuration-samples.md).

Consulte o seguinte para obter um modelo completo com Extensões de VM.

[Extensão do Script Personalizado em uma VM do Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Depois de criar o modelo, é possível implantá-lo usando a CLI do Azure.

<!---HONumber=AcomDC_0601_2016-->