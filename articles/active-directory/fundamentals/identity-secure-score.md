---
title: O que é a classificação de segurança de identidade no Azure AD? - versão prévia | Microsoft Docs
description: Saiba como você pode usar a classificação de segurança de identidade para melhorar a postura de segurança do seu locatário do Azure AD.
services: active-directory
keywords: classificação de segurança de identidade, Azure AD, acesso seguro aos recursos da empresa
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: fundamentals
ms.topic: overview
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: a9bb22cae2e9fcd607a81d442f4fb51f12c43454
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224130"
---
# <a name="what-is-the-identity-secure-score-in-azure-ad---preview"></a>O que é classificação de segurança de identidade no Azure AD? - versão prévia

Quão seguro é seu locatário do Azure AD? Se você não sabe como responder a essa pergunta, leia este artigo para saber como a classificação de segurança da identidade ajuda a monitorar e a melhorar sua postura de segurança de identidade. 

## <a name="what-is-an-identity-secure-score"></a>O que é uma classificação de segurança de identidade?

A classificação de segurança de identidade é um número entre 1 e 248 que funciona como um indicador de quão alinhado você está com as recomendações de melhores práticas da Microsoft para segurança.


![Classificação de segurança](./media/identity-secure-score/01.png)



A classificação ajuda você a:

- Medir objetivamente a sua postura de segurança de identidade

- Planejar aprimoramentos de segurança de identidade

- Examinar o sucesso das suas melhorias 


Você pode acessar a classificação e informações relacionadas a no painel de classificação de segurança de identidade. Neste painel, você encontrará:

- Sua classificação

    ![Classificação de segurança](./media/identity-secure-score/02.png)

- Um gráfico de comparação

    ![Classificação de segurança](./media/identity-secure-score/03.png)

- Um gráfico de tendências

    ![Classificação de segurança](./media/identity-secure-score/04.png)

- Uma lista de melhores práticas de segurança de identidade. 

    ![Classificação de segurança](./media/identity-secure-score/05.png)


Ao seguir as ações de melhoria, você pode:

- Melhorar sua postura e sua classificação de segurança.
 
- Aproveitar os recursos de Identidade da Microsoft. 



## <a name="how-do-i-get-my-secure-score"></a>Como faço para obter minha classificação de segurança?

A Classificação de Segurança de Identidade está disponível em todas as edições do Azure AD.

Para acessar sua classificação, vá para o [painel Visão Geral do Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore).



## <a name="how-does-it-work"></a>Como ele funciona?

A cada 48 horas, o Azure analisa sua configuração de segurança e compara suas configurações com as práticas recomendadas. Com base no resultado dessa avaliação, uma nova classificação é calculada para seu locatário. Isso significa que pode levar até 48 horas até que uma alteração de configuração que você fez se reflita na sua classificação. 

Cada recomendação é medida com base na sua configuração do Azure AD. Se você estiver usando os produtos de terceiros para habilitar uma prática recomendada, poderá indicar isso nas configurações de uma ação de melhoria.

![Classificação de segurança](./media/identity-secure-score/07.png)


Além disso, você também tem a opção de definir recomendações a serem ignoradas se elas não se aplicam ao seu ambiente. Uma recomendação ignorada não contribui para o cálculo de sua classificação. 
 
![Classificação de segurança](./media/identity-secure-score/06.png)



## <a name="how-does-it-help-me"></a>Como isso me ajuda?

A classificação de segurança ajuda você a:

- Medir objetivamente a sua postura de segurança de identidade

- Planejar aprimoramentos de segurança de identidade

- Examinar o sucesso das suas melhorias



## <a name="what-you-should-know"></a>O que você deve saber

### <a name="who-can-use-the-identity-secure-score"></a>Quem pode usar a classificação de segurança de identidade?

A classificação de segurança de identidade pode ser usada pelas funções a seguir:

- Administrador global
- Administrador de segurança 
- Leitores de segurança 

### <a name="what-does-not-scored-mean"></a>O que [Não Pontuado] significa?

Ações rotuladas como [Não Pontuada] são aquelas você pode executar em sua organização, mas não serão pontuadas porque não estão conectadas à ferramenta (ainda!). Portanto, você ainda pode melhorar sua segurança, mas não receberá crédito por essas ações no momento.

### <a name="how-often-is-my-score-updated"></a>Com que frequência minha classificação é atualizada?

A classificação é calculada uma vez por dia (aproximadamente à 1h PST). Se você fizer uma alteração a uma ação de medida, a classificação será atualizada automaticamente no dia seguinte. Podem ser necessárias até 48 horas para uma alteração ser refletida na sua classificação.


### <a name="my-score-changed-how-do-i-figure-out-why"></a>Minha classificação mudou. Como faço para descobrir por quê?

Na página do analisador de classificação no [portal de classificação de segurança](https://securescore.microsoft.com/#!/score), clique em um ponto de dados de um dia específico e, em seguida, role para baixo para ver as ações completas e incompletas para o dia para descobrir o que mudou.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>A classificação de segurança mede meu risco de sofrer uma violação de segurança?

Em breve, não. A classificação de Segurança não expressa uma medida absoluta da probabilidade de você sofrer uma violação de segurança. Expressa a extensão em que você adotou recursos que podem compensar o risco de sofrer uma violação. Nenhum serviço pode garantir que você não sofra uma violação, e a Classificação de Segurança não deve ser interpretada como garantia de forma alguma.

### <a name="how-should-i-interpret-my-score"></a>Como devo interpretar a minha classificação?

Você recebe pontos para configuração recursos de segurança recomendados ou realizar tarefas relacionadas a segurança (como a leitura de relatórios). Algumas ações são pontuadas para conclusão parcial, como habilitar MFA (autenticação multifator) para seus usuários. Sua Classificação de Segurança é diretamente representativa dos serviços de segurança da Microsoft que você usa. Lembre-se de que a segurança sempre deve ser equilibrada com facilidade de uso. Todos os controles de segurança têm um componente de impacto ao usuário. Controles com baixo impacto ao usuário devem ter pouco ou nenhum efeito sobre as operações diárias dos usuários.

Para ver o histórico da classificação, vá para a página do analisador de classificação no [portal de classificação de segurança](https://securescore.microsoft.com/#!/score). Escolha uma data específica para ver quais controles foram habilitados para aquele dia e que pontos você ganhou para cada um.


### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Como a classificação de segurança de identidade se relaciona à classificação de segurança do Office 365? 

A [classificação de segurança do Office 365](https://docs.microsoft.com/office365/securitycompliance/office-365-secure-score) está prestes a ser migrada para uma agregação de cinco classificações diferentes:

- Identidade

- Dados

- Dispositivos

- Infraestrutura

- Aplicativos

A classificação de segurança de identidade a parte da identidade da classificação de segurança do Office 365. Isso significa que suas recomendações para a classificação de segurança de identidade e a classificação da identidade no Office 365 serão iguais. 


## <a name="next-steps"></a>Próximas etapas

Se você quiser assistir a um vídeo sobre a classificação de segurança do Office 365, clique [aqui](https://www.youtube.com/watch?v=jzfpDJ9Kg-A).
 
