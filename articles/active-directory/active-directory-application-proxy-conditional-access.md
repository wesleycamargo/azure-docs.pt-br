<properties
	pageTitle="Acesso condicional para Aplicativos Publicados com o Proxy de Aplicativo do AD do Azure"
	description="Aborda como configurar o acesso condicional para os aplicativos que você publica para serem acessados remotamente usando o Proxy de Aplicativo do AD do Azure."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/09/2016"
	ms.author="kgremban"/>

# Trabalhando com acesso condicional
> [AZURE.NOTE] O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).

Você pode configurar regras de acesso para conceder acesso condicional a aplicativos publicados usando o Proxy de Aplicativo. Isso permite a você:

- Exigir a autenticação multifator por aplicativo
- Exigir a autenticação multifator somente quando os usuários não estiverem no trabalho
- Impedir que os usuários acessem o aplicativo quando eles não estiverem no trabalho

Essas regras podem ser aplicadas a todos os usuários e grupos ou apenas a usuários e grupos específicos. Por padrão, a regra será aplicada a todos os usuários que têm acesso ao aplicativo. No entanto, a regra também pode ser restrita a usuários que sejam membros de grupos de segurança especificados.

Regras de acesso são avaliadas quando um usuário acessa um aplicativo federado que usa OAuth 2.0, OpenID Connect, SAML ou WS-Federation. Além disso, as regras de acesso são avaliadas com OAuth 2.0 e o OpenID Connect quando um token de atualização é usado para adquirir um token de acesso.

## Pré-requisitos de acesso condicional

- Assinatura no Active Directory Premium do Azure
- Um locatário do Active Directory do Azure federado ou gerenciado
- Inquilinos federados exigem que o MFA (Multi-Factor Authentication) esteja habilitado ![Configurar regras de acesso - exigir Multi-Factor Authentication](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## Configure a multi-factor authentication por aplicativo
1. Entre como administrador no portal clássico do Azure.
2. Vá para o Active Directory e selecione o diretório no qual você deseja habilitar o Proxy de aplicativo.
3. Clique em **Aplicativos** e role para baixo até a seção **Regras de Acesso**. A seção de regras de acesso será mostrada apenas para aplicativos publicados usando o Proxy de Aplicativo que usem autenticação federada.
4. Habilitar a regra selecionando **Habilitar Regras de Acesso** para **Ativado**.
5. Especifique os usuários e grupos para os quais as regras serão aplicadas. Use o botão **Adicionar Grupo** para selecionar um ou mais grupos aos quais a regra de acesso será aplicada. Essa caixa de diálogo também pode ser usada para remover os grupos selecionados. Quando as regras são selecionadas para serem aplicadas aos grupos, as regras de acesso só serão impostas para os usuários que pertençam a um dos grupos de segurança especificados.  

  - Para excluir explicitamente grupos de segurança da regra, marque **Exceto** e especifique um ou mais grupos. Os usuários que são membros de um grupo na lista Exceto não serão solicitados a realizar a multi-factor authentication.  

  - Se um usuário tiver sido configurado usando o recurso de autenticação multifator por usuário, essa configuração terá precedência sobre as regras de autenticação multifator do aplicativo. Isso significa que, para um usuário que foi configurado por uma multi-factor authentication por usuário, será necessário executar a multi-factor authentication, mesmo que eles tenham sido isentos das regras de multi-factor authentication do aplicativo. Saiba mais sobre a [autenticação multifator e as configurações por usuário](../multi-factor-authentication/multi-factor-authentication.md).

6. Selecione a regra de acesso que você deseja definir:
	- **Requer autenticação multifator**: os usuários aos quais se aplicam as regras de acesso deverão concluir a autenticação multifator antes de acessar o aplicativo ao qual a regra se aplica.
	- **Requer autenticação multifator quando não está no trabalho**: os usuários que tentarem acessar o aplicativo a partir de um endereço IP confiável não precisarão realizar a autenticação multifator. Os intervalos de endereços IP confiáveis podem ser configurados na página de configurações de multi-factor authentication.
	- **Bloquear o acesso quando não estiver no trabalho**: Os usuários que tentarem acessar o aplicativo de fora da rede corporativa não poderão acessar o aplicativo.


## Configurar a MFA para serviços de federação
Para locatários federados, a MFA (Multi-Factor Authentication) pode ser executada pelo Active Directory do Azure ou pelo servidor AD FS local. Por padrão, a MFA ocorrerá em qualquer página hospedada pelo Active Directory do Azure. Para configurar a MFA local, execute o Windows PowerShell e use a propriedade –SupportsMFA para definir o módulo do AD do Azure.

O exemplo a seguir mostra como habilitar a MFA local usando o [cmdlet Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) no locatário contoso.com: `Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

Além de definir esse sinalizador, a instância do AD FS do locatário federado deve ser configurada para realizar a autenticação multifator. Siga as instruções para [implantar o Microsoft Azure Multi-Factor Authentication local](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).


## Consulte também

- [Trabalho com aplicativos com reconhecimento de declaração](active-directory-application-proxy-claims-aware-apps.md)
- [Publique aplicativos com proxy de aplicativo](active-directory-application-proxy-publish.md)
- [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Publicar aplicativos usando seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)

Para ver as últimas notícias e atualizações, confira o [blog Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)

<!---HONumber=AcomDC_0511_2016-->