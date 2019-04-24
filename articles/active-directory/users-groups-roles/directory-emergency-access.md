---
title: Gerenciar contas de administrador de acesso de emergência - Azure Active Directory | Microsoft Docs
description: Este artigo descreve como usar contas de acesso de emergência para ajudar a evitar ser inadvertidamente bloqueado do seu locatário do Azure Active Directory (Azure AD).
services: active-directory
author: markwahl-msft
ms.author: curtand
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f430a832ffb35b95d0bf4eff2d82be5ecc3d865c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60472342"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Gerenciar contas de acesso de emergência no Microsoft Azure Active Directory

É importante impedir que o seu locatário do Azure Active Directory (Azure AD) seja bloqueado inadvertidamente, pois não é possível entrar ou ativar a conta de um usuário individual existente como um administrador. Você pode reduzir o impacto da falta acidental de acesso administrativo ao criar duas ou mais *contas de acesso de emergência* em seu locatário.

Essas contas de acesso de emergência são altamente privilegiadas e não são atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas à emergência ou cenários de urgência em que as contas administrativas normais não podem ser usadas. As organizações devem manter uma meta de restringir o uso da conta de emergência para somente esse momento durante o qual é absolutamente necessário.

Este artigo fornece diretrizes para gerenciar contas de acesso de emergência no Microsoft Azure Active Directory.

## <a name="when-would-you-use-an-emergency-access-account"></a>Quando você usaria uma conta de acesso de emergência?

Uma organização pode precisar usar uma conta de acesso de emergência nas seguintes situações:

- As contas de usuário são federadas e a federação está indisponível no momento devido a uma interrupção de rede celular ou uma interrupção do provedor de identidade. Por exemplo, se o host de provedor de identidade em seu ambiente foi desligado, os usuários podem não conseguir entrar quando o Azure AD redireciona para seu provedor de identidade.
- Os administradores são registrados por meio da Autenticação Multifator do Azure e todos os seus dispositivos individuais ou o serviço não estão disponíveis. Os usuários podem não conseguir concluir a Autenticação Multifator para ativar uma função. Por exemplo, uma interrupção de rede celular está impedindo que eles atendam a chamadas telefônicas ou recebam mensagens de texto, os dois únicos dois mecanismos de autenticação registrados para os dispositivos.
- A pessoa com acesso administrativo global mais recente saiu da organização. O Microsoft Azure Active Directory impede que a conta do último Administrador Global seja excluída, mas não impede que a conta seja excluída ou desabilitada localmente. Qualquer situação pode fazer com que a organização não consiga recuperar a conta.
- Circunstâncias imprevisíveis, como uma emergência de desastre natural, em que um telefone celular ou outras redes podem não estar disponíveis. 

## <a name="create-two-cloud-based-emergency-access-accounts"></a>Crie duas ou mais contas de acesso de emergência na baseadas em nuvem

Crie duas ou mais contas de acesso de emergência. Elas devem ser contas somente de nuvem que usam o domínio \*.onmicrosoft.com e que não são federadas ou sincronizadas de um ambiente local.

Ao configurar essas contas, os seguintes requisitos devem ser atendidos:

- As contas de emergência não devem ser associadas a nenhum usuário individual na organização. Certifique-se de que suas contas não estejam conectadas a nenhum telefone celular fornecido pelo funcionário, tokens de hardware que viajam com funcionários específicos ou outras credenciais específicas do funcionário. Essa precaução abrange instâncias em que um funcionário individual está inacessível quando a credencial é necessária. É importante garantir que todos os dispositivos registrados sejam mantidos em uma localização segura e conhecida que tenha vários meios de comunicação com o Azure AD.
- O mecanismo de autenticação usado para uma conta de acesso de emergência deve ser diferente do usado por outras contas administrativas, incluindo outras contas de acesso de emergência.  Por exemplo, se sua entrada normal no administrador é por meio da Autenticação Multifator do Microsoft Azure (MFA) local, o Azure MFA seria um mecanismo diferente.  No entanto, se Azure MFA é a parte primária de autenticação para suas contas administrativas, considere uma abordagem diferente para elas, como o uso de acesso condicional com um provedor MFA de terceiros.
- O dispositivo ou a credencial não deve expirar ou estar no escopo de limpeza automatizado devido à falta de uso.  
- A atribuição de função de Administrador Global deve ser permanente para suas contas de acesso de emergência. 


### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Excluir pelo menos uma conta de autenticação de multifator baseada em telefone

Para reduzir o risco de um ataque resultante de uma senha comprometida, o Microsoft Azure Active Directory recomenda que você exija a autenticação multifator para todos os usuários individuais. Este grupo deve incluir os administradores e todos os outros (por exemplo, gerentes financeiros) cuja conta comprometida teria um impacto significativo.

No entanto, pelo menos uma de suas contas de acesso de emergência não deve ter o mesmo mecanismo de autenticação multifator como outras contas não emergenciais. Isso inclui soluções de autenticação multifator de terceiros. Se você tiver uma política de acesso condicional para exigir a [ Autenticação Multifator para todos os administradores](../authentication/howto-mfa-userstates.md) para o Microsoft Azure Active Directory e outros aplicativos SaaS (Software como Serviço), é necessário excluir as contas de acesso de emergência desse requisito e configurar um diferente mecanismo no seu lugar. Além disso, verifique se que as contas não têm uma política de autenticação multifator por usuário.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Exclua pelo menos uma conta de políticas de acesso condicional

