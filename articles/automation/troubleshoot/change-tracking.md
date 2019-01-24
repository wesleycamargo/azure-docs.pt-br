---
title: Solução de problemas com o Controle de Alterações do Azure
description: Este artigo fornece informações sobre a solução de problemas do Controle de Alterações
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 10/24/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 63dc7148904089a31ff95764898a8dac72c37049
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421328"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Solucionar problemas do Controle de Alterações e do Inventário

## <a name="windows"></a> Windows

### <a name="records-not-showing-windows"></a>Cenário: Os registros de Controle de Alterações não estão sendo exibidos no portal do Azure

#### <a name="issue"></a>Problema

Você não vê quaisquer resultados de Inventário ou Controle de Alterações para computadores integrados para Controle de Alterações.

#### <a name="cause"></a>Causa

Esse erro pode ser causado pelos seguintes motivos:

1. O **Microsoft Monitoring Agent** não está em execução
2. A comunicação com a Conta de Automação está sendo bloqueada.
3. Os pacotes de gerenciamento para o Controle de Alterações não foram baixados.
4. A VM que está sendo integrada pode ter vindo de um computador clonado sem ter sido preparada (sysprep) com o Microsoft Monitoring Agent instalado.

#### <a name="resolution"></a>Resolução

1. Verifique se o **Microsoft Monitoring Agent** (HealthService.exe) está em execução no computador.
2. Visite [Planejamento de rede](../automation-hybrid-runbook-worker.md#network-planning) para saber quais endereços e portas devem ter permissão para que Controle de Alterações funcione.
3. Verifique se os seguintes pacotes de gerenciamento de Controle de Alterações e de Inventário existem localmente:
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
4. Se estiver usando uma imagem clonada, primeiro faça sysprep da imagem e depois instale o agente do Microsoft Monitoring Agent.

Se essas soluções não resolverem seu problema, e você entrar em contato com o suporte, execute os comandos a seguir para coletar o diagnóstico no agente

No computador do agente, navegue até `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` e execute os seguintes comandos:

```cmd
set stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Por padrão, o rastreamento de erros está habilitado. Se você quiser habilitar mensagens de erro detalhadas, como no exemplo anterior, use o parâmetro `VER`. Para rastreamentos de informações, use `INF` ao invocar `StartTracing.cmd`.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
