---
title: "Introdução ao dimensionamento automático no Azure | Microsoft Docs"
description: Saiba como dimensionar seu recurso no Azure.
author: rajram
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: rajram
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 4faccae708b5407d1eb64e746824a23688bc5834
ms.contentlocale: pt-br
ms.lasthandoff: 05/25/2017


---
# <a name="get-started-with-auto-scale-in-azure"></a>Introdução ao dimensionamento automático no Azure
Este artigo descreve como configurar o dimensionamento automático para seu recurso no Portal do Azure.

O dimensionamento automático do Azure Monitor se aplica somente aos VMSS (Conjuntos de Dimensionamento da Máquina Virtual), aos serviços de nuvem, aos planos do Serviço de Aplicativo e aos ambientes do Serviço de Aplicativo. 

# <a name="lets-get-started"></a>Vamos começar

## <a name="discover-the-auto-scale-settings-in-your-subscriptions"></a>Descobrir as configurações de dimensionamento automático na sua assinatura
Você pode descobrir todos os recursos a que o dimensionamento automático se aplica no Azure Monitor. Siga as etapas abaixo para ter uma explicação passo a passo.

- Abra o [portal do Azure][1]
- Clique no ícone do Azure Monitor no painel de navegação à esquerda.
  ![Inicie o Azure Monitor][2]
- Clique na configuração do dimensionamento automático para exibir todos os recursos a que ele se aplica, bem como o status de dimensionamento automático atual ![Descobrir o dimensionamento automático no Azure Monitor][3]

Você pode usar o painel de filtro na parte superior para reduzir o escopo da lista e selecionar recursos em um grupo de recursos específico, selecionar os tipos de recursos específicos ou selecionar um recurso específico.

Para cada recurso, você encontrará a contagem de instâncias atual, bem como seu status de dimensionamento automático. O status de dimensionamento automático pode ser

- Não configurado: você ainda não habilitou a configuração de dimensionamento automático para este recurso
- Habilitado: você habilitou a configuração de dimensionamento automático para este recurso
- Desabilitado: você desabilitou a configuração de dimensionamento automático para este recurso

## <a name="create-your-first-auto-scale-setting"></a>Crie sua primeira configuração de dimensionamento automático

Agora, vamos percorrer um passo a passo simples para criar sua primeira configuração de dimensionamento automático.

- Abra a folha “Dimensionamento Automático” no Azure Monitor e selecione um recurso que deseja dimensionar. (as etapas a seguir usam um plano do serviço de aplicativo associado a um aplicativo Web. Você pode [criar seu primeiro aplicativo Web ASP.NET no Azure em cinco minutos][4])
- Na folha de configuração de dimensionamento para o recurso, observe que a contagem de instâncias atual é 1. Clique em “Habilitar dimensionamento automático”.
  ![Configuração de dimensionamento para um novo aplicativo Web][5]
- Forneça um nome para a configuração de dimensionamento e clique em "Adicionar uma regra". Observe as opções de regra de dimensionamento que são abertas como um painel de contexto no lado direito. Por padrão, ele define a opção de dimensionar sua contagem de instâncias em 1 se o percentual de CPU do recurso ultrapassar 70%. Deixe-o com seus valores padrão e clique em Adicionar.
  ![Criar configuração dimensionamento para um aplicativo Web][6]
- Agora, você criou sua primeira regra de dimensionamento. Observe que o UX indica as práticas recomendadas e afirma que "É recomendável ter pelo menos uma escala na regra." Para fazer isso, clique em "Adicionar uma regra" e defina "Operador" como "Menor que", "Limite" como "20" e "Operação" como "Reduzir contagem em". Agora, você deve ter uma configuração de dimensionamento que expande/reduz com base no uso da CPU.
  ![Dimensionamento com base na CPU][8]
- Clique em "Salvar"

Parabéns. Você criou com êxito sua primeira configuração de dimensionamento para fazer o dimensionamento automático de seu aplicativo Web com base no uso da CPU.

> Observação: as mesmas etapas são aplicáveis para começar a usar uma função de serviço de nuvem ou VMSS.

# <a name="other-considerations"></a>Outras considerações
## <a name="scale-based-on-a-schedule"></a>Dimensionamento com base em um planejamento
Além de dimensionar com base na CPU, você também pode definir seu dimensionamento de forma diferente em dias específicos da semana.

- Clique em "Adicionar uma condição de dimensionamento"
- A configuração do modo e das regras de dimensionamento é a mesma que a da condição padrão
- Selecione "Repetir dias específicos" para o agendamento
- Selecione os dias e a hora de início/término em que a condição de dimensionamento deve ser aplicada nos dias selecionados

![Condição de dimensionamento com base no agendamento][9]
## <a name="scale-differently-on-specific-dates"></a>Dimensionar de forma diferente em datas específicas
Além de dimensionar com base na CPU, você também pode definir seu dimensionamento de forma diferente em datas específicas.

- Clique em "Adicionar uma condição de dimensionamento"
- A configuração do modo e das regras de dimensionamento é a mesma que a da condição padrão
- Selecione "Especificar datas de início/término" para o agendamento
- Selecione as datas de início/término, bem como a hora de início/término em que a condição de dimensionamento deve ser aplicada nas datas selecionadas

![Condição de dimensionamento com base em datas][10]

## <a name="view-the-scale-history-of-your-resource"></a>Exibir o histórico de dimensionamento de seu recurso
Sempre que o recurso é expandido/reduzido, um evento é registrado no log de atividades. Você pode exibir o histórico de dimensionamento do seu recurso nas últimas 24 horas acessando a guia "Histórico de execução".

![Histórico da execução][11]

Se quiser exibir o histórico completo de dimensionamento (de até 90 dias), você poderá clicar em "Clique aqui para ver mais detalhes". Isso inicie o log de atividades com o recurso e a categoria como "dimensionamento automático" selecionada.

## <a name="view-the-scale-definition-of-the-resource"></a>Exibir a definição de dimensionamento do recurso
A configuração de dimensionamento é um recurso do ARM. Você pode exibir a definição de dimensionamento no JSON acessando a guia "JSON".

![Definição de dimensionamento][12]

Você pode fazer alterações no JSON diretamente, se necessário. Essas alterações serão refletidas ao Salvar.

## <a name="disable-autoscale-and-manually-scale-your-instances"></a>Desabilitar o dimensionamento automático e dimensionar suas instâncias manualmente
Pode haver momentos em que você queira desabilitar sua configuração de dimensionamento atual e dimensionar manualmente seu recurso.

Clique no botão 'Desabilitar dimensionamento automático' na parte superior.
![Desabilitar dimensionamento automático][13]

Observe que esta opção desabilita a configuração e você ainda pode voltar a ela após habilitar o dimensionamento automático novamente. Agora, você pode definir o número de instâncias para o qual deseja dimensionar manualmente.

![Definir dimensionamento manual][14]

Você sempre pode voltar ao dimensionamento automático clicando em "Habilitar dimensionamento automático" e "Salvar".

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet
[5]: ./media/monitoring-autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/monitoring-autoscale-get-started/scale-in-recommendation.png
[8]: ./media/monitoring-autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/monitoring-autoscale-get-started/scale-condition-schedule.png
[10]: ./media/monitoring-autoscale-get-started/scale-condition-dates.png
[11]: ./media/monitoring-autoscale-get-started/scale-history.png
[12]: ./media/monitoring-autoscale-get-started/scale-definition-json.png
[13]: ./media/monitoring-autoscale-get-started/disable-autoscale.png
[14]: ./media/monitoring-autoscale-get-started/set-manualscale.png

