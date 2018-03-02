---
title: "Hybrid Runbook Worker do Linux da Automação do Azure | Microsoft Docs"
description: "Este artigo fornece informações sobre como instalar um Hybrid Runbook Worker da Automação do Azure que permite executar runbooks em computadores Linux no seu datacenter local ou ambiente de nuvem."
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
ms.date: 02/14/2018
ms.author: magoedte
ms.openlocfilehash: 9926ceef9777032d52c7655e4c2d03f63a4a6af7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Como implantar o Hybrid Runbook Worker do Linux

Os runbooks na Automação do Azure não podem acessar recursos em outras nuvens ou no seu ambiente local, já que eles são executados na nuvem do Azure. O recurso Hybrid Runbook Worker da Automação do Azure permite executar runbooks diretamente no computador que hospeda a função e em recursos no ambiente para gerenciar esses recursos locais. Os runbooks são armazenados e gerenciados na Automação do Azure e entregues a um ou mais computadores designados.

Essa funcionalidade está ilustrada na imagem a seguir:<br>  

![Visão geral do Runbook Worker Híbrido](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Para obter uma visão geral técnica das funções do Hybrid Runbook Worker, consulte [Visão geral da arquitetura de automação](automation-offering-get-started.md#automation-architecture-overview). Examine as seguintes informações sobre [requisitos de hardware e software](automation-offering-get-started.md#hybrid-runbook-worker) e [informações para preparar sua rede](automation-offering-get-started.md#network-planning) antes de começar a implantar um Hybrid Runbook Worker. Depois de você implantar com êxito um runbook worker, leia [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para aprender a configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.     

## <a name="hybrid-runbook-worker-groups"></a>Grupos de Runbook Worker Híbrido
Cada Runbook Worker Híbrido é membro de um grupo de Runbook Worker Híbrido que você especifica ao instalar o agente. Um grupo pode conter um único agente, mas você pode instalar vários agentes em um grupo para ter alta disponibilidade.

Quando você inicia um runbook em um Runbook Worker Híbrido, deve especificar o grupo no qual ele será executado. Os membros do grupo determinarão qual trabalhador atenderá a solicitação. Você não pode especificar um trabalhador específico.

## <a name="installing-linux-hybrid-runbook-worker"></a>Instalação do Hybrid Runbook Worker do Linux
Para instalar e configurar um Hybrid Runbook Worker no seu computador Linux, você segue um processo simples para instalar e configurar manualmente a função. Requer a habilitação da solução **Hybrid Worker de Automação** em seu espaço de trabalho do OMS e, em seguida, a execução de um conjunto de comandos para registrar o computador como um trabalhador e adicioná-lo a um grupo novo ou existente. 

Antes de continuar, é necessário anotar o espaço de trabalho do Log Analytics ao qual sua conta de Automação está vinculada e também a chave primária para a conta de Automação. É possível encontrar os dois no portal selecionando sua conta de Automação do Azure, **Espaço de trabalho** para a ID do Espaço de Trabalho e **Chaves** para a chave primária.  

1.  Habilite a solução "Hybrid Worker de Automação" no OMS. Isso pode ser feito das seguintes maneiras:

   1. Na Galeria de Soluções no [Portal do OMS](https://mms.microsoft.com), habilite a solução **Hybrid Worker de Automação**
   2. Execute o cmdlet a seguir:

        ```powershell
         $null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

2.  Execute o seguinte comando, alterando os valores dos parâmetros *-w*, *-k*, *-g* e *-e*. Para o parâmetro *-g*, substitua o valor pelo nome do grupo do Hybrid Runbook Worker ao qual o novo Linux Hybrid Runbook Worker deve ingressar. Se o nome ainda não existir em sua conta de Automação do Azure, um novo grupo do Hybrid Runbook Worker é criado com esse nome.
    
    ```
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <OMSworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
    ```
3. Depois que o comando for concluído, a folha dos Grupos do Hybrid Worker no Portal do Azure mostrará o novo grupo e o número de membros ou, se for um grupo existente, o número de membros será aumentado. Você pode selecionar o grupo da lista na folha **Grupos do Hybrid Worker** e no bloco **Hybrid Workers**. Na folha **Hybrid Workers**, você verá cada membro do grupo listado.  


## <a name="turning-off-signature-validation"></a>Desativar a validação de assinatura 
Por padrão, o Linux Hybrid Runbook Workers exige validação de assinatura. Se você executar um runbook sem sinal em relação a um trabalho, um erro contendo "Falha na validação de assinatura" é exibido. Para desligar a validação de assinatura, execute o seguinte comando, substituindo o segundo parâmetro por sua ID de espaço de trabalho do OMS:

    ```
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <OMSworkspaceId>
    ```

## <a name="supported-runbook-types"></a>Tipos de runbook com suporte

O Hybrid Runbook Workers do Linux não dá suporte ao conjunto completo dos tipos de runbook que são localizados na automação do Azure.

Os seguintes tipos de runbooks funcionam em um Hybrid Worker do Linux:

* Python 2
* PowerShell
 
Os seguintes tipos de runbook não funcionam em um Hybrid Worker do Linux:

* Fluxo de Trabalho do PowerShell
* Gráfico
* Fluxo de Trabalho Gráfico do PowerShell

## <a name="next-steps"></a>Próximas etapas

* Leia [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para aprender a configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.
* Para obter instruções sobre como remover os Hybrid Runbook Workers, consulte [Remover Hybrid Runbook Workers da Automação do Azure](automation-remove-hrw.md)
