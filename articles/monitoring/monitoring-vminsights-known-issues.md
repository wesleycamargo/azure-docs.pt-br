---
title: O Azure Monitor para problemas conhecidos de VMs | Microsoft Docs
description: O Azure Monitor para VMs é uma solução do Azure que combina integridade e monitoramento de desempenho do sistema operacional da VM do Azure, bem como a descoberta automática dos componentes de aplicativo e dependências com outros recursos e mapeia a comunicação entre eles. Este artigo aborda os problemas conhecidos.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: 819c3e74355cf80c7a998abb8b02b10c9e077059
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47062757"
---
# <a name="known-issues-with-azure-monitor-for-vms"></a>Problemas conhecidos com o Azure Monitor para VMs

Os seguintes são problemas conhecidos com o recurso de integridade do Azure Monitor para VMs:

- O período e a frequência dos critérios de integridade não podem ser modificados com essa versão. 
- Critérios de integridade não podem ser desabilitados. 
- Após a integração, pode levar tempo até os dados aparecerem no Azure Monitor -> Máquinas Virtuais -> Integridade ou da folha de recurso da VM -> Insights
- O Diagnóstico de Integridade tem atualizações mais rápidas do que qualquer outra exibição, portanto, você pode sofrer atrasos de informações ao alternar entre modos de exibição  
- Resumo de alertas fornecido no Azure Monitor para Integridade da VM é apenas para alertas disparados para problemas de integridade detectados com VMs do Azure monitoradas.
- A página de exibição **Lista de alertas** na única VM e no Azure Monitor mostra alertas cuja condição do monitor é definida como "disparada" nos últimos 30 dias.  Não são configuráveis. No entanto, depois de clicar no resumo, quando a página de exibição **Lista de alertas** for inicializada, você poderá alterar o valor do filtro tanto da condição do monitor quanto do intervalo de tempo.
- Na página de exibição **Lista de alertas**, sugerimos que você não modifique os filtros **Tipo de recurso**, **Recurso** e **Serviço do Monitor**, pois eles foram configurados especificamente para a solução (esse modo de exibição de lista mostra alguns campos extras em comparação ao Azure Monitor -> modo de exibição de lista de Alertas).    
- Em VMs do Linux, a exibição **Diagnóstico de Integridade** tem todo o nome de domínio da VM, em vez do nome da VM definido pelo usuário.
- Desligar VMs atualizará alguns dos seus critérios de integridade para um estado crítico e outros, para um estado íntegro com o estado de rede da VM em um estado crítico.
- A gravidade do alerta de integridade não pode ser modificada, só pode ser habilitada ou desabilitada.  Além disso, algumas severidades são atualizadas com base nos critérios de integridade.
- Modificar qualquer configuração de uma instância de critério de integridade levará à modificação da mesma configuração em todas as instâncias de critérios de integridade do mesmo tipo na VM. Por exemplo, se o limite da instância de critério de integridade de espaço livre em disco correspondente a um disco lógico C: for modificado, esse limite se aplicará a todos os outros discos lógicos descobertos e monitorados para a mesma VM.   
- Os limites para alguns critérios de integridade do Windows, como a Integridade do Serviço de Cliente DNS, não são modificáveis, uma vez que seu estado íntegro já está bloqueado para o estado **em execução**, **disponíveis** do serviço ou entidade, dependendo do contexto.  Em vez disso, o valor é representado pelo número 4, ele será convertido na cadeia de caracteres de exibição real em uma versão futura.  
- Os limites para alguns critérios de integridade do Linux não são modificáveis, como a integridade do disco lógico, pois já estão definidos para disparar em estado não íntegro.  Eles indicam se algo está online/offline, ou ligado ou desligado, e são representados e indicam o mesmo mostrando o valor 1 ou 0.
- Atualizando o filtro de grupo de recursos em qualquer grupo de recursos enquanto estiver usando em escala do Azure Monitor -> Máquinas Virtuais -> Integridade -> Qualquer modo de exibição de lista com o grupo de recursos e a assinatura pré-selecionados, fará o modo de exibição de lista Mostrar **nenhum resultado**.  Volte para a guia Azure Monitor -> Máquinas Virtuais -> Integridade, selecione a assinatura e o grupo de recursos desejados, então navegue para o modo de exibição de lista.

## <a name="next-steps"></a>Próximas etapas
Examine [Integrar o Azure Monitor para VMs](monitoring-vminsights-onboard.md) para entender os requisitos e os métodos usados para habilitar o monitoramento das máquinas virtuais.