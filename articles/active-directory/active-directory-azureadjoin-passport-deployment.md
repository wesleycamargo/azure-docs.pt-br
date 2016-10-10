<properties
	pageTitle="Habilitar o Microsoft Windows Hello for Business em sua organização |Microsoft Azure"
	description="Instruções de implantação para habilitar o Microsoft Passport na sua organização."
	services="active-directory"
	documentationCenter=""
	keywords="configurar o Microsoft Passport, implantação do Microsoft Windows Hello for Business"
	authors="markusvi"
	manager="femila"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2016"
	ms.author="femila;markvi"/>


# Habilitar o Microsoft Windows Hello for Business em sua organização

Depois de conectar os dispositivos integrados ao domínio do Windows 10 ao Azure Active Directory (Azure AD), faça o que se segue para habilitar o Microsoft Windows Hello for Business em sua organização.

## Implantar a ramificação atual do System Center Configuration Manager 
Para implantar certificados de usuário baseados em chaves do Windows Hello for Business, você precisa do seguinte:

- **Ramificação atual do System Center Configuration Manager**. Você precisa instalar a versão 1606 ou superior. Para saber mais, confira [Documentação do System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx) e [Blog da equipe do System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
- **PKI (infraestrutura de chave pública)**: para habilitar o Microsoft Windows Hello for Business usando certificados de usuário, você deve ter uma PKI em vigor. Se você não tiver um, ou não quiser usá-lo para certificados de usuário, poderá fazer isso:
 - **Implantar um controlador de domínio**: implante um novo controlador de domínio que tenha o Windows Server 2016 build 10551 (ou posterior) instalada e siga as etapas para [instalar um controlador de domínio de réplica em um domínio existente](https://technet.microsoft.com/library/jj574134.aspx) ou [instalar uma nova floresta do Active Directory, se você estiver criando um novo ambiente](https://technet.microsoft.com/library/jj574166). (Os ISOs estão disponíveis para download em [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).)

## Configurar o Microsoft Windows Hello for Business usando a Política de Grupo no Active Directory

 Você pode usar a Política de Grupo no Active Directory do Windows Server para configurar seus dispositivos Windows 10 ingressados no domínio para provisionar as credenciais do usuário do Windows Hello for Business na entrada do usuário no Windows:

1. 	Abra o Gerenciador do Servidor e navegue até **Ferramentas** > **Gerenciamento de Política de Grupo**.
2.	No Gerenciamento de Política de Grupo, navegue até o nó de domínio que corresponde ao domínio no qual você deseja habilitar o Ingresso no AD do Azure.
3.	Clique com o botão direito do mouse em **Objetos de Política de Grupo** e selecione **Novo**. Dê um nome ao seu Objeto de Política de Grupo, por exemplo, Habilitar o Windows Hello for Business. Clique em **OK**.
4.	Clique com o botão direito do mouse em seu novo Objeto de Política de Grupo e selecione **Editar**.
5.	Navegue até **Configuração do Computador** > **Políticas** > **Modelos Administrativos** > **Componentes do Windows** > **Windows Hello for Business**.
6.	Clique com o botão direito em **Habilitar o Windows Hello for Business** e, em seguida, selecione **Editar**.
7.	Selecione o botão de opção **Habilitado** e clique em **Aplicar**. Clique em **OK**.
8.	Agora você pode vincular o Objeto de Política de Grupo para um local de sua escolha. Para habilitar essa política para todos os dispositivos do Windows 10 associados ao domínio em sua organização, vincule a Política de Grupo ao domínio. Por exemplo:
 - Uma UO (unidade organizacional) específica no Active Directory onde os computadores ingressados no domínio do Windows 10 estejam localizados.
 - Um grupo de segurança específico com computadores ingressados no domínio do Windows 10 que serão registrados automaticamente no AD do Azure.


## Configurar o Windows Hello for Business usando o System Center Configuration Manager

Você só pode usar o System Center Configuration Manager para implantar políticas do Windows Hello for Business para Windows 10 versão 1607. Inicie o winver em seu computador com Windows 10 se você precisar pesquisar a versão de seu computador com Windows 10.

1. Abra o **System Center Configuration Manager** e, em seguida, navegue até **Ativos e Conformidade > Configurações de Conformidade > Acesso a Recursos da Empresa > Perfis do Windows Hello for Business**.

	![Configurar o Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/01.png)


2. Na barra de ferramentas na parte superior, clique em **Criar perfil Windows Hello for Business**.

	![Configurar o Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/02.png)

2. Na caixa de diálogo **Geral**, execute as seguintes etapas:

	![Configurar o Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/03.png)

	a. Na caixa de texto **Nome**, digite um nome para seu perfil, por exemplo, **Meu Perfil WHfB**.

	b. Clique em **Avançar**.


2. Na caixa de diálogo **Plataformas com Suporte**, selecione as plataformas que serão provisionadas com esse perfil do Windows Hello for Business e, em seguida, clique em **Próximo**.

	![Configurar o Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/04.png)


2. Na caixa de diálogo **Configurações**, execute as seguintes etapas:

	![Configurar o Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/05.png)

	a. Para **Configurar o Windows Hello for Business**, selecione **Habilitado**.

	b. Para **Usar um TPM (Trusted Platform Module)**, selecione **Obrigatório**.

	c. Para **Método de autenticação", selecione ** Baseado em certificado**.

	d. Clique em **Avançar**.



2. Na página de **Resumo**, clique em **Avançar**.

2. Na caixa de diálogo **Conclusão**, clique em **Fechar**.


2. Na barra de ferramentas na parte superior, clique em **Implantar**.

	![Configurar o Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/06.png)



## Configurar o perfil de certificado para usar o certificado de registro do Windows Hello for Business no Configuration Manager


Se você estiver usando a autenticação baseada em certificado para autenticação local, você precisará configurar e implantar um perfil de certificado. Essa tarefa exige que você configure um servidor NDES e a função de site do Ponto de Registro de Certificado no System Center Configuration Manager. Para obter mais detalhes, consulte os [Pré-requisitos para perfis de Certificado no Configuration Manager](https://technet.microsoft.com/library/dn261205.aspx).

1. Abra o **System Center Configuration Manager** e, em seguida, navegue até **Ativos e Conformidade > Configurações de Conformidade > Acesso a Recursos da Empresa > Perfis de Certificado**.


2. Selecione um modelo que tenha EKU (uso estendido da chave) para entrada com cartão inteligente.

Na página **Registro do SCEP** do perfil de certificado, você precisa escolher **Instalar Passport for Work se houver falha** como o **Provedor de Armazenamento de Chave**.



## Próximas etapas
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Autenticando identidades sem senhas com o Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0928_2016-->