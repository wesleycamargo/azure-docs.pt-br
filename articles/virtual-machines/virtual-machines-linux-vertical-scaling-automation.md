---
title: Dimensionar verticalmente uma máquina virtual do Azure com a Automação do Azure | Microsoft Docs
description: Como dimensionar verticalmente uma Máquina Virtual do Linux em resposta a alertas de monitoramento com a Automação do Azure
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/29/2016
ms.author: singhkay

---
# Dimensionar verticalmente uma máquina virtual do Azure com a Automação do Azure
A escala vertical é o processo de aumentar ou diminuir os recursos de um computador em resposta à carga de trabalho. No Azure, isso pode ser feito alterando o tamanho da máquina virtual. Isso pode ajudar nos cenários a seguir

* Se a máquina virtual não está sendo usado com frequência, você pode redimensioná-la para um tamanho menor, a fim de reduzir os custos mensais
* Se a máquina virtual está enfrentando um pico de carga, ela pode ser redimensionada para um tamanho maior, a fim de aumentar sua capacidade

Veja abaixo a descrição das etapas para fazer isso

1. Configurar a Automação do Azure para acessar suas máquinas virtuais
2. Importar os runbooks de Escala Vertical da Automação do Azure para sua assinatura
3. Adicionar um webhook ao seu Runbook
4. Adicionar um alerta à sua máquina virtual

> [!NOTE]
> Devido ao tamanho da primeira máquina virtual, os tamanhos para expansão podem ser limitados devido à disponibilidade de outros tamanhos no cluster em que a máquina virtual atual está implantada. Nos Runbooks de automação publicados usados neste artigo, levamos isso em conta e dimensionamos apenas dentro dos pares de tamanhos da VM abaixo. Isso significa que uma máquina virtual Standard\_D1v2 não será expandida repentinamente para Standard\_G5 ou reduzida para Basic\_A0.
> 
> | Par de dimensionamento de tamanhos de VM |  |
> | --- | --- |
> | Basic\_A0 |Basic\_A4 |
> | Standard\_A0 |Standard\_A4 |
> | Standard\_A5 |Standard\_A7 |
> | Standard\_A8 |Standard\_A9 |
> | Standard\_A10 |Standard\_A11 |
> | Standard\_D1 |Standard\_D4 |
> | Standard\_D11 |Standard\_D14 |
> | Standard\_DS1 |Standard\_DS4 |
> | Standard\_DS11 |Standard\_DS14 |
> | Standard\_D1v2 |Standard\_D5v2 |
> | Standard\_D11v2 |Standard\_D14v2 |
> | Standard\_G1 |Standard\_G5 |
> | Standard\_GS1 |Standard\_GS5 |
> 
> 

## Configurar a Automação do Azure para acessar suas máquinas virtuais
A primeira coisa que você precisa fazer é criar uma conta de Automação do Azure que hospedará os Runbooks usados para dimensionar as instâncias do Conjunto de Escala de VM. O serviço de Automação introduziu recentemente o recurso "Executar como conta" que facilita a configuração da Entidade de Serviço para execução automática de Runbooks em nome do usuário. Leia mais sobre isso no artigo abaixo:

* [Autenticar runbooks com uma conta Executar como do Azure](../automation/automation-sec-configure-azure-runas-account.md)

## Importar os runbooks de Escala Vertical da Automação do Azure para sua assinatura
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

<!---HONumber=AcomDC_0824_2016-->