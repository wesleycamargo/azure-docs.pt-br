---
title: "Azure AD Connect: entrada do usuário | Microsoft Docs"
description: "Entrada do usuário do Azure AD Connect para configurações personalizadas."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 4fbe7bd802e9cc32d43f019980650c4723b75d5f
ms.openlocfilehash: 7e821117e62eda286cefb59a5ded85b2f99f3ef7


---
# <a name="azure-ad-connect-user-sign-on-options"></a>Opções de logon único de usuário do Azure AD Connect
O Azure AD Connect permite que os usuários façam logon em recursos de nuvem e locais usando as mesmas senhas. Este artigo descreve os conceitos principais para cada modelo de identidade a fim de ajudá-lo a escolher a identidade que deseja usar para entrar no Azure AD.

Se você já estiver familiarizado com o modelo de identidade do Azure AD e quiser saber mais sobre um método específico, basta clicar abaixo no tópico apropriado.

* [Sincronização de senha](#password-synchronization) com [SSO (Logon único)](active-directory-aadconnect-sso.md)
* [Autenticação de passagem](active-directory-aadconnect-pass-through-authentication.md)
* [SSO federado (com ADFS)](#federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm)





##<a name="choosing-the-user-sign-in-method-for-your-organization"></a>Escolhendo um método de entrada do Usuário para a sua organização
Para a maioria das organizações que desejam apenas habilitar o usuário a fazer logon no Office 365, aplicativos SaaS e outros recursos com base no AD do Azure, a opção de sincronização de senha padrão é recomendável. Algumas organizações, entretanto, têm motivos específicos pelos quais não podem usar essa opção e podem escolher a opção de logon federado como o AD FS ou a Autenticação de passagem. A tabela a seguir o ajuda a fazer a escolha certa. 

Eu preciso de | PHS e SSO| PTA com SSO| AD FS |
 --- | --- | --- | --- |
Sincronizar automaticamente na nuvem as contas de novo usuário, de contato e de grupo, criadas no meu Active Directory local|x|x|x|
Configurar meu locatário para cenários híbridos do Office 365|x|x|x|
Habilitar os usuários para entrar e acessar serviços de nuvem usando suas senhas locais|x|x|x|
Implementar o logon único usando credenciais corporativas|x|x|x|
Certificar-me de que as senhas não sejam armazenadas na nuvem||x*|x|
Habilitar soluções de autenticação multifator locais|||x|

*Através de um conector leve.

>[!NOTE] 
> A autenticação de passagem atualmente tem algumas limitações com clientes avançados.  Consulte [Autenticação de passagem](active-directory-aadconnect-pass-through-authentication.md) para obter mais detalhes.

### <a name="password-synchronization"></a>Sincronização de senha
Com a sincronização de senha, hashes de senhas de usuário são sincronizados do seu Active Directory local para o AD do Azure.  Quando as senhas são alteradas ou redefinidas no local, as novas senhas são sincronizadas imediatamente com o AD do Azure para que os usuários sempre possam usar a mesma senha para recursos de nuvem e recursos locais.  As senhas nunca são enviadas ao AD do Azure nem armazenadas no AD do Azure em texto não criptografado.  A sincronização de senha pode ser usada em conjunto com write-back de senha para habilitar a redefinição de senha de autoatendimento no AD do Azure.

Além disso, você também pode habilitar o [SSO (Logon único)](active-directory-aadconnect-sso.md) para usuários em computadores ingressados no domínio, que estejam na rede corporativa. Com o logon único, os usuários habilitados só precisarão inserir um nome de usuário para acessar com segurança os recursos de nuvem.

![Nuvem](./media/active-directory-aadconnect-user-signin/passwordhash.png)

[Mais informações sobre a sincronização de senha](active-directory-aadconnectsync-implement-password-synchronization.md)

### <a name="pass-through-authentication"></a>Autenticação de Passagem
Com a autenticação de passagem, a senha do usuário é validada no controlador do Active Directory local e a senha não precisa estar presente no Azure AD de nenhuma forma. Isso permite que as políticas locais sejam avaliadas durante a autenticação em serviços de nuvem, como restrições de horário de logon. A autenticação de passagem utiliza um agente simples em um computador Windows Server 2012 R2 ingressado no domínio no ambiente local, que escuta solicitações de validação de senha. Esse agente não requer que nenhuma porta de entrada seja aberta para a Internet.

Além disso, você também pode habilitar o logon único para usuários em computadores ingressados no domínio, que estejam na rede corporativa. Com o logon único, os usuários habilitados só precisarão inserir um nome de usuário para acessar com segurança os recursos de nuvem.
![Autenticação de passagem](./media/active-directory-aadconnect-user-signin/pta.png)

[Mais informações sobre autenticação de passagem](active-directory-aadconnect-pass-through-authentication.md) e [Logon único](active-directory-aadconnect-sso.md).

### <a name="federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm"></a>Federação usando um AD FS novo ou existente no farm do Windows Server 2012 R2
Com o logon federado, os usuários podem acessar serviços baseados no Azure AD com suas senhas locais e, enquanto permanecerem na rede corporativa, nem precisam digitar suas senhas.  A opção de federação com o AD FS permite que você implante um novo AD FS ou especifique um existente em um farm do Windows Server 2012 R2.  Se você optar por especificar um farm existente, o Azure AD Connect configurará a relação de confiança entre seu farm e o AD do Azure para que os usuários possam entrar.

<center>![Nuvem](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="to-deploy-federation-with-ad-fs-in-windows-server-2012-r2-you-will-need-the-following"></a>Para implantar a federação com o AD FS no Windows Server 2012 R2, você precisará do seguinte
Se você estiver implantando um novo farm:

* Um servidor Windows Server 2012 R2 para o servidor de federação.
* Um servidor Windows Server 2012 R2 para o proxy de aplicativo Web.
* Um arquivo .pfx com um certificado SSL para o nome do serviço de federação pretendido, por exemplo, fs.contoso.com.

Se você estiver implantando um novo farm ou usando um farm existente:

* Credenciais de administrador local nos servidores de federação.
* Credenciais de administrador local em qualquer servidor de grupo de trabalho (sem ingresso no domínio) no qual você pretende implantar a função de Proxy de aplicativo Web.
* O computador no qual você executa o assistente deve ser capaz de conectar a outros computadores nos quais você queira instalar o AD FS ou o Proxy de aplicativo Web por meio do Gerenciamento remoto do Windows.

[Configurando o SSO com o AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)

#### <a name="sign-on-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Fazer logon usando uma versão anterior do AD FS ou uma solução de terceiros
Se já tiver configurado o logon na nuvem usando uma versão anterior do AD FS (como o AD FS 2.0) ou um provedor de federação de terceiros, você poderá optar por ignorar a configuração de logon do usuário via Azure AD Connect.  Isso o habilitará a obter a sincronização mais recente e outros recursos do Azure AD Connect enquanto estiver usando sua solução existente para o logon.

[Lista de compatibilidade de federação de terceiros com o Azure AD](active-directory-aadconnect-federation-compatibility.md)


## <a name="user-sign-in-and-user-principal-name-upn"></a>Entrada do usuário e nome UPN
### <a name="understanding-user-principal-name"></a>Entendendo o nome UPN
No Active Directory, o sufixo UPN padrão é o nome DNS do domínio no qual a conta de usuário foi criada. Na maioria dos casos, esse é o nome de domínio registrado como o domínio corporativo na Internet. No entanto, você pode adicionar mais sufixos UPN usando o Domínios e Confianças do Active Directory.

O UPN do usuário está no formato username@domain. Por exemplo, para um Domínio do Active Directory denominado "contoso.com", um usuário chamado João pode ter o UPN 'john@contoso.com'. O UPN do usuário baseia-se em RFC 822. Embora o UPN e o email compartilhem o mesmo formato, o valor do UPN para um usuário pode ou não ser igual ao endereço de email do usuário.

### <a name="user-principal-name-in-azure-ad"></a>Nome UPN no Azure AD
O assistente do Azure AD Connect vai usar o atributo do userPrincipalName ou permite especificar o atributo (na instalação personalizada) para ser usado de locais como o nome UPN no Azure AD. Esse é o valor que será usado para entrar no Azure AD. Se o valor do atributo do nome UPN não corresponder a um domínio verificado no Azure AD, este o substituirá por um valor padrão .onmicrosoft.com.

Cada diretório no Azure Active Directory vem com um nome de domínio interno no formato contoso.onmicrosoft.com, que permite começar a usar o Azure ou outros serviços da Microsoft. Você pode melhorar e simplificar a experiência de entrada usando domínios personalizados. Para obter informações sobre nomes de domínio personalizados no Azure AD e como verificar um domínio, leia [Adicionar seu nome de domínio personalizado ao Azure Active Directory](../active-directory-add-domain.md#add-a-custom-domain-name-to-your-directory)

## <a name="azure-ad-sign-in-configuration"></a>Configuração de entrada do Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Configuração de entrada do Azure AD entrar com o Azure AD Connect
A experiência de entrada do Azure AD depende de Azure AD ser capaz de corresponder o sufixo do nome UPN de um usuário que está sendo sincronizado com um dos domínios personalizados verificados no diretório do Azure AD. O Azure AD Connect fornece ajuda a definir as configuração de entrada no Azure AD, para que a experiência de entrada do usuário na nuvem seja semelhante à experiência local. O Azure AD Connect lista os sufixos UPN definidos para os domínios e tenta correspondê-los com um domínio personalizado do Azure AD e ajuda a realizar as ações que precisam ser tomadas.
A página de entrada do Azure AD lista os sufixos UPN definidos para o Active Directory local e exibe o status correspondente em cada sufixo. Os valores de status podem ser um dos seguintes:

| Estado | Descrição | Ação necessária |
|:--- |:--- |:--- |
| Verificado |O Azure AD Connect encontrou uma correspondência de domínio verificado no Azure AD. Todos os usuários deste domínio podem entrar usando suas credenciais locais |Nenhuma ação é necessária |
| Não verificado |O Azure AD Connect encontrou uma correspondência de domínio personalizado, mas ele não é verificado. O sufixo UPN dos usuários desse domínio será alterado para o sufixo padrão .onmicrosoft.com após a sincronização se o domínio não for verificado. |Verifique o domínio personalizado no Azure AD. [Saiba mais](../active-directory-add-domain.md#verify-the-domain-name-with-azure-ad) |
| Não adicionado |O Azure AD Connect não encontrou um domínio personalizado correspondente com o sufixo UPN. O sufixo UPN dos usuários desse domínio será alterado para o sufixo padrão .onmicrosoft.com se o domínio não for verificado no Azure. |Adicionar e verificar um domínio personalizado correspondente ao sufixo UPN [Saiba mais](../active-directory-add-domain.md) |

A página de entrada do Azure AD lista os sufixos UPN definidos para o Active Directory local e o domínio personalizado correspondente no Azure AD com o status de verificação atual. Em uma instalação personalizada, agora você pode selecionar o atributo para o nome UPN na página **entrar no Azure AD** .

![Página de entrada do Azure AD](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

Você pode clicar no botão atualizar para buscar novamente o status mais recente dos domínios personalizados do Azure AD.

### <a name="selecting-attribute-for-user-principal-name-in-azure-ad"></a>Selecionar o atributo para o nome UPN no Azure AD
UserPrincipalName - o atributo userPrincipalName é o atributo que os usuários usarão ao entrarem no Azure AD e no Office 365. Os domínios usados, também conhecidos como sufixo UPN, devem ser verificados no AD do Azure antes que os usuários sejam sincronizados. É altamente recomendável manter o atributo padrão userPrincipalName. Se esse atributo não for roteável e se não puder ser verificado, será possível selecionar outro atributo, como email, por exemplo, como o atributo com a ID de entrada. Isso é conhecido como ID Alternativa. O valor do atributo da ID Alternativa deve seguir o padrão RFC822. Uma ID alternativa pode ser usada com SSO de senha e SSO de federação como solução de logon.

> [!NOTE]
> O uso de uma ID alternativa não é compatível com todas as cargas de trabalho do Office 365 e com a autenticação de passagem. Para obter mais informações, veja [Configurando ID de logon alternativa](https://technet.microsoft.com/library/dn659436.aspx).
> 
> 

#### <a name="different-custom-domain-states-and-effect-on-azure-sign-in-experience"></a>Estados diferentes de domínio personalizado e o efeito sobre a experiência de entrada do Azure
É muito importante entender a relação entre os estados de domínio personalizado em seu diretório do Azure AD e os sufixos UPN definido localmente. Examinaremos as diferentes experiências de entrada no Azure possível ao configurar a sincronização usando o Azure AD Connect.

Para as informações a seguir, suponhamos que estamos preocupados com o sufixo UPN contoso.com que é usado no diretório local como parte do UPN, por exemplo user@contoso.com.

###### <a name="express-settings--password-synchronization"></a>Configurações Expressas/Sincronização de Senha
| Estado | Efeito sobre a experiência de entrada do usuário do Azure |
|:---:|:--- |
| Não adicionado |Nesse caso, nenhum domínio personalizado para contoso.com foi adicionado no diretório do Azure AD. Os usuários que têm o UPN local com o sufixo @contoso.com, não poderão usar seus UPNs locais para entrar no Azure. Em vez disso, eles precisarão usar um novo UPN fornecido pelo Azure AD adicionando o sufixo ao diretório do Azure AD padrão. Por exemplo, se você estiver sincronizando usuários com o diretório do Azure AD azurecontoso.onmicrosoft.com, um UPN de user@azurecontoso.onmicrosoft.com será atribuído ao usuário local user@contoso.com |
| Não verificado |Nesse caso, temos um domínio personalizado contoso.com adicionado ao diretório do Azure AD, mas ainda não verificado. Se você prosseguir com a sincronização dos usuários sem verificar o domínio, os usuários serão atribuídos a um novo UPN pelo Azure AD, exatamente como no cenário “Não adicionado”. |
| Verificado |Nesse caso, temos um domínio personalizado contoso.com já adicionado e verificado no Azure AD para o sufixo UPN. Os usuários poderão usar o nome UPN local, por exemplo user@contoso.com,, para entrar no Azure depois de serem sincronizados com o Azure AD |

###### <a name="ad-fs-federation"></a>Federação do AD FS
Não é possível criar uma federação com o domínio padrão .onmicrosoft.com no Azure AD ou um domínio personalizado não verificado no Azure AD. Ao executar o Assistente do Azure AD Connect, se você selecionar um domínio não verificado para a criação de uma federação, o Azure AD Connect solicitará que os registros necessários sejam criados no local em que o DNS está hospedado para o domínio. Para obter mais informações, clique [aqui](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se você selecionou a opção entrada Usuário como "Federação com o AD FS", deverá ter um domínio personalizado para prosseguir com a criação de uma federação no Azure AD. Para nossa discussão, isso significa que devemos ter um domínio personalizado contoso.com adicionado ao diretório do Azure AD.

| Estado | Efeito sobre a experiência de entrada do usuário do Azure |
|:---:|:--- |
| Não adicionado |Nesse caso, o Azure AD Connect não pode localizar um domínio personalizado para o sufixo UPN contoso.com correspondente no diretório do Azure AD. Você precisará adicionar um domínio contoso.com personalizado se precisar que os usuários entrem usando o AD FS com seus UPNs locais, como user@contoso.com. |
| Não verificado |Nesse caso, o Azure AD Connect avisará os detalhes adequados sobe como verificar seu domínio em um estágio posterior |
| Verificado |Nesse caso, prossiga com a configuração sem qualquer ação adicional |

## <a name="changing-user-sign-in-method"></a>Alterar o método de entrada do usuário
Você pode alterar o método de entrada do usuário de Federação para Sincronização de Senha ou Autenticação de passagem, usando as tarefas disponíveis no Azure AD Connect após a configuração inicial do Azure AD Connect usando o assistente. Execute o assistente do Azure AD Connect novamente e você verá uma lista de tarefas que podem ser executadas. Selecione **Alterar a entrada do usuário** na lista de tarefas.

![Alterar a entrada do usuário](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Na próxima página, você deverá fornecer as credenciais para o Azure AD.

![Conecte-se ao AD do Azure](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

Na página **Entrada de usuário**, selecione a entrada do usuário desejada.

![Conecte-se ao AD do Azure](./media/active-directory-aadconnect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Se você estiver fazendo apenas uma chave temporária para a sincronização de senha, marque **Não converter contas de usuário**. Não marcar a opção levará à conversão de cada usuário federado, o que pode demorar várias horas.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

Saiba mais sobre [Azure AD Connect: conceitos de design](active-directory-aadconnect-design-concepts.md)




<!--HONumber=Feb17_HO2-->


