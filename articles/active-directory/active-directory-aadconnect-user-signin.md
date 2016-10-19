<properties
	pageTitle="Azure AD Connect: entrada do usuário | Microsoft Azure"
	description="Entrada do usuário do Azure AD Connect para configurações personalizadas."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="billmath"/>



# Opções de logon único de usuário do Azure AD Connect

O Azure AD Connect permite que os usuários façam logon em recursos de nuvem e locais usando as mesmas senhas. Você pode escolher entre várias maneiras diferentes para habilitar isso.

## Escolher um método de entrada do usuário
Para a maioria das organizações que desejam apenas habilitar o usuário a fazer logon no Office 365, aplicativos SaaS e outros recursos com base no AD do Azure, a opção de sincronização de senha padrão é recomendável. No entanto, algumas organizações têm motivos específicos para usar uma opção de logon federado como o AD FS. Estão incluídos:

- Sua organização já tem o AD FS ou um provedor de federação de terceiros implantado
- Sua política de segurança proíbe a sincronização de hashes de senha para a nuvem
- Você precisa que os usuários obtenham SSO direto (sem prompts de senha adicional) ao acessar recursos de nuvem de computadores associados ao domínio na rede corporativa
- Você precisa de alguns recursos específicos que do AD FS tem
	- Autenticação multifator local usando um provedor de terceiros ou cartões inteligentes (saiba mais sobre provedores MFA de terceiros para o AD FS no Windows Server 2012 R2)
	- Recursos de integração do Active Directory, como o bloqueio de conta suave ou a política de horas de trabalho e senha do AD
	- Acesso condicional para recursos locais e de nuvem usando o registro de dispositivos, a associação de AD do Azure ou políticas do Intune MDM

### Sincronização de senha
Com a sincronização de senha, hashes de senhas de usuário são sincronizados do seu Active Directory local para o AD do Azure. Quando as senhas são alteradas ou redefinidas no local, as novas senhas são sincronizadas imediatamente com o AD do Azure para que os usuários sempre possam usar a mesma senha para recursos de nuvem e recursos locais. As senhas nunca são enviadas ao AD do Azure nem armazenadas no AD do Azure em texto não criptografado. A sincronização de senha pode ser usada em conjunto com write-back de senha para habilitar a redefinição de senha de autoatendimento no AD do Azure.

