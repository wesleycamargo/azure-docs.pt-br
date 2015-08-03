<properties
   pageTitle="Gerenciar suas VMs do Azure usando a CLI do Azure para Mac, Linux e Windows | Microsoft Azure"
   description="Descreve como criar, gerenciar e excluir suas VMs do Azure usando a CLI do Azure para Mac, Linux e Windows."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="dlepow"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/09/2015"
   ms.author="danlep"/>

# Gerenciar suas máquinas virtuais usando a CLI do Azure para Mac, Linux e Windows

Muitas tarefas realizadas diariamente para gerenciar suas VMs podem ser automatizadas usando a CLI do Azure. Este artigo fornece comandos de exemplo para tarefas mais simples e links para artigos que mostram os comandos para tarefas mais complexas.

>[AZURE.NOTE]Se ainda não instalou e configurou a CLI do Azure, você pode obter instruções [aqui](../xplat-cli-install.md). Se você quiser um início rápido para as mesmas tarefas no PowerShell, consulte [Gerenciar suas VMs usando o PowerShell do Azure](virtual-machines-manage-vms-powershell.md).

## Como Usar os Comandos de Exemplo
Você precisará substituir parte do texto nos comandos por texto apropriado para seu ambiente. Os símbolos < and > indicam o texto que você precisa substituir. Ao substituir o texto, remova os símbolos, mas mantenha as aspas.

> [AZURE.NOTE]Se você quiser armazenar e manipular a saída dos seus comandos de console de forma programática, convém usar uma ferramenta de análise JSON, como **[jq](https://github.com/stedolan/jq)**, **[jsawk](https://github.com/micha/jsawk)** ou bibliotecas de idiomas adequadas à tarefa.

## Mostrar informações sobre uma VM

Essa é uma tarefa básica que você usará com frequência. Use-a para obter informações sobre uma VM, executar tarefas em uma VM ou obter a saída para armazenar em uma variável.

Para obter informações sobre a VM, execute este comando, substituindo tudo entre aspas, inclusive os caracteres < and >:

     azure vm show -g <group name> -n <virtual machine name>

Para armazenar a saída em uma variável $vm como um documento JSON, execute:

    vmInfo=$(azure vm show -g <group name> -n <virtual machine name> --json)

ou você pode redirecionar o stdout para um arquivo.

## Fazer logon uma máquina virtual baseada em Linux

Normalmente, as máquinas Linux são conectadas por meio de SSH. Para obter mais informações, consulte [Como usar SSH com o Linux no Azure](virtual-machines-linux-use-ssh-key.md). Visão Geral do Gerenciador de Recursos do Azure
## Parar uma VM

Execute este comando:

    azure vm stop <group name> <virtual machine name>

>[AZURE.IMPORTANT]Use esse parâmetro para manter o VIP (IP virtual) do serviço de nuvem, caso essa seja a última VM no serviço de nuvem. <br><br> Se usar o parâmetro StayProvisioned, você ainda será cobrado pela VM.

## Iniciar uma VM

Execute este comando: Azure Resource Manager Overview azure vm start <group name> <virtual machine name>

## Anexar um Disco de Dados

Você também precisará decidir se deseja anexar um novo disco ou um que contenha dados. Para um novo disco, o comando cria o arquivo .vhd e anexa-o no mesmo comando.

Para anexar um novo disco, execute este comando:

     azure vm disk attach-new <resource-group> <vm-name> <size-in-gb>

Para anexar um disco de dados existente, execute este comando:

    azure vm disk attach <resource-group> <vm-name> [vhd-url]

## Criar uma VM do Linux

Para criar uma nova VM baseada no Linux, você precisará ter vários valores à mão, incluindo o nome de um grupo de recursos, um local, um nome de imagem, um nome de vm e uma conta de armazenamento para armazenar a imagem .vhd de backup. Depois que você tiver as informações que deseja usar, a CLI do Azure poderá criar uma sessão interativa para solicitar esses valores, digitando:

    azure vm create

É claro que, se já tiver esses valores, você poderá encontrar as opções apropriadas para passá-las diretamente digitando `azure help vm create`.

## Próximas etapas

Para obter mais exemplos de uso da CLI do Azure com o modo **arm**, consulte [Usando a CLI do Microsoft Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](../xplat-cli-resource-manager.md). Para saber mais sobre recursos do Azure e seus conceitos, consulte [Visão geral do Gerenciador de Recursos do Azure](../resource-group-overview.md).
 

<!---HONumber=July15_HO4-->