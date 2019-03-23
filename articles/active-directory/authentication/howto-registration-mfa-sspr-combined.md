---
title: Introdução ao registro combinado para SSPR do Azure AD e MFA (visualização) – Azure Active Directory
description: Habilitar combinadas do Azure AD a autenticação multifator e (visualização) do registro de redefinição de senha de autoatendimento
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5d7f9623a7594aaa79c4cff187486360b1befc8
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369013"
---
# <a name="enable-combined-security-information-registration-preview"></a>Registro de informações de segurança Enable combinado (visualização)

Antes de habilitar a nova experiência, examine o artigo [combinados de registro de informações de segurança (visualização)](concept-registration-mfa-sspr-combined.md) para garantir que você entende a funcionalidade e o impacto desse recurso.

![Experiência aprimorada de registro de informações de segurança combinada](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Registro de informações de segurança combinada para redefinição de senha self-service de autenticação multifator do Azure e o Azure AD é um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="enable-combined-registration"></a>Habilitar o registro combinado

Conclua as seguintes etapas para habilitar o registro combinado:

1. Entrar no portal do Azure como um usuário administrador ou administrador global.
2. Navegue até **Active Directory do Azure** >  **Configurações do usuário** >  **Gerenciar configurações dos recursos de visualização do painel de acesso**.
3. Sob **os usuários podem usar recursos para registrar e gerenciar informações de segurança de visualização - atualizar**, optar por ativar para um **selecionados** grupo de usuários ou para **todos os** usuários.

![Habilitar a experiência de visualização de informações de segurança combinada para todos os usuários](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> A partir de março de 2019 as opções de chamada telefônica não estará disponível para os usuários do MFA e o SSPR em locatários gratuitos/avaliação do Azure AD. Mensagens SMS não são afetadas por essa alteração. Chamada telefônica continuará disponível para os usuários no pago locatários do Azure AD. Essa alteração só afeta o locatários gratuitos/avaliação do Azure AD.

> [!NOTE]
> Uma vez você habilitar registro combinado, os usuários que registram ou confirme que seu número de telefone ou aplicativo móvel por meio da nova experiência pode usá-los para o MFA e o SSPR, se esses métodos estão habilitados nas políticas de MFA e o SSPR. Se você, em seguida, desabilite essa experiência, os usuários que vá para o registro SSPR anterior da página em `https:/aka.ms/ssprsetup` precisarão realizar a autenticação multifator, antes que possam acessar a página.

Se você tiver configurado o site à lista de atribuição de zona do Internet Explorer, os sites a seguir devem ser na mesma zona:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>Próximas etapas

[Métodos disponíveis para o MFA e o SSPR](concept-authentication-methods.md)

[Configurar a redefinição de senha de autoatendimento](howto-sspr-deployment.md)

[Configurar a autenticação multifator do Azure](howto-mfa-getstarted.md)

[Solução de problemas combinado de registro de informações de segurança](howto-registration-mfa-sspr-combined-troubleshoot.md)