Durante uma emergência, não é desejável uma política para bloquear o acesso para corrigir um problema. Ao menos uma conta de acesso de emergência deve ser excluída de todas as políticas de acesso condicionais. Se tiver habilitado uma [política de linha de base](../conditional-access/baseline-protection.md), exclua as contas de acesso de emergência.

## <a name="additional-guidance-for-hybrid-customers"></a>Orientações adicionais para clientes híbridos

Uma opção adicional para organizações que usam os serviços de domínio do domínio do aplicativo (AD) e do Sistema de Arquivos Distribuído do Azure (ADFS) ou o provedor de identidade semelhante para federar ao Microsoft Azure Active Directory, é configurar uma conta de acesso de emergência cuja declaração da MFA pode ser fornecida por ser um provedor de identidade.  Por exemplo, a conta de acesso de emergência pode ser feita por um certificado e um par de chaves como uma armazenada em um cartão inteligente.  Quando o usuário é autenticado para o AD, o ADFS pode fornecer uma declaração ao Microsoft Azure Active Directory que indica que o usuário atende aos requisitos da MFA.  Mesmo com essa abordagem, as organizações ainda devem ter contas de acesso de emergência baseadas em nuvem no caso da federação não poder ser estabelecida. 

## <a name="store-devices-and-credentials-in-a-safe-location"></a>Armazene os dispositivos e as credenciais em um local seguro

As organizações precisam garantir que as credenciais para essas contas de emergência sejam mantidas seguras e conhecidas apenas para os indivíduos que estão autorizados a usá-las. Alguns clientes usam um cartão inteligente e outros usam senhas. A senha para uma conta de acesso de emergência geralmente é separada em duas ou três partes, escrita em pedaços de papel separados e armazenada em cofres seguros à prova de incêndio que estão em locais separados e seguros.

Se usar as senhas, verifique se as contas têm senhas fortes que não façam a senha expirar. Idealmente, as senhas devem ter pelo menos 16 caracteres longos e gerados aleatoriamente.


## <a name="monitor-sign-in-and-audit-logs"></a>Exibir entradas e logs de auditoria

Monitore os [logs de auditoria e entradas do Microsoft Azure Active Directory](../reports-monitoring/concept-sign-ins.md) para quaisquer atividades de entrada ou auditoria das contas de acesso de emergência. Normalmente, essas contas não devem entrar nem fazer alterações, portanto, o uso delas provavelmente é anormal e requer uma investigação de segurança.

## <a name="validate-accounts-at-regular-intervals"></a>Valide as contas em intervalos regulares

Para o treinamento dos membros da equipe para usar contas de acesso de emergência e validar as contas de acesso de emergência, execute as seguintes etapas mínimas em intervalos regulares:

- Certifique-se de que a equipe de monitoramento de segurança esteja ciente de que a atividade de verificação de conta é contínua.
- Certifique-se de que o processo de quebra de vidro de emergência para usar essas contas é documentado e atual.
- Certifique-se de que os administradores e os agentes de segurança que talvez sejam necessários para executar essas etapas durante uma emergência recebam um treinamento sobre o processo.
- Atualize as credenciais da conta, em particular, todas as senhas para suas contas de acesso de emergência e, em seguida, confirme se as contas de acesso de emergência podem ser acessadas e as tarefas administrativas executadas.
- Certifique-se de que os usuários não tenham registrado a Autenticação Multifator ou o SSPR (redefinição de senha de autoatendimento) em nenhum dispositivo de usuário individual ou detalhes pessoais. 
- Se as contas forem registradas para a Autenticação Multifator para um dispositivo, para uso durante as entradas ou a ativação de função, certifique-se de que o dispositivo esteja acessível a todos os administradores que podem precisar usá-lo durante uma emergência. Verifique também se o dispositivo pode se comunicar por meio de pelo menos dois caminhos de rede que não compartilham um modo de falha comum. Por exemplo, o dispositivo pode se comunicar com a Internet por meio de rede sem fio da instalação e por uma rede de provedor celular.

Essas etapas devem ser realizadas em intervalos regulares e para alterações de chave:

- Pelo menos a cada 90 dias
- Quando houve uma alteração recente na equipe de TI, como uma alteração de cargo, uma saída ou uma nova contratação
- Quando as assinaturas do Microsoft Azure Active Directory da organização foram alteradas

## <a name="next-steps"></a>Próximos passos

- [Protegendo o acesso privilegiado para implantações de nuvem e híbridos no Azure AD](directory-admin-roles-secure.md)
- [Adicionar um usuário usando o Microsoft Azure Active Directory](../fundamentals/add-users-azure-active-directory.md) e [atribuir ao novo usuário à função de administrador global](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Inscreva-se no Azure AD Premium](../fundamentals/active-directory-get-started-premium.md), se você ainda não tiver se inscrito
- [Como exigir a verificação em duas etapas para um usuário](../authentication/howto-mfa-userstates.md)
- [Configurar as proteções adicionais para os Administradores Globais no Office 365](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts), se você estiver usando o Office 365
- [Executar uma análise de acesso de Administradores Globais](../privileged-identity-management/pim-how-to-start-security-review.md) e [transicionar os Administradores Globais existentes para funções de administrador mais específicas](directory-assign-admin-roles.md)
