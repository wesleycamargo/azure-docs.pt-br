---
title: "Gerenciar contas administrativas de acesso de emergência no Azure AD | Microsoft Docs"
description: "Este artigo descreve como usar as contas de acesso de emergência para ajudar as organizações a restringir o acesso privilegiado em um ambiente existente do Azure Active Directory."
services: active-directory
keywords: "Não adicione ou edite palavras-chave sem consultar seu especialista em SEO."
author: markwahl-msft
ms.author: billmath
ms.date: 12/13/2017
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.openlocfilehash: 1545fb9a89794a74efbb855c4480040973c3308e
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>Gerenciar contas administrativas de acesso de emergência no Azure AD 

Para a maioria das atividades diárias, os direitos do *administrador global* não são necessários para os usuários. Os usuários não devem ser atribuídos à função permanentemente, pois podem, inadvertidamente, realizar uma tarefa que requer permissões superiores do que eles devem ter. Quando os usuários não precisam atuar como um administrador global, eles devem ativar a atribuição de função usando o PIM (Privileged Identity Management) do Azure AD (Azure Active Directory) em sua própria conta ou em uma outra conta administrativa.

Além dos usuários conseguirem direitos administrativos para eles mesmos, você precisa evitar ser inadvertidamente bloqueado da administração do locatário do Azure AD porque você não pode entrar ou ativar uma conta de usuário individual existente como um administrador. Você pode reduzir o impacto da falta acidental de acesso administrativo ao armazenar duas ou mais *contas de acesso de emergência* em seu locatário.

As contas de acesso de emergência podem ajudar as organizações a restringir o acesso privilegiado em um ambiente existente do Azure Active Directory. Essas contas são altamente privilegiadas e não são atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas a emergência ou cenários de urgência, situações em que as contas administrativas normais não podem ser usadas. As organizações devem manter uma meta de restringir o uso da conta de emergência para somente esse momento durante o qual é necessário.

Uma organização pode precisar usar uma conta de acesso de emergência nas seguintes situações:

 - As contas de usuário são federadas e a federação está indisponível no momento devido a uma interrupção de rede celular ou uma interrupção do provedor de identidade. Por exemplo, se o host de provedor de identidade em seu ambiente foi desligado, os usuários podem não conseguir entrar quando o Azure AD redireciona para seu provedor de identidade. 
 - Os administradores são registrados por meio da Autenticação Multifator do Azure e todos os seus dispositivos individuais não estão disponíveis. Os usuários podem não conseguir concluir a Autenticação Multifator para ativar uma função. Por exemplo, uma interrupção de rede celular está impedindo que eles atendam a chamadas telefônicas ou recebam mensagens de texto, os dois únicos dois mecanismos de autenticação registrados para os dispositivos. 
 - A pessoa com acesso administrativo global mais recente saiu da organização. O Azure AD impede que a conta do último *administrador global* seja excluída, mas não impede que a conta seja excluída ou desabilitada localmente. Qualquer situação pode fazer com que a organização não consiga recuperar a conta.

## <a name="initial-configuration"></a>Configuração inicial

Crie duas ou mais contas de acesso de emergência. Elas devem ser contas somente de nuvem que usam o domínio \*.onmicrosoft.com e que não são federadas ou sincronizadas de um ambiente local. 

As contas não devem ser associadas a nenhum usuário individual na organização. As organizações precisam garantir que as credenciais para essas contas sejam mantidas seguras e conhecidas apenas para os indivíduos que estão autorizados a usá-las. 

> [!NOTE]
> A senha de uma conta para uma conta de acesso de emergência geralmente é separada em duas ou três partes, escrita em pedaços de papel separados e armazenada em cofres seguros à prova de incêndio que estão em locais separados e seguros. 
>
> Certifique-se de que suas contas de acesso de emergência não estejam conectadas a nenhum telefone celular fornecido pelo funcionário, tokens de hardware que viajam com funcionários específicos ou outras credenciais específicas do funcionário. Essa precaução abrange instâncias em que um funcionário individual está inacessível quando a credencial é necessária. 

### <a name="initial-configuration-with-permanent-assignments"></a>Configuração inicial com atribuições permanentes

Uma opção é tornar os usuários membros permanentes da função de *administrador global*. Essa opção pode ser adequada para organizações que não têm assinaturas do Azure AD Premium P2.

