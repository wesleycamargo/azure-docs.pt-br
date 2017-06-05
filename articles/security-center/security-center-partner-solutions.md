---
title: "Gerenciamento de soluções de parceiros na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento fornece uma orientação de como a Central de Segurança do Azure permite que você monitore rapidamente o status de integridade de suas soluções de parceiro integradas com sua assinatura do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5ffc3253cf07018ad6fe9f0112c75c2e1495e5df
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="monitoring-partner-solutions-with-azure-security-center"></a>Monitoramento de soluções de parceiros com a Central de Segurança do Azure
Este documento orienta sobre como monitorar o status de integridade de suas soluções de parceiro na Central de Segurança do Azure.

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo. Ela não é um guia passo a passo.
>
>

## <a name="monitoring-partner-solutions"></a>Monitoramento das soluções de parceiros
O bloco **Soluções de parceiros** na folha **Central de Segurança** permite que você monitore rapidamente o status de integridade de suas soluções de parceiro integradas com sua assinatura do Azure.

![Bloco Soluções de parceiros][1]

O bloco **Soluções de parceiros** exibe o número de soluções de parceiros integradas à sua assinatura. Se não houver nenhuma solução integrada, o bloco exibirá o número zero.

Para exibir a integridade das soluções de seu parceiro:

1. Selecione o bloco **Soluções de parceiros** . A folha **Soluções de parceiros** será aberta, exibindo uma lista das soluções de parceiros conectadas à Central de Segurança.

   ![Soluções de parceiros][3]

   O status de uma solução de parceiro pode ser:

   * Protegido (verde) - não há qualquer problema de integridade
   * Não íntegro (vermelho) - há um problema de integridade que requer atenção imediata
   * Parou de relatar (laranja) - a solução interrompeu o envio de relatórios sobre sua integridade
   * Status de proteção desconhecido (laranja) - a integridade da solução é desconhecida no momento devido a um processo para adicionar um novo recurso à solução existente com falha.
   * Não relatado (cinza) - a solução não reportou nada ainda. O status da solução pode não ser relatado se ele foi conectado e ainda está sendo implantado

2. Selecione uma solução de parceiro. Neste exemplo, vamos selecionar a solução **Qualys**.  Uma folha será aberta mostrando o status da solução de parceiro e dos recursos associados a ela. Selecione **Console da solução** para abrir a experiência de gerenciamento do parceiro para essa solução.

   ![Detalhes da solução de parceiro][4]
3. Retorne à folha **Qualys** e selecione **Vincular VM**. A folha **Vincular Aplicativos** é aberta. Nela, você pode conectar recursos à solução de parceiro.

   ![Vincular recursos à solução de parceiro][5]

## <a name="next-steps"></a>Próximas etapas
Neste documento, você foi apresentado às **Soluções de Parceiros** na Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) : saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) : saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e responder aos alertas de segurança.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png

