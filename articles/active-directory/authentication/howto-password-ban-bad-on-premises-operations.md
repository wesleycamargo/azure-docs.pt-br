---
title: Operações de proteção de senha do AD do Azure e emissão de relatórios - Azure Active Directory
description: Operações de pós-implantação de proteção por senha do AD do Azure e a emissão de relatórios
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
ms.openlocfilehash: ca85007bb016cc98d1be61ce08865945e699ad4a
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312420"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Procedimentos operacionais de proteção por senha do Azure AD

Após concluir a [instalação da proteção por senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md) no local, há alguns itens que devem ser configurados no portal do Azure.

## <a name="configure-the-custom-banned-password-list"></a>Configurar a lista de senhas banidas personalizada

Siga as orientações no artigo [Configurar a lista de senhas banidas personalizada](howto-password-ban-bad-configure.md) para ver as etapas para personalizar a lista de senhas banidas da sua organização.

## <a name="enable-password-protection"></a>Habilitar proteção por senha

1. Entrar para o [portal do Azure](https://portal.azure.com) e navegue até **Azure Active Directory**, **métodos de autenticação**, em seguida, **proteção por senha**.
1. Configure **Habilitar proteção por senha no Windows Server Active Directory** para **Sim**
1. Conforme mencionado na [Guia de implantação](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), é recomendável configurar inicialmente o **Modo** para **Auditoria**
   * Depois que estiver familiarizado com o recurso, você pode alternar a **Modo** para **Imposto**
1. Clique em **Salvar**

![Habilitar os componentes de proteção por senha do Azure AD no portal do Azure](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Modo de auditoria

O modo de auditoria foi desenvolvido como uma maneira de executar o software em um modo "what if". Cada serviço do agente DC avalia uma senha de entrada conforme a política ativa no momento. Se a política atual estiver configurada no modo de auditoria, senhas "incorretas" resultam em mensagens do log de eventos, mas são aceitas. Essa é a única diferença entre o modo de auditoria e de imposição; todas as outras operações executam o mesmo.

> [!NOTE]
> A Microsoft recomenda que a implantação e testes iniciais sempre sejam comecem no modo de auditoria. Em seguida, os eventos no log de eventos devem ser monitorados para tentar prever se todos os processos operacionais existentes seriam afetados quando o modo de imposição for habilitado.

## <a name="enforce-mode"></a>Modo de imposição

O modo de imposição serve como a configuração final. Como no modo de auditoria acima, cada serviço do agente DC avalia senhas de entrada de acordo com a política ativa no momento. No entanto, se o modo de imposição estiver habilitado, uma senha que for considerada não segura, conforme a política, será rejeitada.

Quando uma senha é rejeitada no modo de imposição pelo Agente DC de proteção por senha do Azure AD, o impacto visível observado por um usuário final será idêntico ao que ele veria se a senha fosse rejeitada pela tradicional imposição de complexidade da senha local. Por exemplo, um usuário pode ver a seguinte mensagem de erro tradicional na tela de logon do Windows \ change password:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Essa mensagem é apenas um exemplo dos vários resultados possíveis. A mensagem de erro específica pode variar, dependendo do software ou cenário real que está tentando definir uma senha não segura.

Os usuário finais afetados talvez precisem trabalhar com a equipe de TI para entender os requisitos novos e serem mais capazes de escolher senhas seguras.

## <a name="enable-mode"></a>Ativar modo

Essa configuração normalmente deve ser deixada em seu estado ativado padrão (Sim). A definição dessa configuração como desabilitada (Não) fará com que todos os agentes DC de proteção de senha do Azure AD implantados entrem em um modo quiescente em que todas as senhas sejam aceitas como estão e nenhuma atividade de validação seja executada (nem mesmo eventos de auditoria serão executados, por exemplo).

## <a name="next-steps"></a>Próximas etapas

[Monitoramento para proteção de senha do Azure AD](howto-password-ban-bad-on-premises-monitor.md)
