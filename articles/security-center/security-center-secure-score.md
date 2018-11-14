---
title: Pontuação segura no Azure Security Center | Microsoft Docs
description: " Priorize suas recomendações de segurança usando a pontuação segura na Central de Segurança do Azure. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/5/2018
ms.author: rkarlin
ms.openlocfilehash: 3a377441758fcd7dd91deefb5cae91579e881498
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007036"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Melhorar sua pontuação segura na Central de segurança do Azure


Com tantos serviços oferecendo os benefícios de segurança, muitas vezes é difícil saber quais etapas seguir primeiro para proteger e otimizar sua carga de trabalho. A pontuação segura do Azure analisa suas recomendações de segurança e as prioriza para você, para que você saiba quais recomendações devem ser executadas primeiro. Isso ajuda a encontrar as vulnerabilidades de segurança mais sérias para que você possa priorizar a investigação. A pontuação segura é uma ferramenta que ajuda a avaliar sua postura de segurança de carga de trabalho.

![Proteger o painel de pontuação](./media/security-center-secure-score/secure-score-dashboard.png)

## <a name="secure-score-calculation"></a>Proteger o cálculo de pontuação

A Central de Segurança reproduz o trabalho de um analista de segurança, revisa suas recomendações de segurança e aplica algoritmos avançados para determinar a importância de cada recomendação.
O Centro de Segurança do Azure analisa constantemente as recomendações ativas e calcula sua pontuação segura com base nelas. A pontuação de uma recomendação é derivada de sua gravidade e das práticas recomendadas de segurança que afetarão mais a segurança da sua carga de trabalho.

A Central de Segurança do Azure também fornece uma **pontuação geral segura**. 

**A pontuação geral segura** é uma acumulação de todas as suas pontuações de recomendação. Você pode exibir sua pontuação geral segura entre suas assinaturas ou grupos de gerenciamento, dependendo do que você selecionar. A pontuação varia de acordo com a assinatura selecionada e as recomendações ativas nessas assinaturas.

 
Para verificar quais recomendações impactam mais a sua pontuação segura, você pode visualizar as três principais recomendações mais impactantes no painel da Central de Segurança ou pode classificar as recomendações na lista de recomendações usando a coluna **Impacto da pontuação de impacto**.


Para visualizar sua pontuação geral segura:

1. No painel do Azure, clique em **Central de segurança** e, em seguida, clique em **recomendações**.
2. Na parte superior você pode ver a pontuação segura, que representa a pontuação por políticas, por assinatura selecionada. 
2. Na tabela abaixo, que lista as recomendações, você pode ver que, para cada recomendação, há uma coluna que representa o **impacto da pontuação segura**. Esse número representa quanto sua pontuação geral segura melhorará se você seguir as recomendações. Por exemplo, na tela abaixo, se você **corrigir as vulnerabilidades nas configurações de segurança do contêiner**, sua pontuação segura aumentará em 35 pontos.

   ![Pontuação segura](./media/security-center-secure-score/security-center-secure-score1.png)

## <a name="individual-secure-score"></a>Pontuação segura individuais

Além disso, para visualizar pontuações seguras individuais, você pode encontrá-las dentro da área de recomendação individual.  

A **pontuação segura da recomendação** é um cálculo baseado na proporção entre seus recursos saudáveis e seus recursos totais. Se o número de recursos saudáveis for igual ao número total de recursos, você obtém a pontuação máxima segura da recomendação de 50. Para tentar obter sua pontuação segura mais próxima da pontuação máxima, corrija os recursos não íntegros seguindo as recomendações.

O **impacto recomendação** permite que você saiba o quanto melhora sua pontuação segura se você aplicar as etapas de recomendação. Por exemplo, se sua pontuação segura for 42 e o **impacto da recomendação** for +3, a execução das etapas descritas na recomendação melhora sua pontuação para se tornar 45.

A recomendação mostra quais ameaças sua carga de trabalho está exposta se as etapas de correção não forem executadas.

![pontuação de recomendação individual seguro](./media/security-center-secure-score/indiv-recommendation-secure-score.png)

## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como melhorar sua postura de segurança usando **Pontuação segura** na Central de Segurança do Azure. Para saber mais sobre a Central de Segurança, confira:

* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre perguntas frequentes sobre como usar o serviço.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md)– saiba como monitorar a integridade dos recursos do Azure.


