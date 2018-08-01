---
title: Operações da versão prévia da proteção por senha do Azure AD e relatórios
description: Operações de pós-implantação da versão prévia da proteção por senha do Azure AD e relatórios
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 14aa52b6d424423f4863efa63f3e2e66b84dac70
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163433"
---
# <a name="preview-azure-ad-password-protection-post-deployment"></a>Versão prévia: pós-implantação da proteção por senha do Azure AD

|     |
| --- |
| A lista de senhas proibidas personalizada e a proteção por senha do Azure AD são recursos de visualização pública do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Após concluir a [instalação da proteção por senha do Azure AD](howto-password-ban-bad-on-premises.md) no local, há alguns itens que devem ser configurados no portal do Azure.

## <a name="configure-the-custom-banned-password-list"></a>Configurar a lista de senhas banidas personalizada

Siga as orientações no artigo [Configurar a lista de senhas banidas personalizada](howto-password-ban-bad.md) para ver as etapas para personalizar a lista de senhas banidas da sua organização.

## <a name="enable-password-protection"></a>Habilitar proteção por senha

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até **Azure Active Directory**, **Métodos de autenticação** e, em seguida, **Proteção por senha (versão prévia)**.
1. Configure **Habilitar proteção por senha no Windows Server Active Directory** para **Sim**
1. Conforme mencionado na [Guia de implantação](howto-password-ban-bad-on-premises.md#deployment-strategy), é recomendável configurar inicialmente o **Modo** para **Auditoria**
   * Depois que estiver familiarizado com o recurso, você pode alternar a **Modo** para **Imposto**
1. Clique em **Salvar**

![Habilitar os componentes de proteção por senha do Azure AD no portal do Azure](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Modo de auditoria

O modo de auditoria foi desenvolvido como uma maneira de executar o software em um modo "what if". Cada serviço do agente DC avalia uma senha de entrada conforme a política ativa no momento. Se a política atual estiver configurada no modo de auditoria, senhas "incorretas" resultam em mensagens do log de eventos, mas são aceitas. Essa é a única diferença entre o modo de auditoria e de imposição; todas as outras operações executam o mesmo.

> [!NOTE]
> A Microsoft recomenda que a implantação e testes iniciais sempre sejam comecem no modo de auditoria. Em seguida, os eventos no log de eventos devem ser monitorados para tentar prever se todos os processos operacionais existentes seriam afetados quando o modo de imposição for habilitado.

## <a name="enforce-mode"></a>Modo de imposição

O modo de imposição serve como a configuração final. Como no modo de auditoria acima, cada serviço do agente DC avalia senhas de entrada de acordo com a política ativa no momento. No entanto, se o modo de imposição estiver habilitado, uma senha que for considerada não segura, conforme a política, será rejeitada.

Quando uma senha é rejeitada no modo de imposição pelo Agente DC de proteção por senha do Azure AD, o impacto visível observado por um usuário final será idêntico ao que ele veria se a senha fosse rejeitada pela tradicional imposição de complexidade da senha local. Por exemplo, um usuário poderá ver a mensagem de erro tradicional na tela de logon/alteração de senha:

"Não foi possível atualizar a senha. O valor fornecido para a nova senha não atende os requisitos de comprimento, complexidade ou de histórico do domínio.”

Essa mensagem é apenas um exemplo dos vários resultados possíveis. A mensagem de erro específica pode variar, dependendo do software ou cenário real que está tentando definir uma senha não segura.

Os usuário finais afetados talvez precisem trabalhar com a equipe de TI para entender os requisitos novos e serem mais capazes de escolher senhas seguras.

## <a name="usage-reporting"></a>Relatório de uso

O cmdlet `Get-AzureADPasswordProtectionSummaryReport` pode ser usado para produzir uma exibição resumida da atividade. Um exemplo de saída deste cmdlet é o seguinte:

```
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

O escopo da emissão de relatórios do cmdlet pode ser influenciado usando um dos parâmetros –Forest, -Domain ou – DomainController. Não especificar um parâmetro implica – Forest.

> [!NOTE]
> Esse cmdlet funciona consultando remotamente o log de eventos do administrador de cada serviço do agente DC. Se os logs de eventos contiverem um grande número de eventos, o cmdlet pode levar muito tempo para ser concluído. Além disso, consultas de rede em massa de grandes conjuntos de dados podem afetar o desempenho do controlador de domínio. Portanto, esse cmdlet deve ser usado com cuidado em ambientes de produção.

## <a name="next-steps"></a>Próximas etapas

[Solução de problemas e informações de registro em log da proteção por senha do Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)
