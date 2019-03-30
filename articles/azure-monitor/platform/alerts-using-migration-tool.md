---
title: Migrar seus alertas clássicos no Azure Monitor usando a ferramenta de migração voluntária
description: Saiba como usar a ferramenta de migração voluntária migrar suas regras de alerta clássicas.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 58c664beee942fe7115c7fff38a039c23bed6ac3
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631968"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Use a ferramenta de migração voluntária para migrar suas regras de alerta clássicas

Como [anunciado anteriormente](monitoring-classic-retirement.md), alertas clássicos no Azure Monitor estão sendo desativadas em julho de 2019. A ferramenta de migração para disparar voluntariamente a migração está disponível no portal do Azure e está distribuindo para os clientes que usam regras de alerta clássicas. Este artigo explica sobre como usar a ferramenta de migração para migrar voluntariamente suas regras de alerta clássicas antes do início de migração automática em julho de 2019.

## <a name="benefits-of-new-alerts"></a>Benefícios dos novos alertas

Alertas clássicos estão sendo substituídas por novos alertas unificada no Azure Monitor. A nova plataforma alertas tem os seguintes benefícios:

- Alerta sobre uma variedade de métricas multidimensionais para [muitos mais serviços do Azure](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)
- Suporte de alertas de métrica novos [regras de alerta com vários recursos](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) que reduzir bastante a sobrecarga de gerenciamento de várias regras.
- Mecanismo de notificação unificada
  - [Grupos de ação](action-groups.md) é um mecanismo de notificação modular que funciona com todos os novos tipos de alertas (métrica, log e log de atividades)
  - Você também poderá tirar proveito dos novos mecanismos de notificação, como SMS, voz e conector ITSM
- O [alerta experiência unificada](alerts-overview.md) traz todos os alertas em sinais diferentes (atividade de métrica, log e log) em um único lugar

## <a name="before-you-migrate"></a>Antes de migrar

Como parte da migração, as regras de alerta clássicas são convertidas em regras de alerta novo equivalentes e grupos de ação são criados.

- O formato de carga de notificação, bem como as APIs para criar e gerenciar regras de alerta novo é diferente das regras de alerta clássicas conforme eles dão suporte a mais recursos. Saiba mais [como se preparar para a migração](alerts-prepare-migration.md).

- Algumas regras de alerta clássicas não podem ser migradas usando a ferramenta. [Saiba quais regras não são migráveis e veja como migrá-los](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated).

    > [!NOTE]
    > Processo de migração não afetará a avaliação de suas regras de alerta clássicas. Eles continuarão a executar e enviar alertas até que eles são migrados e começam a avaliar novas regras de alerta.


## <a name="how-to-use-the-migration-tool"></a>Como usar a ferramenta de migração

O procedimento a seguir descreve como disparar a migração de suas regras de alerta clássicas no portal do Azure:

1. No [portal do Azure](https://portal.azure.com), clique em **Monitor**.

2. Clique em **alertas** , em seguida, clique em **gerenciar regras de alerta** ou **exibir alertas clássicos**.

3. Clique em **migrar para novas regras** para ir para a página de aterrissagem de migração. Esta página mostra uma lista de todas as suas assinaturas e o status de migração para eles.

    ![migração de aterrissagem](media/alerts-migration/migration-landing.png "migre regras")

4. Todas as assinaturas que podem ser migradas usando a ferramenta serão marcadas como **pronto para migrar**.

    > [!NOTE]
    > A ferramenta de migração está distribuindo em fases para todas as assinaturas que usam regras de alerta clássicas. Nas fases iniciais da distribuição, você poderá ver algumas assinaturas como não pronto para a migração.

5. Selecione uma ou mais assinaturas e clique em **migração de visualização**

6. Nessa página, você pode ver os detalhes de regras de alerta clássicos que serão migrados para uma assinatura por vez. Você também pode **baixar os detalhes de migração para esta assinatura** em um formato. csv.

    ![visualização da migração](media/alerts-migration/migration-preview.png "migração de visualização")

7. Forneça um ou mais **endereços de email** para ser notificado sobre o status de migração. Nós lhe enviaremos um email quando a migração for concluída ou uma ação é necessária.

8. Clique em **Iniciar migração**. Leia as informações mostradas na caixa de diálogo de confirmação e confirme se você está pronto para iniciar o processo de migração.

    >[!IMPORTANT]
    > Depois que você iniciar o processo de migração para uma assinatura, você não poderá editar/criar regras de alerta clássicas para a assinatura. No entanto, suas regras de alerta clássicas continuará em execução e fornecendo alertas até que eles são migrados. Isso é para garantir a fidelidade entre as regras de alerta clássicas e o novo criado durante a migração. Depois que a migração for concluída para a sua assinatura, você não pode usar regras de alerta clássicas mais.

    ![Confirmar a migração](media/alerts-migration/migration-confirm.png "confirmar Iniciar migração")

9. Como podemos concluir a migração ou precisa de uma ação, você receberá um email sobre os endereços de email fornecido na etapa 8. Periodicamente, você pode verificar o status, a página de aterrissagem de migração no portal.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="why-is-my-subscriptions-listed-as-not-ready-for-migration"></a>**Por que minha assinatura (s) está listado como não pronto para a migração?**

A ferramenta de migração está distribuindo em fases para todos os clientes. Nas fases iniciais, a maioria ou todas as suas assinaturas podem ser marcadas como **não está pronto para a migração**. No entanto, por abril, todas as assinaturas devem estar prontas para migrar.

Quando uma assinatura fica pronta para a migração, os proprietários da assinatura receberá um email notificando a disponibilidade da ferramenta. Fique atento para essa notificação.

### <a name="who-can-trigger-the-migration"></a>**Quem pode disparar a migração?**

Os usuários que têm a função de Colaborador de monitoramento atribuída a eles no nível da assinatura poderá disparar a migração. Saiba mais sobre [controle de acesso baseado em função para o processo de migração](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-is-the-migration-going-to-take"></a>**Quanto tempo a migração será necessária?**

Para a maioria das assinaturas, a migração completa normalmente menos de uma hora. Você pode manter controle do andamento da migração da página de aterrissagem de migração.  Durante esse tempo, por favor, ser garantida seus alertas ainda estão em execução no sistema de alertas clássicos ou em um novo.

### <a name="what-can-i-do-if-i-run-into-an-issue-during-migration"></a>**O que fazer se eu encontrar um problema durante a migração?**

Siga as [solução de problemas para ver as etapas de correção de problemas que você poderá enfrentar durante a migração](alerts-understand-migration.md#common-issues-and-remediations). Se nenhuma ação é necessária para concluir a migração, você será notificado sobre os endereços de email fornecido durante a migração.

## <a name="next-steps"></a>Próximas etapas

- [Preparar para a migração](alerts-prepare-migration.md)
- [Entender como funciona a ferramenta de migração](alerts-understand-migration.md)
