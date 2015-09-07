<properties
	pageTitle="Visualização de Acesso Condicional do Azure para Aplicativos SaaS| Microsoft Azure"
	description="O acesso condicional no AD do Azure permite que você configure as regras de acesso de autenticação multifator por aplicativo e a capacidade de bloquear o acesso de usuários que não estão em uma rede confiável."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="femila"/>

# Visualização de acesso condicional do Azure para aplicativos SaaS

O Acesso Condicional do Azure para aplicativos SaaS está disponível para visualização pública. A visualização permite que você configure as regras de acesso de autenticação multifator por aplicativo e a capacidade de bloquear o acesso de usuários que não estão em uma rede confiável.

Você pode aplicar as regras de autenticação multifator a todos os usuários atribuídos ao aplicativo ou apenas aos usuários nos grupos de segurança especificados. Os usuários podem ser excluídos do requisito de autenticação multifator se estiverem acessando o aplicativo de um endereço IP de dentro da rede da organização. Esses recursos estarão disponíveis para os clientes que compraram uma licença Azure Active Directory Premium.

## Pré-requisitos do cenário
* Assinatura no Active Directory Premium do Azure

* Um locatário do Active Directory do Azure federado ou gerenciado

* Inquilinos federados exigem que MFA (Multi-Factor Authentication) esteja habilitada.

## Problemas conhecidos nesta versão de Visualização
Essa visualização se aplica a aplicativos SaaS federados pré-integrados, aplicativos que usam o logon único de senha, aplicativos de linha de negócios, aplicativos desenvolvidos e registrados, e o Proxy de Aplicativo do AD do Azure. Alguns aplicativos adicionais ainda estão sendo habilitados.

##Configurar regras de acesso por aplicativo

Esta seção descreve como configurar as regras de acesso por aplicativo.

1. Faça logon no Portal do Microsoft Azure como um Administrador.
2. No painel esquerdo, selecione **Active Directory**.
3. Na guia Diretório, selecione o diretório.
4. Selecione a guia **Aplicativos**.
5. Selecione o aplicativo que será definido para a regra.
6. Selecione a guia **Configurar**.
7. Role para baixo até a seção de regras de acesso. Selecione a regra de acesso desejada.
8. Especifique os usuários aos quais a regra será aplicada.
9. Habilite a política selecionando **Habilitado para Ativação**.

##Noções básicas de regras de acesso

Esta seção fornece uma descrição detalhada das regras de acesso com suporte na Visualização de Acesso do Aplicativo Condicional do Azure.
### Especificar os usuários aos quais as regras de acesso se aplicam

Por padrão, a política será aplicada a todos os usuários que têm acesso ao aplicativo. No entanto, também é possível restringir a política aos usuários que são membros dos grupos de segurança especificados. O botão **Adicionar Grupo** é usado para selecionar um ou mais grupos da caixa de diálogo de seleção de grupo ao qual a regra de acesso se aplicará. Essa caixa de diálogo também pode ser usada para remover os grupos selecionados. Quando as regras são selecionadas para serem aplicadas aos Grupos, as regras de acesso só serão impostas para os usuários que pertençam a um dos grupos de segurança especificados.

Os grupos de segurança também podem ser excluídos explicitamente da política selecionado a opção Exceto e especificando um ou mais grupos. Usuários que são membros de um grupo na lista Exceto não estarão sujeitos ao requisito de autenticação multifator, mesmo se forem membros de um grupo ao qual a regra de acesso se aplica. A regra de acesso mostrada abaixo exige que todos os usuários no grupo de Gerentes usem a autenticação multifator ao acessar o aplicativo.

![Configurando regras de acesso condicional com MFA](./media/active-directory-conditional-access/conditionalaccess-saas-apps.jpg)

##Regras de acesso condicional com MFA
Se um usuário tiver sido configurado usando o recurso de autenticação multifator por usuário, essa configuração terá precedência sobre as regras de autenticação multifator do aplicativo. Isso significa que, para um usuário que foi configurado por uma autenticação multifator por usuário, será necessário executar a autenticação multifator, mesmo que eles tenham sido isentos das regras de autenticação multifator do aplicativo. Saiba mais sobre autenticação multifator e configurações por usuário.

###Opções de regra de acesso
A visualização atual dá suporte às seguintes opções:

* **Exigir Multi-Factor Authentication**: com essa opção, os usuários aos quais as regras de acesso se aplicam precisarão concluir a autenticação multifator antes de acessar o aplicativo ao qual a política se aplica.

* **Exigir Multi-Factor Authentication quando não estiver no trabalho**: com essa opção, um usuário proveniente de um IP confiável não precisará realizar a autenticação multifator. Os intervalos IP confiáveis podem ser configurados na página de configurações de autenticação multifator

* **Bloquear o acesso quando não estiver no trabalho**: com essa opção, um usuário que não vêm de um IP confiável será bloqueado. Os intervalos IP confiáveis podem ser configurados na página de configurações de autenticação multifator.

###Status da regra de configuração
O status da regra de acesso permite ativar ou desativar as regras. Quando as regras de acesso estão desativadas, o requisito de autenticação multifator não será imposto.

### Avaliação da regra de acesso

Regras de acesso são avaliadas quando um usuário acessa um aplicativo federado que usa OAuth 2.0, OpenID Connect, SAML ou WS-Federation. Além disso, as regras de acesso são avaliadas quando o OAuth 2.0 e o OpenID conectam, quando um token de atualização é usado para adquirir um token de acesso. Se a avaliação da política falhar quando um token de atualização for usado, será retornado o erro invalid\_grant, indicando que o usuário precisa ser autenticado novamente para o cliente. Configurar serviços de federação para fornecer Multi-Factor Authentication

Para locatários federados, a multi-factor authentication (MFA) pode ser executada pelo Active Directory do Azure ou pelo local do servidor AD FS.

Por padrão, a MFA ocorrerá em uma página hospedada pelo Active Directory do Azure. Para configurar o MFA localmente, a propriedade – SupportsMFA deve ser definida como true no Active Directory do Azure, usando o módulo AD do Azure para Windows PowerShell.

O exemplo a seguir mostra como habilitar a MFA local usando o [cmdlet Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) no locatário contoso.com:

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

Além de definir esse sinalizador, a instância do AD FS do locatário federado deve ser configurada para realizar a autenticação multifator. Siga as instruções para implantar a Azure Multi-Factor Authentication local.

<!---HONumber=August15_HO9-->