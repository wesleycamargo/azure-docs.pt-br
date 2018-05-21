---
title: Detectar problemas de dispositivos na solução de monitoramento remoto – Azure | Microsoft Docs
description: Este tutorial mostra como usar regras e ações para detectar automaticamente os problemas do dispositivo relacionados ao limite na solução de monitoramento remoto.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: f3583b27b2fb9959e65a9c66a75c1174ebf3e238
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="detect-issues-using-threshold-based-rules"></a>Detectar problemas usando regras baseadas em limites

Este tutorial mostra os recursos do mecanismo de regras na solução de monitoramento remoto. Para apresentar esses recursos, o tutorial usa um cenário no aplicativo Contoso IoT.

A Contoso tem uma regra que gera um alerta crítico quando a pressão informada por um dispositivo **Resfriador** excede 250 PSI. Como um operador, pode ser útil identificar os dispositivos **Resfriadores** que podem apresentar problemas nos sensores buscando por picos de pressão. Para identificar esses dispositivos, crie uma regra para gerar um aviso quando a pressão exceder 150 PSI.

Você também foi informado de que um alerta crítico deve ser disparado quando a umidade média do dispositivo **Resfriador** nos últimos 5 minutos for maior do que 80% e a temperatura do dispositivo **Resfriador** nos últimos 5 minutos for maior do que 75 graus Fahrenheit.

Neste tutorial, você aprenderá como:

>[!div class="checklist"]
> * Exibir as regras na solução
> * Criar uma nova regra
> * Criar uma nova regra com várias condições
> * Editar uma regra existente
> * Excluir uma regra

## <a name="prerequisites"></a>pré-requisitos

Para seguir este tutorial, você precisará de uma instância implantada de solução de monitoramento remoto na sua assinatura do Azure.

Se você ainda não implantou a solução de monitoramento remoto, conclua o tutorial [Implantar o acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-deploy.md).

## <a name="view-the-rules-in-your-solution"></a>Exibir as regras na solução

A página **Regras** na solução exibe uma lista de todas as regras atuais:

![Página Regras e ações](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2.png)

Para exibir somente as regras que se aplicam aos dispositivos **Resfriadores**, aplique um filtro:

![Filtrar a lista de regras](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2.png)

É possível exibir mais informações sobre uma regra e editá-la ao selecioná-la na lista:

![Exibir detalhes das regras](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2.png)

Para desabilitar, habilitar ou excluir uma ou mais regras, selecione várias regras na lista:

![Selecionar várias regras](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2.png)

## <a name="create-a-new-rule"></a>Criar uma nova regra

Para adicionar uma nova regra que gera um aviso quando a pressão em um dispositivo **Resfriador** exceder 150 PSI, escolha **Nova regra**:

![Criar regra](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2.png)

Use os seguintes valores para criar a regra:

| Configuração          | Valor                                 |
| ---------------- | ------------------------------------- |
| Nome da regra        | Aviso de resfriador                       |
| DESCRIÇÃO      | A pressão do resfriador excedeu PSI 150 |
| Grupo de dispositivos     | Grupo de dispositivos dos **Resfriadores**             |
| Cálculo      | Instantâneo                               |
| Campo Condição 1| pressure                              |
| Operador de Condição 1 | Maior que                      |
| Valor de Condição 1    | 150                               |
| Nível de gravidade  | Aviso                               |

Para salvar a nova regra, escolha **Aplicar**.

É possível exibir quando a regra é disparada na página **Regras** ou na página **Painel**.

## <a name="create-a-new-rule-with-multiple-conditions"></a>Criar uma nova regra com várias condições

Para criar uma nova regra com várias condições que gere um alerta crítico quando a umidade média do dispositivo **Resfriador** nos últimos 5 minutos for maior do que 80% e a temperatura do dispositivo **Resfriador** nos últimos 5 minutos for maior do que 75 graus Fahrenheit, escolha **Nova regra**:

![Criar várias regras](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2.png)

Use os seguintes valores para criar a regra:

| Configuração          | Valor                                 |
| ---------------- | ------------------------------------- |
| Nome da regra        | Umidade e temperatura críticas do resfriador    |
| DESCRIÇÃO      | Umidade e temperatura são essenciais |
| Grupo de dispositivos     | Grupo de dispositivos dos **Resfriadores**             |
| Cálculo      | Média                               |
| Período de tempo      | 5                                     |
| Campo Condição 1| umidade                              |
| Operador de Condição 1 | Maior que                      |
| Valor de Condição 1    | 80                               |
| Nível de gravidade  | Crítico                              |

Para adicionar a segunda condição, clique em "+ Adicionar condição".

![Criar Condição 2](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2.png)

Use os seguintes valores na nova condição:

| Configuração          | Valor                                 |
| ---------------- | ------------------------------------- |
| Campo Condição 2| temperatura                           |
| Operador de Condição 2 | Maior que                      |
| Valor de Condição 2    | 75                                |

Para salvar a nova regra, escolha **Aplicar**.

É possível exibir quando a regra é disparada na página **Regras** ou na página **Painel**.

## <a name="edit-an-existing-rule"></a>Editar uma regra existente

Para fazer uma alteração em uma regra existente, selecione-a na lista de regras.

![Editar regra](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2.png)

<!--## Disable a rule

To temporarily switch off a rule, you can disable it in the list of rules. Choose the rule to disable, and then choose **Disable**. The **Status** of the rule in the list changes to indicate the rule is now disabled. You can re-enable a rule that you previously disabled using the same procedure.

![Disable rule](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable.png)

You can enable and disable multiple rules at the same time if you select multiple rules in the list.-->

<!--## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Exibir as regras na solução
> * Criar uma nova regra
> * Editar uma regra existente
> * Excluir uma regra

Agora que você aprendeu a detectar problemas usando regras baseadas em limite, as próximas etapas sugeridas são aprender como:

* [Gerenciar e configurar seus dispositivos](iot-accelerators-remote-monitoring-manage.md).
* [Solucionar e corrigir problemas de dispositivo](iot-accelerators-remote-monitoring-maintain.md).
* [Testar sua solução com dispositivos simulados](iot-accelerators-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->