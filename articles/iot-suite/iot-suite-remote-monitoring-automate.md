---
title: "Detectar problemas de dispositivos na solução de monitoramento remoto – Azure | Microsoft Docs"
description: "Este tutorial mostra como usar regras e ações para detectar automaticamente os problemas do dispositivo relacionados ao limite na solução de monitoramento remoto."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 11/10/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 173ffbdd70313ef5a0d2af2cf1c8996d2395274a
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2017
---
# <a name="detect-issues-using-threshold-based-rules"></a>Detectar problemas usando regras baseadas em limites

Este tutorial mostra os recursos do mecanismo de regras na solução de monitoramento remoto. Para apresentar esses recursos, o tutorial usa um cenário no aplicativo Contoso IoT.

A Contoso tem uma regra que gera um alerta crítico quando a pressão informada por um dispositivo **Resfriador** excede 250 PSI. Como um operador, pode ser útil identificar os dispositivos **Resfriadores** que podem apresentar problemas nos sensores buscando por picos de pressão. Para identificar esses dispositivos, crie uma regra para gerar um aviso quando a pressão exceder 150 PSI.

Neste tutorial, você aprenderá como:

>[!div class="checklist"]
> * Exibir as regras na solução
> * Criar uma nova regra
> * Editar uma regra existente
> * Excluir uma regra

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, você precisará de uma instância implantada de solução de monitoramento remoto na sua assinatura do Azure.

Se você ainda não implantou a solução de monitoramento remoto, conclua o tutorial [Deploy the remote monitoring preconfigured solution](iot-suite-remote-monitoring-deploy.md) (Implantar a solução de monitoramento remoto pré-configurada).

## <a name="view-the-rules-in-your-solution"></a>Exibir as regras na solução

A página **Regras e ações** na solução exibe uma lista de todas as regras atuais:

![Página Regras e ações](media/iot-suite-remote-monitoring-automate/rulesactions.png)

Para exibir somente as regras que se aplicam aos dispositivos **Resfriadores**, aplique um filtro:

![Filtrar a lista de regras](media/iot-suite-remote-monitoring-automate/rulesactionsfilter.png)

É possível exibir mais informações sobre uma regra e editá-la ao selecioná-la na lista:

![Exibir detalhes das regras](media/iot-suite-remote-monitoring-automate/rulesactionsdetail.png)

Para desabilitar, habilitar ou excluir uma ou mais regras, selecione várias regras na lista:

![Selecionar várias regras](media/iot-suite-remote-monitoring-automate/rulesactionsmultiselect.png)

## <a name="create-a-new-rule"></a>Criar uma nova regra

Para adicionar uma nova regra que gera um aviso quando a pressão em um dispositivo **Resfriador** exceder 150 PSI, escolha **Nova regra**:

![Criar regra](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule.png)

Use os seguintes valores para criar a regra:

| Configuração          | Valor                                 |
| ---------------- | ------------------------------------- |
| Nome             | Aviso de resfriador                       |
| Fonte           | Grupo de dispositivos **Resfriador**              |
| Campo de gatilho    | Pressão                              |
| Operador de gatilho | Maior que                          |
| Valor do gatilho    | 150                                   |
| Nível de severidade   | Aviso                               |
| Texto do evento de alarme | A pressão do resfriador excedeu PSI 150 |

Para salvar a nova regra, escolha **Aplicar**.

É possível exibir quando a regra é disparada na página **Regras e ações** ou na página **Painel**.

## <a name="edit-an-existing-rule"></a>Editar uma regra existente

Para fazer uma alteração em uma regra existente, selecione-a na lista de regras. Em seguida, no painel **Detalhes da regra**, escolha **Modo de edição**.

![Editar regra](media/iot-suite-remote-monitoring-automate/rulesactionsedit.png)

## <a name="disable-a-rule"></a>Desabilitar uma regra

Para desativar temporariamente uma regra, desabilite-a na lista de regras. Escolha a regra a ser desabilitada e selecione **Desabilitar**. O **Status** da regra na lista é alterado para indicar que a regra agora está desabilitada. Use o mesmo procedimento para reabilitar uma regra que foi desabilitada.

![Desabilitar regra](media/iot-suite-remote-monitoring-automate/rulesactionsdisable.png)

É possível habilitar e desabilitar várias regras ao mesmo tempo selecionando diversas regras na lista.

## <a name="delete-a-rule"></a>Excluir uma regra

Para excluir uma regra permanentemente, escolha-a na lista de regras e selecione **Excluir**.

É possível excluir várias regras ao mesmo tempo selecionando diversas regras na lista.

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Exibir as regras na solução
> * Criar uma nova regra
> * Editar uma regra existente
> * Excluir uma regra

Agora que você aprendeu a detectar problemas usando regras baseadas em limite, as próximas etapas sugeridas são aprender como:

* [Gerenciar e configurar seus dispositivos](./iot-suite-remote-monitoring-manage.md).
* [Solucionar e corrigir problemas de dispositivo](./iot-suite-remote-monitoring-maintain.md).
* [Testar sua solução com dispositivos simulados](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->