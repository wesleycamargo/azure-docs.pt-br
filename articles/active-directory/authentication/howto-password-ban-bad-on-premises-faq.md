---
title: Perguntas frequentes sobre a proteção por senha do Microsoft Azure Active Directory no local
description: Perguntas frequentes sobre a proteção por senha do Microsoft Azure Active Directory no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: f690625fda07bdbff671567f0292cc8e1cabeda1
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078688"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Visualização: Proteção por senha do Azure AD local – perguntas frequentes

|     |
| --- |
| A proteção de senha do Azure Active Directory é uma versão prévia do recurso do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Perguntas gerais

**P: Quando a proteção por senha do Azure AD alcançará a GA (Disponibilidade Geral)?**

Não anunciamos uma data de GA.

**P: Há suporte para a proteção por senha do Azure AD local em nuvens não públicas?**

Não, locais de proteção por senha do Azure Active Directory têm suporte apenas na nuvem pública.

**P: Como posso aplicar os benefícios da proteção por senha do Azure AD a um subconjunto dos usuários locais?**

Sem suporte. Depois de implantada e habilitada, a proteção por senha do Azure Active Directory não discrimina - todos os usuários recebem benefícios de segurança igual.

**P: Há suporte para instalação da proteção por senha do Azure AD lado a lado com outros produtos baseados em filtro de senha?**

Sim. Suporte para várias dlls de filtro de senha registrada é um recurso do Windows core e não específica para a proteção por senha do Azure Active Directory. Todas as dlls de filtro de senha registrados devem aceitar antes que uma senha seja aceita.

**P: Por que o DFSR é necessário para a replicação de SYSVOL?**

FRS (a tecnologia do predecessor para DFSR) tem vários problemas conhecidos e é totalmente suportada em versões mais recentes do Windows Server Active Directory. Zero testes da proteção por senha do Azure Active Directory serão feito nos domínios configurados para FRS.

Para obter mais informações, consulte:

[O caso para a replicação do sysvol migrando para DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[O fim é Nigh para FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**P: Por que uma reinicialização é necessária para instalar ou atualizar o software do agente de controlador de domínio?**

Esse requisito é causado pelo comportamento principal do Windows.

**P: Há alguma maneira de configurar um agente de controlador de domínio para usar um servidor proxy específico?**

 Não.

## <a name="next-steps"></a>Próximas etapas

Se você tiver uma pergunta de proteção de senha do Microsoft Azure Active Directory local que não foi respondida aqui, envie um item de comentários abaixo - Obrigado!

[Implantar proteção de senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
