<properties 
	pageTitle="Azure AD Connect - entrada do usuário" 
	description="Entrada do usuário do Azure AD Connect para configurações personalizadas." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>



# Opções de logon único de usuário do Azure AD Connect

O Azure AD Connect permite que os usuários façam logon em recursos de nuvem e locais usando as mesmas senhas. Você pode escolher entre várias maneiras diferentes para habilitar isso.


### Sincronização de senha
Com a sincronização de senha, hashes de senhas de usuário são sincronizados do seu Active Directory local para o AD do Azure. Quando as senhas são alteradas ou redefinidas no local, as novas senhas são sincronizadas imediatamente com o AD do Azure para que os usuários sempre possam usar a mesma senha para recursos de nuvem e recursos locais. As senhas nunca são enviadas ao AD do Azure nem armazenadas no AD do Azure em texto não criptografado. A sincronização de senha pode ser usada em conjunto com write-back de senha para habilitar a redefinição de senha de autoatendimento no AD do Azure.

<center>![Nuvem](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[Mais informações sobre a sincronização de senha](https://msdn.microsoft.com/library/azure/dn246918.aspx)


### Federação usando um AD FS novo ou existente no farm do Windows Server 2012 R2
Com logon federada, os usuários podem acessar serviços do AD do Azure com base em com suas senhas locais e enquanto estão na rede corporativa, sem precisar digitar suas senhas novamente. A opção de federação com o AD FS permite que você implante um novo AD FS ou especifique um existente em um farm do Windows Server 2012 R2. Se você optar por especificar um farm existente, o Azure AD Connect configurará a relação de confiança entre seu farm e o AD do Azure para que os usuários possam entrar.

<center>![Nuvem](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### Para implantar a federação com o AD FS no Windows Server 2012 R2, você precisará do seguinte
Se você estiver implantando um novo farm:

- Um servidor Windows Server 2012 R2 para o servidor de federação.
- Um servidor Windows Server 2012 R2 para o proxy de aplicativo Web.
- Um arquivo .pfx com um certificado SSL para o nome do serviço de federação pretendido, por exemplo, fs.contoso.com.

Se você estiver implantando um novo farm ou usando um farm existente:

- Credenciais de administrador local nos servidores de federação.
- Credenciais de administrador local em qualquer servidor de grupo de trabalho (sem ingresso no domínio) no qual você pretende implantar a função de Proxy de aplicativo Web.
- O computador no qual você executa o assistente deve ser capaz de conectar a outros computadores nos quais você queira instalar o AD FS ou o Proxy de aplicativo Web por meio do Gerenciamento remoto do Windows.

#### Fazer logon usando uma versão anterior do AD FS ou uma solução de terceiros
Se já tiver configurado o logon na nuvem usando uma versão anterior do AD FS (como o AD FS 2.0) ou um provedor de federação de terceiros, você poderá optar por ignorar a configuração de logon do usuário via Azure AD Connect. Isso o habilitará a obter a sincronização mais recente e outros recursos do Azure AD Connect enquanto estiver usando sua solução existente para o logon.

### Escolhendo um método de logon do usuário para sua organização
Para a maioria das organizações que desejam apenas habilitar o usuário a fazer logon no Office 365, aplicativos SaaS e outros recursos com base no AD do Azure, a opção de sincronização de senha padrão é recomendável. No entanto, algumas organizações têm motivos específicos para usar uma opção de logon federado como o AD FS. Estão incluídos:

- Sua organização já tem o AD FS ou um provedor de federação de terceiros implantado
- Sua política de segurança proíbe a sincronização de hashes de senha para a nuvem
- Você precisa que os usuários obtenham SSO direto (sem prompts de senha adicional) ao acessar recursos de nuvem de computadores associados ao domínio na rede corporativa
- Você precisa de alguns recursos específicos que do AD FS tem
	- Autenticação multifator local usando um provedor de terceiros ou cartões inteligentes (saiba mais sobre provedores MFA de terceiros para o AD FS no Windows Server 2012 R2)
	- Recursos de integração do Active Directory, como o bloqueio de conta suave ou a política de horas de trabalho e senha do AD
	- Acesso condicional para recursos locais e de nuvem usando o registro de dispositivos, a associação de AD do Azure ou políticas do Intune MDM
 

<!---HONumber=August15_HO6-->