---
title: Exemplo de script da CLI do Azure - Criar um host Docker | Microsoft Docs
description: Exemplo de script da CLI do Azure - Criar um host de Docker
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/15/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 298190e1a83b2f7ac11a302e42ad2a3ab92c5ab2
ms.lasthandoff: 04/06/2017

---

# <a name="create-a-vm-with-docker"></a>Criar uma máquina virtual com o Docker

Esse script cria uma máquina virtual com o Docker habilitado e inicia um contêiner do Docker que executa o NGINX. Após a execução do script, é possível acessar o servidor Web NGINX por meio do FQDN da máquina virtual do Azure. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/virtual-machine/create-docker-host/create-docker-host.sh "Host Docker")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar a implantação. Cada item em que a tabela contém links para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Cria a máquina virtual e a conecta à placa de rede, a rede virtual, à sub-rede e ao grupo de segurança de rede. Este comando também especifica a imagem de máquina virtual a ser usada e as credenciais administrativas.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) | Cria uma regra de grupo de segurança de rede para permitir o tráfego de entrada. Neste exemplo, a porta 80 está aberta para tráfego HTTP. |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | Adiciona e executa uma extensão da máquina virtual a uma VM. Neste exemplo, a extensão de VM do Docker é usada para configurar um host do Docker.|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI de máquina virtual adicionais podem ser encontrados na [documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

