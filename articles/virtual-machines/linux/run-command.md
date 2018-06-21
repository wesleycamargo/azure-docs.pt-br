---
title: Executar scripts de shell em uma VM Linux no Azure
description: Este tópico descreve como executar scripts dentro de uma máquina virtual Linux do Azure usando o recurso Executar Comando
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: a7e828aa79d3a7fba53c0ef9f683ed16afc9a3e6
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267440"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Executar scripts de shell em sua VM Linux com o recurso Executar Comando

O recurso Executar Comando usa o agente da VM para executar scripts de shell dentro de uma VM Linux do Azure. Esses scripts podem ser usados para gerenciamento geral do computador ou de aplicativos e para diagnosticar e corrigir rapidamente o acesso à VM, problemas de rede e colocar a VM de volta em um bom estado.

## <a name="benefits"></a>Benefícios

Há várias opções que podem ser usadas para acessar suas máquinas virtuais. O recurso Executar Comando pode executar scripts em suas máquinas virtuais remotamente usando o agente de VM. O recurso Executar Comando pode ser usado por meio do Portal do Azure, [API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), da [CLI do Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) ou do [PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand).

Essa funcionalidade é útil em todos os cenários em que você deseja executar um script dentro de uma máquina virtual e é a única maneira de solucionar e corrigir uma máquina virtual que não tem a porta RDP ou SSH aberta devido a uma rede ou a uma configuração de usuário administrativo incorreta.

## <a name="restrictions"></a>Restrições

A seguir há uma lista de restrições que estão presentes ao usar o recurso Executar Comando.

* A saída está limitada aos últimos 4096 bytes
* O tempo mínimo para executar um script é de cerca de 20 segundos
* Scripts executados por padrão como usuário com privilégios elevados no Linux
* É possível executar um script por vez
* Não é possível cancelar um script em execução
* O tempo máximo que um script pode ser executado é 90 minutos. Após esse tempo, ele atingirá o tempo limite

## <a name="azure-cli"></a>CLI do Azure

A seguir, veja um exemplo que usa o comando [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) para executar um script de shell em uma VM Linux do Azure.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Para executar comandos como um usuário diferente, é possível usar `sudo -u` para especificar uma conta de usuário a ser usada.

## <a name="azure-portal"></a>Portal do Azure

Navegue até uma VM no [Azure](https://portal.azure.com) e selecione **Executar comando** em **OPERAÇÕES**. Há uma lista dos comandos disponíveis a serem executados na VM.

![Executar lista de comandos](./media/run-command/run-command-list.png)

Escolha um comando a ser executado. Alguns dos comandos podem ter parâmetros de entrada obrigatórios ou opcionais. Para esses comandos, os parâmetros são apresentados como campos de texto para você fornecer os valores de entrada. Para cada comando, é possível exibir o script que está sendo executado expandindo **Exibir script**. **RunShellScript** é diferente dos outros comandos, porque permite a você fornecer seu próprio script personalizado. 

> [!NOTE]
> Os comandos internos não podem ser editados.

Quando o comando for escolhido, clique em **Executar** para executar o script. O script é executado e, quando concluído, retorna a saída e os erros na janela de saída. A captura de tela a seguir mostra um exemplo de saída da execução do comando **ifconfig**.

![Executar saída de script de comando](./media/run-command/run-command-script-output.png)

## <a name="available-commands"></a>Comandos disponíveis

Esta tabela mostra a lista de comandos disponíveis para VMs Linux. O comando **RunShellScript** pode ser usado para executar qualquer script personalizado desejado.

|**Nome**|**Descrição**|
|---|---|
|**RunShellScript**|Executa um script de shell do Linux.|
|**ifconfig**| Obtenha a configuração de todas as interfaces de rede.|

## <a name="limiting-access-to-run-command"></a>Limitando o acesso ao recurso Executar Comando

Listar os comandos de execução ou mostrar os detalhes de um comando requer a permissão `Microsoft.Compute/locations/runCommands/read`, que a função [Leitor](../../role-based-access-control/built-in-roles.md#reader) interna e superiores têm.

Executar um comando requer a permissão `Microsoft.Compute/virtualMachines/runCommand/action`, que a função [Colaborador](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e superior têm.

É possível usar uma das funções [internas](../../role-based-access-control/built-in-roles.md) ou criar uma função [personalizada](../../role-based-access-control/custom-roles.md) para usar o recurso Executar Comando.

## <a name="next-steps"></a>Próximas etapas

Consulte [Run scripts in your Linux VM](run-scripts-in-vm.md) (Executar scripts em sua VM Linux) para conhecer outras maneiras de executar scripts e comandos remotamente em sua VM.
