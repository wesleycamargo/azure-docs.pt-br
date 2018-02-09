---
title: "Remover Hybrid Runbook Workers da Automação do Azure | Microsoft Docs"
description: "Este artigo fornece informações sobre como gerenciar Hybrid Runbook Workers implantados na Automação do Azure, que permite executar runbooks em computadores no seu datacenter local ou ambiente de nuvem."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: 602b868073da6bd1f64099c0f344c9b492abaff0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="remove-azure-automation-hybrid-runbook-workers"></a>Remover Hybrid Runbook Workers da Automação do Azure

O recurso Hybrid Runbook Worker da Automação do Azure permite executar runbooks diretamente no computador que hospeda a função e em recursos no ambiente para gerenciar esses recursos locais. Este artigo o orienta durante as etapas para remover um trabalhador híbrido de um computador local.

## <a name="removing-hybrid-runbook-worker"></a>Removendo o Hybrid Runbook Worker

Você pode remover um ou mais Trabalhadores de Runbook Híbridos de um grupo ou remover o grupo, dependendo dos seus requisitos.  Para remover um Hybrid Runbook Worker de um computador local, execute as etapas a seguir.

1. No portal do Azure, abra sua Conta de Automação.  
2. Na folha **Configurações**, selecione **Teclas** e anote os valores para o campo **URL** e **Tecla de Acesso Primária**.  Você precisará dessas informações para a próxima etapa.
3. Abra uma sessão do PowerShell no modo de Administrador e execute o comando a seguir ‑ `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Use a opção **-Verbose** para obter um log detalhado do processo de remoção.

> [!NOTE]
> Isso não remove o Microsoft Monitoring Agent do computador, apenas a funcionalidade e a configuração da função do Hybrid Runbook Worker.  

## <a name="remove-hybrid-worker-groups"></a>Remover grupos do Hybrid Worker

Para remover um grupo, primeiro você precisa remover o Hybrid Runbook Worker de cada computador membro do grupo usando o procedimento mostrado anteriormente e, em seguida, executar as seguintes etapas para remover o grupo.  

1. Abra a conta de Automação no Portal do Azure.
1. Em **Automação de Processo**, selecione **Grupos de Trabalho Híbrido**. Selecione o grupo que você deseja excluir.  Depois de selecionar o grupo específico, a folha de propriedades do **grupo Hybrid Worker** é exibida.<br> ![Folha do grupo Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
2. Na folha de propriedades do grupo selecionado, clique em **Excluir**.  Uma mensagem será exibida solicitando que você confirme a ação. Selecione **Sim** se tiver certeza de que deseja continuar.<br> ![Caixa de diálogo de confirmação de exclusão de grupo](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> Esse processo pode levar vários segundos e você pode acompanhar o progresso no menu **Notificações**. 

## <a name="next-steps"></a>Próximas etapas

* Leia [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para aprender a configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.
* Para obter informações sobre como instalar Hybrid Runbook Workers no Windows, consulte [Hybrid Runbook Worker Windows da Automação do Azure](automation-windows-hrw-install.md).
* Para obter informações sobre como instalar Hybrid Runbook Workers no Linux, consulte [Hybrid Runbook Worker Linux da Automação do Azure](automation-linux-hrw-install.md).