---
title: Perguntas frequentes sobre a proteção por senha do Azure AD local
description: Perguntas frequentes sobre a proteção por senha do Azure AD local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a4528cde92c5f738fa3fa7f4a649d84b1e79431
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175941"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Visualização: Proteção por senha do Azure AD local – perguntas frequentes

|     |
| --- |
| A proteção de senha do Azure AD é uma versão prévia pública do recurso do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Perguntas gerais

**P: Quando a proteção por senha do Azure AD alcançará a Disponibilidade Geral (GA)?**

A GA está planejada para o primeiro trimestre do ano de 2019 (antes do final de março de 2019). Obrigado a todos que forneceram comentários sobre o recurso até agora. Nós agradecemos!

**P: Há suporte para a proteção por senha do Azure AD local em nuvens não públicas?**

Não, locais de proteção por senha do Azure AD têm suporte apenas na nuvem pública.

**P: Como posso aplicar os benefícios da proteção por senha do Azure AD a um subconjunto dos usuários locais?**

Sem suporte. Depois de implantada e habilitada, a proteção por senha do Azure AD não discrimina, todos os usuários recebem benefícios de segurança iguais.

**P: Há suporte para instalação da proteção por senha do Azure AD lado a lado com outros produtos baseados em filtro de senha?**

Sim. Suporte para várias dlls de filtro de senha registrada é um recurso importante do Windows e não específico para a proteção por senha do Azure AD. Todas as dlls de filtro de senha registrados devem aceitar antes que uma senha seja aceita.

**P: Por que o DFSR é necessário para a replicação de SYSVOL?**

FRS (a tecnologia do predecessor para DFSR) tem vários problemas conhecidos e é totalmente suportada em versões mais recentes do Windows Server Active Directory. Zero testes da proteção por senha do Azure AD serão feitos nos domínios configurados para FRS.

Para obter mais informações, consulte:

[O caso para a replicação do sysvol migrando para DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[O fim é Nigh para FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**P: Por que uma reinicialização é necessária para instalar ou atualizar o software do agente de controlador de domínio?**

Esse requisito é causado pelo comportamento principal do Windows.

**P: Há alguma maneira de configurar um agente de controlador de domínio para usar um servidor proxy específico?**

 Não.

## <a name="next-steps"></a>Próximas etapas

Se você tiver uma pergunta sobre proteção de senha do Azure AD local que não foi respondida aqui, envie um comentário abaixo. Obrigado!

[Implantar proteção de senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
