---
title: Acesso condicional do Azure para aplicativos SaaS| Microsoft Docs
description: "O acesso condicional no AD do Azure permite que você configure as regras de acesso de autenticação multifator por aplicativo e a capacidade de bloquear o acesso de usuários que não estão em uma rede confiável. "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 4720d168f5ecd3a5e39cdcad812efb52755fb59a
ms.contentlocale: pt-br
ms.lasthandoff: 12/29/2016

---
# <a name="getting-started-with-azure-active-directory-conditional-access"></a>Introdução ao Acesso Condicional ao Azure Active Directory
O Acesso Condicional ao Azure Active Directory para aplicativos [SaaS](https://azure.microsoft.com/overview/what-is-saas/) e aplicativos conectados ao Azure AD permite que você configure o acesso condicional com base em grupo, localização e sensibilidade de aplicativo. 

Com acesso condicional com base na confidencialidade do aplicativo, você pode definir regras de acesso de autenticação multifator (MFA) por aplicativo. O MFA por aplicativo fornece a capacidade de bloquear o acesso para usuários que não estão em uma rede confiável. Você pode aplicar as regras de MFA a todos os usuários atribuídos ao aplicativo ou apenas aos usuários nos grupos de segurança especificados.  Os usuários poderão ser excluídos do requisito de MFA se estiverem acessando o aplicativo de um endereço IP de dentro da rede da organização.

Esses recursos estarão disponíveis para os clientes que compraram uma licença Azure Active Directory Premium.

## <a name="scenario-prerequisites"></a>Pré-requisitos do cenário
* Licença do Azure Active Directory Premium
* Um locatário do Azure Active Directory federado ou gerenciado
* Locatários federados exigem que a autenticação multifator esteja habilitada.

## <a name="configure-per-application-access-rules"></a>Configurar regras de acesso por aplicativo
Esta seção descreve como configurar as regras de acesso por aplicativo.

1. Entre no portal clássico do Azure usando uma conta que seja um administrador global no Azure AD.
2. No painel esquerdo, selecione **Active Directory**.
3. Na guia Diretório, selecione o diretório.
4. Selecione a guia **Aplicativos** .
5. Selecione o aplicativo que será definido para a regra.
6. Selecione a guia **Configurar** .
7. Role para baixo até a seção de regras de acesso. Selecione a regra de acesso desejada.
8. Especifique os usuários aos quais a regra será aplicada.
9. Habilite a política selecionando **Habilitado para Ativação**.

## <a name="understanding-access-rules"></a>Noções básicas de regras de acesso
Esta seção fornece uma descrição detalhada das regras de acesso com suporte no Acesso Condicional de Aplicativo do Azure.

### <a name="specifying-the-users-the-access-rules-apply-to"></a>Especificar os usuários aos quais as regras de acesso se aplicam
Por padrão, a política será aplicada a todos os usuários que têm acesso ao aplicativo. No entanto, também é possível restringir a política aos usuários que são membros dos grupos de segurança especificados. O botão **Adicionar Grupo** é usado para selecionar um ou mais grupos da caixa de diálogo de seleção de grupo ao qual a regra de acesso se aplicará. Essa caixa de diálogo também pode ser usada para remover os grupos selecionados. Quando as regras são selecionadas para serem aplicadas aos Grupos, as regras de acesso só serão impostas para os usuários que pertençam a um dos grupos de segurança especificados.

Os grupos de segurança também podem ser excluídos explicitamente da política selecionado a opção **Exceto** e especificando um ou mais grupos. Usuários que são membros de um grupo na lista **Exceto** não estarão sujeitos ao requisito de autenticação multifator, mesmo se forem membros de um grupo ao qual a regra de acesso se aplica.
A regra de acesso mostrada abaixo exige que todos os usuários no grupo de Gerentes usem a autenticação multifator ao acessar o aplicativo.

![Configurando regras de acesso condicional com MFA](./media/active-directory-conditional-access-azuread-connected-apps/conditionalaccess-saas-apps.png)

## <a name="conditional-access-rules-with-mfa"></a>Regras de acesso condicional com MFA
Se um usuário tiver sido configurado usando o recurso de autenticação multifator por usuário, essa configuração no usuário será combinada com as regras de autenticação multifator do aplicativo. Isso significa que, para um usuário que foi configurado por uma autenticação multifator por usuário, será necessário executar a autenticação multifator, mesmo que eles tenham sido isentos das regras de autenticação multifator do aplicativo. Saiba mais sobre autenticação multifator e configurações por usuário.

### <a name="access-rule-options"></a>Opções de regra de acesso
Há suporte para as seguintes opções:

* **Exigir autenticação multifator**: os usuários aos quais as regras de acesso se aplicam precisarão concluir a autenticação multifator antes de acessar o aplicativo ao qual a política se aplica.
* **Exigir autenticação multifator quando não estiver no trabalho**: um usuário proveniente de um endereço IP confiável não precisará realizar a autenticação multifator. Os intervalos de endereços IP confiáveis podem ser configurados na página de configurações de multi-factor authentication.
* **Bloquear o acesso quando não estiver no trabalho**: um usuário que não vem de um endereço IP confiável será bloqueado. Os intervalos de endereços IP confiáveis podem ser configurados na página de configurações de multi-factor authentication.

### <a name="setting-rule-status"></a>Status da regra de configuração
O status da regra de acesso permite ativar ou desativar as regras. Quando as regras de acesso estão desativadas, o requisito de autenticação multifator não será imposto.

### <a name="access-rule-evaluation"></a>Avaliação da regra de acesso
Regras de acesso são avaliadas quando um usuário acessa um aplicativo federado que usa OAuth 2.0, OpenID Connect, SAML ou WS-Federation. Além disso, as regras de acesso são avaliadas quando o OAuth 2.0 e o OpenID conectam, quando um token de atualização é usado para adquirir um token de acesso. Se a avaliação da política falhar quando um token de atualização for usado, será retornado o erro **invalid_grant**, indicando que o usuário deverá ser autenticado novamente para o cliente.

### <a name="configure-federation-services-to-provide-multi-factor-authentication"></a>Configurar serviços de federação para fornecer Multi-Factor Authentication
Para locatários federados, a MFA pode ser executada pelo Azure Active Directory ou pelo servidor AD FS local.

Por padrão, a MFA ocorrerá em uma página hospedada pelo Active Directory do Azure. Para configurar o MFA localmente, a propriedade –**SupportsMFA** deve ser definida como **true** no Azure Active Directory usando o módulo Azure AD para Windows PowerShell.

O exemplo a seguir mostra como habilitar a MFA local usando o [cmdlet Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) no locatário contoso.com:

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

Além de definir esse sinalizador, a instância do AD FS do locatário federado deve ser configurada para realizar a autenticação multifator. Siga as instruções para [implantar a Autenticação Multifator do Azure localmente](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="related-articles"></a>Artigos relacionados
* [Proteger o acesso ao Office 365 e a outros aplicativos conectados ao Active Directory do Azure](active-directory-conditional-access.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)


