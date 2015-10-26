<properties
   pageTitle="Gerenciar máquinas virtuais com a CLI | Microsoft Azure"
   description="Automatize o gerenciamento de suas VMs do Gerenciador de Recursos do Azure usando a CLI (Interface de Linha de Comando) do Azure."
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="10/07/2015"
   ms.author="danlep"/>

# Gerenciar suas máquinas virtuais do Gerenciador de Recursos usando a CLI do Azure para Mac, Linux e Windows


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](virtual-machines-how-to-automate-azure-resource-manager.md).

Várias das tarefas que você realiza diariamente para gerenciar suas VMs podem ser automatizadas por meio da CLI (Interface de Linha de Comando) do Azure para Mac, Linux e Windows. Este artigo fornece comandos de exemplo para tarefas mais simples e links para artigos que mostram os comandos para tarefas mais complexas.

>[AZURE.NOTE]Se você ainda não instalou a CLI do Azure, é possível obter instruções [aqui](../xplat-cli-install.md) e saber como se conectar à sua assinatura [aqui](../xplat-cli-connect.md). Também é necessário configurar a CLI no modo arm (Gerenciador de Recursos do Azure).

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

* Para obter mais exemplos de uso da CLI do Azure com o modo do Gerenciador de Recursos do Azure, veja [Usando a CLI do Microsoft Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](xplat-cli-azure-resource-manager.md).

* Para saber mais sobre recursos do Azure e seus conceitos, consulte [Visão geral do Gerenciador de Recursos do Azure](../resource-group-overview.md).

<!---HONumber=Oct15_HO3-->