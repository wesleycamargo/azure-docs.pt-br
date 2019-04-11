---
title: Introdução ao registro combinado para a autenticação multifator (visualização) – Azure Active Directory e o Azure AD SSPR
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
ms.openlocfilehash: 3baf2690ae07b87bb4d5dba30fcd20f62a1a4506
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280563"
---
# <a name="enable-combined-security-information-registration-preview"></a>Registro de informações de segurança Enable combinado (visualização)

Antes de habilitar a nova experiência, examine o artigo [combinados de registro de informações de segurança (visualização)](concept-registration-mfa-sspr-combined.md) para garantir que você entende a funcionalidade e os efeitos desse recurso.

![Experiência aprimorada de registro de informações de segurança combinada](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| O registro de informações de segurança combinada para autenticação multifator do Azure e redefinição de senha de autoatendimento do Azure Active Directory (Azure AD) é um recurso de visualização pública do Azure AD. Para saber mais, veja [Termos de uso complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="enable-combined-registration"></a>Habilitar o registro combinado

Conclua estas etapas para habilitar o registro combinado:

1. Entrar no portal do Azure como um usuário administrador ou administrador global.
2. Vá para **Azure Active Directory** > **configurações do usuário** > **gerenciar as configurações de recursos de visualização do painel de acesso**.
3. Sob **os usuários podem usar recursos para registrar e gerenciar informações de segurança de visualização - atualizar**, optar por ativar para um **selecionados** grupo de usuários ou para **todos os** usuários.

   ![Habilitar a experiência de visualização de informações de segurança combinada para todos os usuários](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamada telefônica não estarão disponíveis para a autenticação multifator e os usuários do SSPR em locatários gratuitos/avaliação do Azure AD. Mensagens SMS não são afetadas por essa alteração. As opções de telefonema ainda estará disponíveis para os usuários no pago locatários do Azure AD.

> [!NOTE]
> Depois de habilitar o registro combinado, os usuários que se registrar ou confirmar o número de telefone ou aplicativo móvel por meio da nova experiência pode usá-los para a autenticação multifator e SSPR, se esses métodos estão habilitados no SSPR e a autenticação multifator políticas. Se você, em seguida, desabilite essa experiência, os usuários que vá para o registro SSPR anterior da página em `https:/aka.ms/ssprsetup` precisarão realizar a autenticação multifator, antes que possam acessar a página.

Se você tiver configurado o Site à lista de atribuição de zona do Internet Explorer, os seguintes sites precisa estar na mesma zona:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>Próximas etapas

[Métodos disponíveis para a autenticação multifator e SSPR](concept-authentication-methods.md)

[Configurar a redefinição de senha de autoatendimento](howto-sspr-deployment.md)

[Configurar a Autenticação Multifator do Azure](howto-mfa-getstarted.md)

[Solução de problemas combinado de registro de informações de segurança](howto-registration-mfa-sspr-combined-troubleshoot.md)