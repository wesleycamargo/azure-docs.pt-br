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
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: e5362b32a0f93433c77c27a6fc116960c45404f9
ms.openlocfilehash: 3693e6f78541caeca8fd1c276028c0f64ca71ddc
ms.lasthandoff: 03/01/2017

---

# <a name="create-a-vm-with-docker"></a>Criar uma máquina virtual com o Docker

Este script de exemplo cria uma máquina virtual e, em seguida, usa a extensão de VM do Docker do Azure para configurar um host do Docker. A extensão de VM do Docker, em seguida, cria um contêiner executando NGINX. Por fim, o script configura um grupo de segurança de rede do Azure para todo o tráfego de entrada na porta 80. Após a execução do script com êxito, o servidor de web NGINX pode ser acessado por meio do FQDN da máquina virtual do Azure. 

Antes de executar esse script, certifique-se de que uma conexão com o Azure foi criada usando o comando `az login`.

Este exemplo funciona em um shell Bash. Para opções sobre como executar scripts da CLI do Azure no cliente Windows, veja [Execução da CLI do Azure no Windows](../virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/virtual-machine/create-docker-host/create-docker-host.sh "Host Docker")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Após a execução do exemplo de script, o comando a seguir pode ser usado para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

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

Os exemplos de script da CLI de máquina virtual adicionais podem ser encontrados na [documentação da VM Linux do Azure](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

