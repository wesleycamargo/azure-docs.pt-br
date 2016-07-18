<properties 
	pageTitle="Reimplantar as máquinas virtuais Linux | Microsoft Azure" 
	description="Descreve como reimplantar máquinas virtuais Linux para atenuar problemas de conexão SSH." 
	services="virtual-machines-linux" 
	documentationCenter="virtual-machines" 
	authors="iainfoulds" 
	manager="timlt"
	tags="azure-resource-manager,top-support-issue" 
/>
	

<tags 
	ms.service="virtual-machines-linux" 
	ms.devlang="na" 
	ms.topic="support-article" 
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure" 
	ms.date="06/28/2016" 
	ms.author="iainfou" 
/>

# Reimplantar a máquina virtual em um novo nó do Azure

Se você enfrentou dificuldades para solucionar problemas de SSH ou de acesso ao aplicativo em uma VM (máquina virtual) do Azure, reimplantar a VM pode ajudar. Quando você reimplanta uma VM, ela é movida para um novo nó dentro da infraestrutura do Azure e, depois, é ligada novamente, mantendo todas as suas opções de configuração e recursos associados. Este artigo mostra como reimplantar uma VM usando a CLI do Azure ou o Portal do Azure.

> [AZURE.NOTE] Depois que você reimplanta uma VM, o disco temporário será perdido e os endereços IP dinâmicos associados à interface de rede virtual serão atualizados.


## Usando a CLI do Azure

Verifique se você tem a [CLI do Azure mais recente instalada](../xplat-cli-install.md) em seu computador e se você está no modo do resource manager (`azure config mode arm`).

Use o seguinte comando da CLI do Azure para reimplantar sua máquina virtual:

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

Você pode ver o status da VM mudar enquanto percorre o processo de reimplantação. O `PowerState` da VM mudará de 'Em execução' para 'Atualizando', depois para 'Iniciando' e finalmente para 'Em execução' enquanto percorre o processo de reimplantação para um novo host. Verifique o status das VMs dentro de um grupo de recursos com:

```bash
azure vm list -g <resourcegroup>
```


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## Próximas etapas
Você pode encontrar ajuda específica em [Solução de problemas de conexões SSH](virtual-machines-linux-troubleshoot-ssh-connection.md) ou [Etapas detalhadas de solução de problemas de SSH](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md) se você estiver enfrentando problemas para se conectar à sua VM. Você também pode ler [problemas com a solução de problemas de aplicativo](virtual-machines-linux-troubleshoot-app-connection.md) se não conseguir acessar um aplicativo em execução em sua VM.

<!---HONumber=AcomDC_0706_2016-->