<center>![Nuvem](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[Mais informações sobre a sincronização de senha](active-directory-aadconnectsync-implement-password-synchronization.md)


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

## Entrada do usuário e nome UPN

### Entendendo o nome UPN

No Active Directory, o sufixo UPN padrão é o nome DNS do domínio no qual a conta de usuário foi criada. Na maioria dos casos, esse é o nome de domínio registrado como o domínio corporativo na Internet. No entanto, você pode adicionar mais sufixos UPN usando o Domínios e Confianças do Active Directory.

O UPN do usuário está no formato username@domai. Por exemplo, para contoso.com do Active Dirctory, o usuário João pode ter o UPN john@contoso.com. O UPN do usuário baseia-se em RFC 822. Embora o UPN e o email compartilhem o mesmo formato, o valor do UPN para um usuário pode ou não ser igual ao endereço de email do usuário.

### Nome UPN no Azure AD

O assistente do Azure AD Connect vai usar o atributo do userPrincipalName ou permite especificar o atributo (na instalação personalizada) para ser usado de locais como o nome UPN no Azure AD. Esse é o valor que será usado para entrar no Azure AD. Se o valor do atributo do nome UPN não corresponder a um domínio verificado no Azure AD, este o substituirá por um valor padrão .onmicrosoft.com.

Cada diretório no Azure Active Directory vem com um nome de domínio interno no formato contoso.onmicrosoft.com, que permite começar a usar o Azure ou outros serviços da Microsoft. Você pode melhorar e simplificar a experiência de entrada usando domínios personalizados. Para obter informações sobre nomes de domínio personalizados no Azure AD e como verificar um domínio, leia [Adicionar seu nome de domínio personalizado ao Azure Active Directory](active-directory-add-domain.md#add-your-custom-domain-name-to-azure-active-directory)

## Configuração de entrada do Azure AD

### Configuração de entrada do Azure AD entrar com o Azure AD Connect
A experiência de entrada do Azure AD depende de Azure AD ser capaz de corresponder o sufixo do nome UPN de um usuário que está sendo sincronizado com um dos domínios personalizados verificados no diretório do Azure AD. O Azure AD Connect fornece ajuda a definir as configuração de entrada no Azure AD, para que a experiência de entrada do usuário na nuvem seja semelhante à experiência local. O Azure AD Connect lista os sufixos UPN definidos para os domínios e tenta correspondê-los com um domínio personalizado do Azure AD e ajuda a realizar as ações que precisam ser tomadas. A página de entrada do Azure AD lista os sufixos UPN definidos para o Active Directory local e exibe o status correspondente em cada sufixo. Os valores de status podem ser um dos seguintes:

* Verificado: o Azure AD Connect encontrou uma correspondência de domínio verificado no Azure AD e nenhuma ação é necessária
* Não verificado: o Azure AD Connect encontrou uma correspondência de domínio personalizado, mas ele não é verificado. O usuário deve verificar o domínio personalizado para garantir que o sufixo UPN dos usuários não seja alterado para o sufixo padrão .onmicrosoft.com após a sincronização.
* Não adicionado: o Azure AD Connect não encontrou um domínio personalizado correspondente com o sufixo UPN. O usuário deve adicionar e verificar o domínio personalizado correspondente ao sufixo UPN para garantir que o sufixo UPN dos usuários não seja alterado para o sufixo padrão .onmicrosoft.com após a sincronização.

A página de entrada do Azure AD lista os sufixos UPN definidos para o Active Directory local e o domínio personalizado correspondente no Azure AD com o status de verificação atual. Em uma instalação personalizada, agora você pode selecionar o atributo para o nome UPN na página **entrar no Azure AD**.

![Página de entrada do Azure AD](.\media\active-directory-aadconnect-user-signin\custom_azure_sign_in.png)

Você pode clicar no botão atualizar para buscar novamente o status mais recente dos domínios personalizados do Azure AD.

###Selecionar o atributo para o nome UPN no Azure AD

UserPrincipalName - o atributo userPrincipalName é o atributo que os usuários usarão ao entrarem no Azure AD e no Office 365. Os domínios usados, também conhecidos como sufixo UPN, devem ser verificados no AD do Azure antes que os usuários sejam sincronizados. É altamente recomendável manter o atributo padrão userPrincipalName. Se esse atributo não for roteável e se não puder ser verificado, será possível selecionar outro atributo, como email, por exemplo, como o atributo com a ID de entrada. Isso é conhecido como ID Alternativa. O valor do atributo da ID Alternativa deve seguir o padrão RFC822. Uma ID alternativa pode ser usada com SSO de senha e SSO de federação como solução de logon.

>[AZURE.NOTE] Usar uma ID alternativa não é compatível com todas as cargas de trabalho do Office 365. Para obter mais informações, veja [Configurando ID de logon alternativa](https://technet.microsoft.com/library/dn659436.aspx).

#### Estados diferentes de domínio personalizado e o efeito sobre a experiência de entrada do Azure
É muito importante entender a relação entre os estados de domínio personalizado em seu diretório do Azure AD e os sufixos UPN definido localmente. Examinaremos as diferentes experiências de entrada no Azure possível ao configurar a sincronização usando o Azure AD Connect.

Para as informações a seguir, suponhamos que estamos preocupados com o sufixo UPN contoso.com que é usado no diretório local como parte do UPN, por exemplo user@contoso.com.

###### Configurações Expressas/Sincronização de Senha
| Estado | Efeito sobre a experiência de entrada do usuário do Azure |
|:-------------:|:----------------------------------------|
| Não adicionado | Nesse caso, nenhum domínio personalizado para contoso.com foi adicionado no diretório do Azure AD. Os usuários que têm o UPN local com o sufixo @contoso.com não poderão usar seus UPNs locais para entrar no Azure. Em vez disso, eles precisarão usar um novo UPN fornecido pelo Azure AD adicionando o sufixo ao diretório do Azure AD padrão. Por exemplo, se você estiver sincronizando usuários com o diretório do Azure AD azurecontoso.onmicrosoft.com, um UPN de user@azurecontoso.onmicrosoft.com será atribuído ao usuário local user@contoso.com|
| Não verificado | Nesse caso, temos um domínio personalizado contoso.com adicionado ao diretório do Azure AD, mas ainda não verificado. Se você prosseguir com a sincronização dos usuários sem verificar o domínio, os usuários serão atribuídos a um novo UPN pelo Azure AD, exatamente como no cenário “Não adicionado”.|
| Verificado | Nesse caso, temos um domínio personalizado contoso.com já adicionado e verificado no Azure AD para o sufixo UPN. Os usuários poderão usar o nome UPN local, por exemplo, user@contoso.com, para entrar no Azure depois de serem sincronizados com o Azure AD|

###### Federação do AD FS
Não é possível criar uma federação com o domínio padrão .onmicrosoft.com no Azure AD ou um domínio personalizado não verificado no Azure AD. Ao executar o Assistente do Azure AD Connect, se você selecionar um domínio não verificado para a criação de uma federação, o Azure AD Connect solicitará que os registros necessários sejam criados no local em que o DNS está hospedado para o domínio. Para obter mais informações, clique [aqui](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se você selecionou a opção entrada Usuário como "Federação com o AD FS", deverá ter um domínio personalizado para prosseguir com a criação de uma federação no Azure AD. Para nossa discussão, isso significa que devemos ter um domínio personalizado contoso.com adicionado ao diretório do Azure AD.

| Estado | Efeito sobre a experiência de entrada do usuário do Azure |
|:-------------:|:----------------------------------------|
| Não adicionado | Nesse caso, o Azure AD Connect não pode localizar um domínio personalizado para o sufixo UPN contoso.com correspondente no diretório do Azure AD. Você precisará adicionar um domínio personalizado contoso.com se precisar que os usuários entrem usando o AD FS com seus UPNs locais, como user@contoso.com.|
| Não verificado | Nesse caso, o Azure AD Connect avisará os detalhes adequados sobe como verificar seu domínio em um estágio posterior|
| Verificado | Nesse caso, prossiga com a configuração sem qualquer ação adicional|  

## Alterar o método de entrada do usuário

Você pode alterar o método de entrada do usuário de Federação para Sincronização de Senha usando as tarefas disponíveis no Azure AD Connect após a configuração inicial do Azure AD Connect usando o assistente. Execute o assistente do Azure AD Connect novamente e você verá uma lista de tarefas que podem ser executadas. Selecione **Alterar a entrada do usuário** na lista de tarefas.

![Alterar a entrada do usuário](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Na próxima página, você deverá fornecer as credenciais para o Azure AD.

![Conecte-se ao AD do Azure](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

Na página **Entrada do usuário**, selecione **Sincronização de Senha**. Isso vai alterar o diretório de federado para gerenciado.

![Conecte-se ao AD do Azure](./media/active-directory-aadconnect-user-signin/changeusersignin3.png)

>[AZURE.NOTE] Se você estiver fazendo apenas uma chave temporária para a sincronização de senha, marque **Não converter contas de usuário**. Não marcar a opção levará à conversão de cada usuário federado, o que pode demorar várias horas.
  
## Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

Saiba mais sobre [Azure AD Connect: conceitos de design](active-directory-aadconnect-design-concepts.md)

<!---HONumber=AcomDC_0928_2016-->