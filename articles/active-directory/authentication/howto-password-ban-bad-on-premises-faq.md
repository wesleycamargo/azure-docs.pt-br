---
title: Perguntas frequentes sobre a proteção por senha do Microsoft Azure Active Directory no local
description: Perguntas frequentes sobre a proteção por senha do Microsoft Azure Active Directory no local
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 59c89c81f618876de48de66a38e1063eb658fba4
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50742951"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Versão prévia: proteção de senha do Microsoft Azure Active Directory no local - Perguntas frequentes

|     |
| --- |
| A proteção de senha do Azure Active Directory é uma versão prévia do recurso do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Perguntas gerais

**P: Quando a proteção por senha do Azure Active Directory alcançará a Disponibilidade Geral (GA)?**

Não anunciamos uma data de GA.

**P: há locais de proteção por senha do Azure Active Directory com suporte nas nuvens não público?**

Não, locais de proteção por senha do Azure Active Directory têm suporte apenas na nuvem pública.

**P: como posso aplicar os benefícios da proteção de senha do Azure Active Directory a um subconjunto dos meus usuários locais?**

Sem suporte. Depois de implantada e habilitada, a proteção por senha do Azure Active Directory não discrimina - todos os usuários recebem benefícios de segurança igual.

**P: há suporte para instalar a proteção de senha do Azure Active Directory lado a lado com outros produtos com base em filtro de senha?**

Sim. Suporte para várias dlls de filtro de senha registrada é um recurso do Windows core e não específica para a proteção por senha do Azure Active Directory. Todas as dlls de filtro de senha registrados devem aceitar antes que uma senha seja aceita.

**P: por que o DFSR é necessário para a replicação de sysvol?**

FRS (a tecnologia do predecessor para DFSR) tem vários problemas conhecidos e é totalmente suportada em versões mais recentes do Windows Server Active Directory. Zero testes da proteção por senha do Azure Active Directory serão feito nos domínios configurados para FRS.

Para obter mais informações, consulte:

[O caso para a replicação do sysvol migrando para DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[O fim é Nigh para DRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**P: por que uma reinicialização é necessária para instalar ou atualizar o software do agente de controlador de domínio?**

Esse requisito é causado pelo comportamento principal do Windows.

**P: há alguma maneira de configurar um agente de controlador de domínio para usar um servidor proxy específico?**

Não.

## <a name="next-steps"></a>Próximas etapas

Se você tiver uma pergunta de proteção de senha do Microsoft Azure Active Directory local que não foi respondida aqui, envie um item de comentários abaixo - Obrigado!

[Implantar proteção de senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
