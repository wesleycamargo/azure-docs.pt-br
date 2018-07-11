---
title: Detectar problemas de dispositivos na solução de monitoramento remoto baseada em Azure | Microsoft Docs
description: Este tutorial mostra como usar regras e ações para detectar automaticamente os problemas do dispositivo relacionados ao limite na solução de Monitoramento Remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 1e3eaeec1d2eae3c36f285a3e4c536657504cbb8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098474"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>Tutorial: Detectar problemas com dispositivos conectados à sua solução de monitoramento

Neste tutorial, você irá configurar o acelerador de solução de Monitoramento Remoto para detectar problemas com seus dispositivos de IoT conectados. Para detectar os problemas com seus dispositivos, você deve adicionar as regras que geram alertas no painel da solução.

Para apresentar as regras e alertas, o tutorial usa um dispositivo resfriador simulado. O resfriador é gerenciado por uma organização chamada Contoso e é conectado ao acelerador de solução de Monitoramento Remoto. A Contoso tem uma regra que gera um alerta crítico quando a pressão em um resfriador excede 298 PSI. Como um operador na Contoso, pode ser útil identificar os dispositivos resfriadores que podem apresentar problemas nos sensores buscando por picos de pressão. Para identificar esses dispositivos, você deve adicionar uma regra que gera um alerta de aviso quando a pressão no resfriador excede 150 PSI.

Você também foi solicitado para criar um alerta crítico para um resfriador quando, nos últimos cinco minutos, a umidade média no dispositivo era maior que 80% e a temperatura do dispositivo era maior que 75 graus Fahrenheit.

Neste tutorial, você irá:

>[!div class="checklist"]
> * Exibir as regras na solução
> * Criar uma regra
> * Criar uma regra com várias condições
> * Editar uma regra existente
> * Ativar e desativar regras

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, você precisará de uma instância implantada do acelerador de solução de Monitoramento Remoto em sua assinatura do Azure.

Se você ainda não implantou o acelerador de solução de Monitoramento Remoto, conclua o início rápido [Implantar uma solução de monitoramento remoto baseado em nuvem](quickstart-remote-monitoring-deploy.md).

## <a name="view-the-existing-rules"></a>Exibir as regras existentes

A página **Regras** no acelerador de solução exibe uma lista de todas as regras atuais:

[![Página de regras](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

Para exibir somente as regras que se aplicam aos dispositivos resfriadores, aplique um filtro:

[![Filtrar a lista de regras](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-expanded.png#lightbox)

É possível exibir mais informações sobre uma regra e editá-la ao selecioná-la na lista:

[![Exibir detalhes das regras](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

Para desabilitar ou habilitar uma ou mais regras, selecione uma ou mais regras na lista:

[![Selecionar várias regras](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>Criar uma regra

Para criar uma regra que gera um aviso quando a pressão em um dispositivo resfriador exceder 150 PSI, clique em **Nova regra**. Use os seguintes valores para criar a regra:

| Configuração          | Valor                                 |
| ---------------- | ------------------------------------- |
| Nome da regra        | Aviso de resfriador                       |
| Descrição      | A pressão do resfriador excedeu PSI 150 |
| Grupo de dispositivos     | Grupo de dispositivos dos **Resfriadores**             |
| Cálculo      | Instantâneo                               |
| Campo Condição 1| pressão                              |
| Operador de Condição 1 | Maior que                      |
| Valor de Condição 1    | 150                               |
| Nível de severidade  | Aviso                               |

[![Criar regra de aviso](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-expanded.png#lightbox)

Para salvar a nova regra, clique em **Aplicar**.

É possível ver quando a regra é disparada na página **Regras** ou na página **Painel**:

[![Regra de aviso disparada](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-expanded.png#lightbox)

## <a name="create-a-rule-with-multiple-conditions"></a>Criar uma regra com várias condições

Para criar uma regra com várias condições que gere um alerta crítico quando, nos últimos cinco minutos para um dispositivo resfriador, a umidade média for maior do que 80% e a temperatura média for maior do que 75 graus Fahrenheit, clique em **Nova regra**. Use os seguintes valores para criar a regra:

| Configuração          | Valor                                 |
| ---------------- | ------------------------------------- |
| Nome da regra        | Umidade e temperatura críticas do resfriador    |
| Descrição      | Umidade e temperatura são essenciais |
| Grupo de dispositivos     | Grupo de dispositivos dos **Resfriadores**             |
| Cálculo      | Média                               |
| Período de tempo      | 5                                     |
| Campo Condição 1| umidade                              |
| Operador de Condição 1 | Maior que                      |
| Valor de Condição 1    | 80                                |
| Nível de severidade  | Crítico                              |

[![Criar regra de várias condições parte um](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-expanded.png#lightbox)

Para adicionar a segunda condição, clique em “+ Adicionar condição”. Use os seguintes valores para a nova condição:

| Configuração          | Valor                                 |
| ---------------- | ------------------------------------- |
| Campo Condição 2| temperatura                           |
| Operador de Condição 2 | Maior que                      |
| Valor de Condição 2    | 75                                |

[![Criar regra de várias condições parte dois](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-expanded.png#lightbox)

Para salvar a nova regra, clique em **Aplicar**.

É possível ver quando a regra é disparada na página **Regras** ou na página **Painel**:

[![Regra de várias condições disparada](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-expanded.png#lightbox)

## <a name="edit-an-existing-rule"></a>Editar uma regra existente

Para fazer uma alteração em uma regra existente, selecione-a na lista de regras e clique em **Editar**:

[![Editar regra](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-expanded.png#lightbox)

## <a name="disable-a-rule"></a>Desabilitar uma regra

Para desativar temporariamente uma regra, desabilite-a na lista de regras. Escolha a regra a ser desabilitada e selecione **Desabilitar**. O **Status** da regra na lista é alterado para indicar que a regra agora está desabilitada. Use o mesmo procedimento para reabilitar uma regra que foi desabilitada.

[![Desabilitar regra](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

É possível habilitar e desabilitar várias regras ao mesmo tempo selecionando diversas regras na lista.

<!-- ## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja passar para o próximo tutorial, deixe o acelerador de solução de Monitoramento Remoto implantado. Para reduzir os custos de executar o acelerador de solução, enquanto ele não estiver sendo usado, você pode interromper os dispositivos simulados no painel de configurações:

[![Pausar telemetria](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-expanded.png#lightbox)

Quando você estiver pronto para iniciar o próximo tutorial, você pode reiniciar os dispositivos simulados.

Se você não precisar mais do acelerador de solução, exclua-o da página [Soluções provisionadas](https://www.azureiotsolutions.com/Accelerators#dashboard):

![Excluir solução](media/iot-accelerators-remote-monitoring-automate/deletesolution.png)

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como usar a página **Regras** no acelerador de solução de Monitoramento Remoto para criar e gerenciar regras que disparam alertas na solução. Para saber como usar o acelerador de solução para gerenciar e configurar seus dispositivos conectados, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Configurar e gerenciar dispositivos conectados à sua solução de monitoramento](iot-accelerators-remote-monitoring-manage.md)