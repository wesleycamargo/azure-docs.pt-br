<properties
	pageTitle="Configurar um novo dispositivo com o AD do Azure durante a instalação | Microsoft Azure"
	description="Um tópico que explica como os usuários podem configurar a Junção do Azure AD durante sua experiência de primeira execução."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="femila"/>

# Configurar um novo dispositivo com o AD do Azure durante a instalação

No Windows 10, os usuários podem ingressar seus dispositivos no Azure AD (Azure Active Directory) na FRX (tela de apresentação). Isso permite que as organizações distribuam dispositivos reduzidos e encapsulados para seus funcionários ou alunos ou deixem que eles tenham uma experiência CYOD (escolha seus próprios dispositivos). Se o Windows 10 Professional ou Windows 10 Enterprise Edition estiver instalado em um dispositivo, a experiência usará como padrão o processo de instalação de dispositivos de propriedade da empresa.

## Para adicionar um dispositivo ao AD do Azure


1. Ao ligar o novo dispositivo e iniciar o processo de instalação, você deverá ver a mensagem **Preparando-se**. Siga os prompts para configurar o dispositivo.
2. Inicie personalizando a região e o idioma. Em seguida, aceite os Termos de Licença para Software Microsoft. 
![Personalizar a região](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. Selecione a rede que deseja usar para se conectar à Internet.
4. Selecione se está usando um dispositivo pessoal ou de propriedade da empresa. Se ele for de propriedade da empresa, clique em **Este dispositivo pertence à minha organização**. Isso inicia a experiência de junção do Azure AD. Apresentamos a seguir uma tela que será exibida se estiver usando o Windows 10 Professional.
<center>
![Tela Quem é o proprietário deste computador?](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)

5.	Insira as credenciais que foram fornecidas a você por sua organização.
<center>
![Tela de entrada](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6.	Depois de inserir seu nome de usuário, um locatário correspondente estará localizado no Azure AD. Se estiver em um domínio federado, você será redirecionado para o servidor local do STS (Serviço de Token Seguro) – por exemplo, o AD FS (Serviços de Federação do Active Directory).
7. Se você for um usuário em um domínio não federado, será necessário inserir suas credenciais diretamente na página hospedada pelo Azure AD. Você também verá o logotipo de sua organização e um texto de suporte se a identidade visual da empresa tiver sido configurada.
8.	Um desafio da autenticação multifator será apresentado a você. Esse desafio é configurável por um administrador de TI.
9.	O Azure AD verifica se esse usuário/dispositivo exige registro no gerenciamento de dispositivo móvel.
10.	O Windows registra o dispositivo no diretório da organização no Azure AD e no gerenciamento de dispositivo móvel, se apropriado.
11.	Se você for um usuário gerenciado, o Windows o levará para a área de trabalho por meio do processo de entrada automática.
12.	Se você for um usuário federado, será direcionado para a tela de entrada do Windows para inserir suas credenciais.

> [AZURE.NOTE] Não há suporte para o ingresso em um domínio local do Active Directory do Windows Server na tela de apresentação do Windows. Portanto, se pretende adicionar um computador a um domínio, é necessário selecionar o link **Configurar o Windows com uma conta local** em vez disso. Em seguida, é possível ingressar no domínio através das configurações do computador, como você fez antes.

## Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Autenticando identidades sem senhas com o Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0928_2016-->