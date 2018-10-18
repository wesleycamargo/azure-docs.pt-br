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
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: fc521db9ad753c4162b65abfd2f9f23c318fa994
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46131056"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Melhorar sua pontuação segura na Central de segurança do Azure


Com tantos serviços oferecendo os benefícios de segurança, muitas vezes é difícil saber quais etapas seguir primeiro para proteger e otimizar sua carga de trabalho. A pontuação segura do Centro de Segurança do Azure analisa suas recomendações de segurança e as prioriza para você, para que você saiba quais recomendações devem ser executadas primeiro, ajudando a encontrar as vulnerabilidades de segurança mais sérias para poder priorizar a investigação. A pontuação segura é uma ferramenta de medição que ajuda você a proteger a segurança para alcançar uma carga de trabalho segura.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]


![Proteger o painel de pontuação](./media/security-center-secure-score/secure-score-dashboard.png)

## <a name="secure-score-calculation"></a>Proteger o cálculo de pontuação

A Central de Segurança reproduz o trabalho do analista de segurança, analisando suas recomendações de segurança e aplicando algoritmos avançados para determinar a importância de cada recomendação.
O Centro de Segurança do Azure analisa constantemente as recomendações ativas e calcula sua pontuação segura com base nelas. A pontuação de uma recomendação é derivada das práticas recomendadas de segurança e gravidade que afetarão mais a segurança da sua carga de trabalho.

O **secure score** é um cálculo com base na proporção de entre os recursos de integridade e o total de recursos. Se o número de recursos saudáveis for igual ao número total de recursos, você obterá a pontuação máxima segura de 50. Para tentar obter sua pontuação segura mais próxima da pontuação máxima, corrija os recursos não íntegros seguindo as recomendações.

A Central de Segurança também fornece uma pontuação geral segura. 

**Pontuação geral segura** é um acúmulo de todas as suas recomendações. Você pode exibir sua pontuação geral segura entre suas assinaturas ou grupos de gerenciamento, dependendo do que você selecionar. A pontuação varia de acordo com a assinatura selecionada e as recomendações ativas nessas assinaturas.

 

Para verificar quais recomendações impactam mais a sua pontuação segura, você pode visualizar as 3 principais recomendações de maior impacto no painel da Central de Segurança ou pode classificar as recomendações na lista de recomendações usando a coluna **Impacto da pontuação de impacto**.


Para visualizar sua pontuação geral segura:

1. No painel do Azure, clique em **Central de segurança** e, em seguida, clique em **recomendações**.
2. Na parte superior você pode ver a pontuação segura, que representa a pontuação por políticas, por assinatura selecionada. 
2. Na tabela abaixo, que lista as recomendações, você pode ver que, para cada recomendação, há uma coluna que representa o **impacto da pontuação segura**. Esse número representa quanto sua pontuação geral segura melhorará se você seguir as recomendações. Por exemplo, na tela abaixo, se você **corrigir as vulnerabilidades nas configurações de segurança do contêiner**, sua pontuação segura aumentará em 35 pontos.

   ![Pontuação segura](./media/security-center-secure-score/security-center-secure-score1.png)

## <a name="individual-secure-score"></a>Pontuação segura individuais

Além disso, para visualizar pontuações seguras individuais, você pode encontrá-las dentro da área de recomendação individual.  

A **pontuação segura da recomendação** é um cálculo baseado na proporção entre seus recursos saudáveis e seus recursos totais. Se o número de recursos saudáveis for igual ao número total de recursos, você obterá a pontuação máxima segura da recomendação. Para tentar obter sua pontuação segura mais próxima da pontuação máxima, corrija os recursos não íntegros seguindo as etapas de correção.

O **impacto da recomendação** permite que você saiba o quanto sua pontuação segura melhorará se você aplicar as etapas de recomendação. Por exemplo, se sua pontuação segura for 42 e o **impacto da Recomendação** for +3, se você executar as etapas descritas na recomendação, sua pontuação segura aumentará para se tornar 45.

A recomendação mostra quais ameaças sua carga de trabalho está exposta se as etapas de correção não forem executadas.

![pontuação de recomendação individual seguro](./media/security-center-secure-score/indiv-recommendation-secure-score.png)

## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como melhorar sua postura de segurança usando **Pontuação segura** na Central de Segurança do Azure. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre perguntas frequentes sobre como usar o serviço.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md)– saiba como monitorar a integridade dos recursos do Azure.


