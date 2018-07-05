---
title: Executar scripts do PowerShell em uma VM do Windows no Azure
description: Este tópico descreve como executar scripts do PowerShell em uma máquina virtual Windows do Azure usando o recurso Executar Comando
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: ddbac24020110e32792286a1ac64070316cfb081
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332707"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>Executar scripts do PowerShell em sua VM Windows com o recurso Executar Comando

O recurso Executar Comando usa o agente da VM para executar scripts de shell do PowerShell dentro de uma VM Windows do Azure. Esses scripts podem ser usados para gerenciamento geral do computador ou de aplicativos e para diagnosticar e corrigir rapidamente o acesso à VM, problemas de rede e colocar a VM de volta em um bom estado.

## <a name="benefits"></a>Benefícios

Há várias opções que podem ser usadas para acessar suas máquinas virtuais. O recurso Executar Comando pode executar scripts em suas máquinas virtuais remotamente usando o agente de VM. O recurso Executar Comando pode ser usado por meio do Portal do Azure, [API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), da [CLI do Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) ou do [PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand).

Essa funcionalidade é útil em todos os cenários em que você deseja executar um script dentro de uma máquina virtual e é a única maneira de solucionar e corrigir uma máquina virtual que não tem a porta RDP ou SSH aberta devido a uma rede ou a uma configuração de usuário administrativo incorreta.

## <a name="restrictions"></a>Restrições

As seguintes considerações se aplicam ao usar o recurso Executar Comando:

* A saída está limitada aos últimos 4096 bytes
* O tempo mínimo para executar um script é de cerca de 20 segundos
* Os scripts são executados como sistema no Windows
* É possível executar um script por vez
* Não é possível cancelar um script em execução
* O tempo máximo que um script pode ser executado é 90 minutos. Após esse tempo, ele atingirá o tempo limite

**PermissionsConfig-OrchestratorUsersGroup***GroupName***-OrchestratorUser***UserName***\-remote** 

## <a name="run-a-command"></a>Executar um comando

Navegue até uma VM no [Azure](https://portal.azure.com) e selecione **Executar comando** em **OPERAÇÕES**. Há uma lista dos comandos disponíveis a serem executados na VM.

![Executar lista de comandos](./media/run-command/run-command-list.png)

Escolha um comando a ser executado. Alguns dos comandos podem ter parâmetros de entrada obrigatórios ou opcionais. Para esses comandos, os parâmetros são apresentados como campos de texto para você fornecer os valores de entrada. Para cada comando, é possível exibir o script que está sendo executado expandindo **Exibir script**. **RunPowerShellScript** é diferente dos outros comandos, porque permite a você fornecer seu próprio script personalizado.

> [!NOTE]
> Os comandos internos não podem ser editados.

Quando o comando for escolhido, clique em **Executar** para executar o script. O script é executado e, quando concluído, retorna a saída e os erros na janela de saída. A captura de tela a seguir mostra um exemplo de saída da execução do comando **RDPSettings**.

![Executar saída de script de comando](./media/run-command/run-command-script-output.png)

## <a name="commands"></a>Comandos

Esta tabela mostra a lista de comandos disponíveis para VMs Windows. O comando **RunPowerShellScript** pode ser usado para executar qualquer script personalizado desejado.

|**Nome**|**Descrição**|
|---|---|
|**RunPowerShellScript**|Executa um script do PowerShell|
|**EnableRemotePS**|Configura o computador para habilitar o PowerShell remoto.|
|**EnableAdminAccount**|Verifica se a conta de administrador local está desabilitada e, em caso positivo, habilita a conta.|
|**IPConfig**| Mostra informações detalhadas do endereço IP, da máscara de sub-rede e do gateway padrão de cada adaptador associado ao TCP/IP.|
|**RDPSettings**|Verifica as configurações do registro e de política de domínio. Sugere as ações de política se o computador fizer parte de um domínio ou modifica as configurações para os valores padrão.|
|**ResetAccountPassword**| Redefine a senha da conta de administrador interno.|
|**ResetRDPCert**|Remove o certificado SSL associado ao ouvinte RDP e restaura a segurança dele para o padrão. Use este script se houver algum problema com o certificado.|
|**SetRDPPort**|Define o padrão ou o número da porta do usuário especificado para conexões de Área de Trabalho Remota. Habilita a regra de firewall para acesso de entrada à porta.|

## <a name="powershell"></a>PowerShell

O exemplo a seguir usa o cmdlet [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) para executar um script PowerShell em uma VM do Azure.

```azurepowershell-interactive
Invoke-AzureRmVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitando o acesso ao recurso Executar Comando

Listar os comandos de execução ou mostrar os detalhes de um comando requer a permissão `Microsoft.Compute/locations/runCommands/read`, que a função [Leitor](../../role-based-access-control/built-in-roles.md#reader) interna e superiores têm.

Executar um comando requer a permissão `Microsoft.Compute/virtualMachines/runCommand/action`, que a função [Colaborador](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e superior têm.

É possível usar uma das funções [internas](../../role-based-access-control/built-in-roles.md) ou criar uma função [personalizada](../../role-based-access-control/custom-roles.md) para usar o recurso Executar Comando.

## <a name="next-steps"></a>Próximas etapas

Consulte [Executar scripts em sua VM Windows](run-scripts-in-vm.md) para conhecer outras maneiras de executar scripts e comandos remotamente em sua VM.