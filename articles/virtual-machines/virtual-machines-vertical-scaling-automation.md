<properties
	pageTitle="Dimensionar verticalmente uma máquina virtual do Azure com a Automação do Azure | Microsoft Azure"
	description="Como dimensionar verticalmente uma máquina virtual em resposta a alertas de monitoramento com a Automação do Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/29/2016"
	ms.author="singhkay"/>

# Dimensionar verticalmente uma máquina virtual do Azure com a Automação do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico

A escala vertical é o processo de aumentar ou diminuir os recursos de um computador em resposta à carga de trabalho. No Azure, isso pode ser feito alterando o tamanho da máquina virtual. Isso pode ajudar nos cenários a seguir

- Se a máquina virtual não está sendo usado com frequência, você pode redimensioná-la para um tamanho menor, a fim de reduzir os custos mensais
- Se a máquina virtual está enfrentando um pico de carga, ela pode ser redimensionada para um tamanho maior, a fim de aumentar sua capacidade

Veja abaixo a descrição das etapas para fazer isso

1. Configurar a Automação do Azure para acessar suas máquinas virtuais
2. Importar os Runbooks de Escala Vertical da Automação do Azure para sua assinatura
3. Adicionar um webhook ao seu Runbook
4. Adicionar um alerta à sua máquina virtual

> [AZURE.NOTE] Devido ao tamanho da primeira máquina virtual, os tamanhos para expansão podem ser limitados devido à disponibilidade de outros tamanhos no cluster em que a máquina virtual atual está implantada. Nos Runbooks de automação publicados usados neste artigo, levamos isso em conta e dimensionamos apenas dentro dos pares de tamanhos da VM abaixo. Isso significa que uma máquina virtual Standard\_D1v2 não será expandida repentinamente para Standard\_G5 ou reduzida para Basic\_A0.

>| Par de dimensionamento de tamanhos de VM | |
|---|---|
| Basic\_A0 | Basic\_A4 |
| Standard\_A0 | Standard\_A4 |
| Standard\_A5 | Standard\_A7 |
| Standard\_A8 | Standard\_A9 |
| Standard\_A10 | Standard\_A11 |
| Standard\_D1 | Standard\_D4 |
| Standard\_D11 | Standard\_D14 |
| Standard\_DS1 | Standard\_DS4 |
| Standard\_DS11 | Standard\_DS14 |
| Standard\_D1v2 | Standard\_D5v2 |
| Standard\_D11v2 | Standard\_D14v2 |
| Standard\_G1 | Standard\_G5 |
| Standard\_GS1 | Standard\_GS5 |

## Configurar a Automação do Azure para acessar suas máquinas virtuais

Nesta seção, você realizará as seguintes tarefas:

* Criar um usuário no Active Directory
* Criar um AutomationPSCredential com informações de logon do usuário
* Configurar o usuário para acessar os recursos em sua assinatura

Antes de poder executar os Runbooks de Automação do Azure em sua assinatura, você precisa dar à Automação do Azure acesso à sua assinatura. Isso é feito criando outro usuário no Active Directory. Em seguida, você precisa criar um AutomationPSCredential que permita ao usuário se autenticar no Azure e executar comandos do PowerShell que redimensionarão sua máquina virtual.

Você pode ler um passo a passo de criação de um usuário e um AutomationPSCredential no seguinte artigo:

* [Configurando a Automação do Azure](../automation/automation-configuring.md)

Após criar um usuário, você precisará tornar esse usuário um coadministrador de seus recursos clássicos e dar a ele uma função de "Proprietário" para os recursos do Azure Resource Manager.

O usuário precisa ser adicionado como coadministrador no portal clássico para poder acessar os recursos clássicos.

![Coadministrador do portal antigo](./media/virtual-machines-vertical-scaling-automation/old-portal-automation-user.png)

Você precisará usar o Portal do Azure para permitir que o usuário acesse as máquinas virtuais do Azure Resource Manager.

![Administrador do portal novo](./media/virtual-machines-vertical-scaling-automation/new-portal-automation-user.png)

## Importar os Runbooks de Escala Vertical da Automação do Azure para sua assinatura

Os Runbooks necessários para dimensionar verticalmente a sua máquina virtual já estão publicados na Galeria de Runbook da Automação do Azure. Você precisará importá-los para sua assinatura. Saiba como importar Runbooks lendo o seguinte artigo:

* [Galerias de runbook e de módulos para a Automação do Azure](../automation/automation-runbook-gallery.md)

Os Runbooks que precisam ser importados são mostrados na imagem abaixo

![Importar Runbooks](./media/virtual-machines-vertical-scaling-automation/scale-runbooks.png)

## Adicionar um webhook ao seu Runbook

Depois de ter importado os Runbooks, você precisará adicionar um webhook ao Runbook para que ele possa ser disparado por um alerta de uma máquina virtual. Os detalhes da criação de um webhook para seu Runbook podem ser lidos aqui

* [Webhooks da Automação do Azure](../automation/automation-webhooks.md)

Copie o webhook antes de fechar a caixa de diálogo do webhook, pois você precisará disso na próxima seção.

## Adicionar um alerta à sua máquina virtual

1. Selecione Configurações da Máquina Virtual
2. Selecione “Regras de alerta”
3. Selecione “Adicionar alerta”
4. Selecione uma métrica na qual acionar o alerta
5. Selecione uma condição que, quando cumprida, fará com que o alerta seja acionado
6. Selecione um limite para a condição na Etapa 5 ser cumprida
7. Selecione um período no qual o serviço de monitoramento verificará a condição e o limite nas Etapas 5 e 6
8. Cole o webhook que você copiou da seção anterior.

![Adicionar alerta à máquina virtual 1](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-1.png)

![Adicionar alerta à máquina virtual 2](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-2.png)

<!---HONumber=AcomDC_0330_2016-->