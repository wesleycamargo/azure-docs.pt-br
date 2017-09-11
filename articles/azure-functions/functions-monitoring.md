---
title: Monitoramento do Azure Functions | Microsoft Docs
description: Saiba como monitorar o Azure Functions.
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/03/2016
ms.author: wesmc
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 3ab6123b6acfdec57f1ca71b404c9e1123d1ff6d
ms.contentlocale: pt-br
ms.lasthandoff: 08/29/2017

---

# <a name="monitoring-azure-functions"></a>Monitoramento do Azure Functions

## <a name="overview"></a>Visão geral 


A guia **Monitorar** de cada função permite revisar cada execução de uma função.

![guia Monitorar do Azure Functions](./media/functions-monitoring/monitor-tab.png) 

Ao clicar em uma execução, é possível rever a duração, os dados de entrada, os erros e os arquivos de log associados. Tudo isso é útil na depuração e no ajuste de desempenho de suas funções.

> [!IMPORTANT]
> Ao usar o [Plano de hospedagem de consumo](functions-overview.md#pricing) do Azure Functions, o bloco **Monitoramento** no Aplicativo de Funções não mostrará dados. Isso ocorre porque a plataforma gerenciar e dimensionar dinamicamente instâncias de computação para você. Essas métricas não são significativas em um plano de consumo. Para monitorar o uso dos Aplicativos de Funções, você deve usar as orientações neste artigo.
> 
> A captura de tela a seguir mostra um exemplo:
> 
> ![Monitoramento de função](./media/functions-monitoring/app-service-overview-monitoring.png)


## <a name="real-time-monitoring"></a>Monitoramento em tempo real

Para acessar o monitoramento em tempo real, clique na **transmissão de evento ao vivo**, como mostrado abaixo. 

![Opção de transmissão de evento ao vivo para a guia monitorar](./media/functions-monitoring/monitor-tab-live-event-stream.png)

O fluxo de evento ao vivo é exibido em um gráfico em uma nova guia no navegador, como no exemplo a seguir: 

![Exemplo de transmissão de evento ao vivo](./media/functions-monitoring/live-event-stream.png)


> [!NOTE]
> Há um problema conhecido que pode causar falha na população dos dados. Caso você enfrente esse problema, talvez seja preciso fechar a guia do navegador que contém a transmissão de evento ao vivo e clicar na **transmissão de evento ao vivo** novamente para permitir que os dados da transmissão do evento sejam populados corretamente. 

A transmissão de evento ao vivo apresentará em gráfico as seguintes estatísticas para sua função:

* Execuções iniciadas por segundo
* Execuções concluídas por segundo
* Execuções com falha por segundo
* Tempo médio de execução em milissegundos.

Essas estatísticas são em tempo real, mas o gráfico real dos dados de execução pode ter aproximadamente 10 segundos de latência.


## <a name="monitoring-log-files-from-a-command-line"></a>Monitoramento de arquivos de log de uma linha de comando

Você pode transmitir arquivos de log para uma sessão de linha de comando em uma estação de trabalho local usando a CLI (Interface de Linha de Comando) 1.0 do Azure ou o PowerShell.

### <a name="monitoring-function-app-log-files-with-the-azure-cli-10"></a>Monitoramento de arquivos de log do aplicativo de funções com a CLI do Azure 1.0

Para começar, [instale a CLI do Azure 1.0](../cli-install-nodejs.md)

Entre na sua conta do Azure usando o comando a seguir ou qualquer uma das outras opções abordadas em [Entrar no Azure usando a CLI do Azure 1.0](../xplat-cli-connect.md).

    azure login

Use o comando a seguir para habilitar a CLI do Azure 1.0 no modo de gerenciamento de serviço Clássico:

    azure config mode asm

Se você tiver várias assinaturas, use os comandos a seguir para listá-las e definir a assinatura atual para aquela que contém seu aplicativo de funções.

    azure account list
    azure account set <subscriptionNameOrId>

O seguinte comando transmitirá os arquivos de log do seu aplicativo de funções para a linha de comando:

    azure site log tail -v <function app name>

### <a name="monitoring-function-app-log-files-with-powershell"></a>Monitoramento de arquivos de log do aplicativo de funções com o PowerShell

Para começar, [instale e configure o Azure PowerShell](/powershell/azure/overview).

Adicione a conta do Azure executando o seguinte comando:

    PS C:\> Add-AzureAccount

Se você tiver várias assinaturas, liste-as pelo nome com o seguinte comando para ver se a assinatura correta é a atualmente selecionada com base na propriedade `IsCurrent`:

    PS C:\> Get-AzureSubscription

Se precisar definir a assinatura ativa para aquela que contém seu aplicativo de funções, use o seguinte comando:

    PS C:\> Get-AzureSubscription -SubscriptionName "MyFunctionAppSubscription" | Select-AzureSubscription

Transmita os logs para sua sessão do PowerShell com o seguinte comando:

    PS C:\> Get-AzureWebSiteLog -Name MyFunctionApp -Tail

Para obter mais informações, consulte [Como transmitir logs para aplicativos Web](../app-service-web/web-sites-enable-diagnostic-log.md#streamlogs). 

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os recursos a seguir:

* [Testando uma função](functions-test-a-function.md)
* [Dimensionar uma função](functions-scale.md)