Para reduzir o risco de um ataque resultante de uma senha comprometida, o Azure AD recomenda que você exija a Autenticação Multifator para todos os usuários individuais. Este grupo deve incluir os administradores e todos os outros (por exemplo, gerentes financeiros) cuja conta comprometida teria um impacto significativo. 

No entanto, se sua organização não tiver dispositivos compartilhados, a Autenticação Multifator poderá não ser possível para essas contas de acesso de emergência. Se você estiver configurando uma política de acesso condicional para exigir o [registro de Autenticação Multifator para todos os administradores](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) para o Azure AD e outros aplicativos SaaS (Software como Serviço), poderá precisar configurar exclusões de política para excluir as contas de acesso de emergência desse requisito.

### <a name="initial-configuration-with-approvals"></a>Configuração inicial com aprovações

Outra opção é configurar seus usuários como qualificados e aprovadores para ativar a função de *administrador global*. Esta opção exige que sua organização tenha assinaturas do Azure AD Premium P2. Ela também requer uma opção de Autenticação Multifator adequada para uso compartilhado entre várias pessoas e o ambiente de rede. Esses requisitos são porque a ativação da função de *administrador global* requer que os usuários tenham realizado a Autenticação Multifator anteriormente. Para obter mais informações, consulte [Como exigir o MFA no Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa).

Não recomendamos o uso da Autenticação Multifator associada a dispositivos pessoais para as contas de acesso de emergência. Em caso de emergência real, a pessoa que precisa de acesso a um dispositivo registrado da Autenticação Multifator pode não ser a que tem o dispositivo pessoal. 

Além disso, considere o cenário de ameaças. Por exemplo, uma circunstância imprevisível, como uma emergência de desastre natural, pode surgir, durante a qual um telefone celular ou outras redes podem não estar disponíveis. É importante garantir que todos os dispositivos registrados sejam mantidos em uma localização segura e conhecida que tenha vários meios de comunicação com o Azure AD.

## <a name="ongoing-monitoring"></a>Monitoramento contínuo

Monitore os [logs de auditoria e entrada do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) para quaisquer atividades de entrada ou auditoria das contas de acesso de emergência. Normalmente, essas contas não devem entrar nem fazer alterações, portanto, o uso delas provavelmente é anormal e requer uma investigação de segurança.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>A validação da verificação da conta deve ocorrer em intervalos regulares

Para validar a conta, execute as seguintes etapas no mínimo:
- A cada 90 dias.
- Quando houve uma alteração recente na equipe de TI, como uma alteração de cargo, uma saída ou uma nova contratação.
- Quando as assinaturas do Azure AD da organização foram alteradas.

Para treinar os membros da equipe para usar contas de acesso de emergência, faça o seguinte:

* Certifique-se de que a equipe de monitoramento de segurança esteja ciente de que a atividade de verificação de conta é contínua.
* Valide se as contas de usuário de nuvem podem entrar e ativar suas funções e se os usuários que precisam executar essas etapas durante uma emergência são treinados quanto ao processo.
* Certifique-se de que elas não tenham registrado a Autenticação Multifator ou o SSPR (redefinição de senha de autoatendimento) em nenhum dispositivo de usuário individual ou detalhes pessoais. 
* Se as contas forem registradas para a Autenticação Multifator para um dispositivo, para uso durante a ativação de função, certifique-se de que o dispositivo esteja acessível a todos os administradores que podem precisar usá-lo durante uma emergência. Verifique também se o dispositivo está registrado por meio de pelo menos dois mecanismos que não compartilham um modo de falha comum. Por exemplo, o dispositivo pode se comunicar com a Internet por meio de rede sem fio da instalação e por uma rede de provedor celular.
* Atualize as credenciais de conta.

## <a name="next-steps"></a>Próximas etapas
- [Adicionar um usuário baseado em nuvem](add-users-azure-active-directory.md) e [atribuir o novo usuário à função de administrador global](active-directory-users-assign-role-azure-portal.md).
- [Inscreva-se no Azure Active Directory Premium](active-directory-get-started-premium.md), se você ainda não tiver se inscrito.
- [Exigir Autenticação Multifator do Azure para usuários individuais atribuídos como administradores](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).
- [Configurar as proteções adicionais para os administradores globais no Office 365](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560), se você estiver usando o Office 365.
- [Executar uma análise de acesso de administradores globais](active-directory-privileged-identity-management-how-to-start-security-review.md) e [transicionar os administradores globais existentes para funções de administrador mais específicas](active-directory-assign-admin-roles-azure-portal.md).


