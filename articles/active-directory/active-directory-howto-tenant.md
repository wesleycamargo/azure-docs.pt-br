<properties
	pageTitle="Como obter um locatário do AD do Azure | Microsoft Azure"
	description="Como obter um locatário do Active Directory do Azure para registrar e criar aplicativos."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/02/2015"
	ms.author="dastrock"/>

# Como obter um locatário do Active Directory do Azure

No AD do Azure, um [locatário](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) é representativo de uma organização. Ele é uma instância dedicada do serviço Azure AD que uma organização recebe e detém como sua propriedade quando se inscreve em um serviço de nuvem da Microsoft, como o Azure, o Microsoft InTune ou o Office 365. Cada locatário do AD do Azure é distinto e separado de outros diretórios do AD do Azure.

Um locatário acomoda os usuários em uma empresa e as informações sobre eles - suas senhas, dados de perfil do usuário, permissões e assim por diante. Ele também contém grupos, aplicativos e outras informações referentes a uma organização e à sua segurança.

Para permitir que os usuários do AD do Azure entrem no aplicativo, você deve registrá-lo em um locatário de sua propriedade. Publicar um aplicativo em um locatário do AD do Azure é **totalmente gratuito**. Na verdade, a maioria dos desenvolvedores criará vários locatários e aplicativos para fins de experimentação, teste, desenvolvimento e preparo. As organizações que se inscreverem em seu aplicativo e tornarem-se clientes dele podem optar por adquirir licenças, se desejarem tirar proveito dos recursos avançados de diretório.

Então, como você faria para obter um locatário do AD do Azure? O processo pode ser um pouco se diferente se você:

- [Tem uma assinatura existente do Office 365](#use-an-existing-office-365-subscription)
- [Tem uma assinatura existente do Azure associada a uma conta da Microsoft](#use-an-msa-azure-subscription)
- [Tem uma assinatura existente do Azure associada a uma conta organizacional](#use-an-organizational-azure-subscription)
- [Não tem nenhum deles e deseja começar do zero](#start-from-scratch)

## Usar uma assinatura existente do Office 365
Se você tiver uma assinatura existente do Office 365, mas não tiver uma assinatura do Azure (e não puder entrar no [Portal de Gerenciamento](https://manage.windowsazure.com)), execute [estas instruções](https://technet.microsoft.com/library/dn832618.aspx) para obter acesso ao seu locatário do AD do Azure.

## Usar uma assinatura do Azure MSA
Se você já se inscreveu anteriormente para uma assinatura do Azure com sua conta individual da Microsoft, você já tem um locatário! No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), você deve encontrar um locatário nomeado "Locatário padrão" listado em "Todos os itens" e "Active Directory". Você é livre para usar esse locatário como achar melhor - mas você talvez queira criar uma conta de administrador organizacional.

Para fazer isso, siga essas etapas. Como alternativa, você poderá criar um novo locatário e criar um administrador nesse locatário seguindo um processo semelhante.

1.	Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com) com sua conta individual
2.	Navegue até a seção "Active Directory" do portal (localizado na barra de navegação à esquerda)
3.	Selecione a entrada "Diretório padrão" na lista de pastas disponíveis
4.	Clique no link Usuários na parte superior da página. Você verá um único usuário na lista com o valor "Conta da Microsoft" na coluna “Originado de”
5.	Clique em “Adicionar usuário” na parte inferior da página
6.	No formulário Adicionar usuário, forneça os detalhes a seguir:
    - Tipo de usuário: novo usuário na organização
    - Nome de usuário: (escolha um nome de usuário para esse administrador)
    - Nome/Sobrenome/Nome de exibição: (escolha valores apropriados)
    - Função: administrador global
    - Endereço de email alternativo: (insira os valores apropriados)
    - Opcional: habilitar a Multi-Factor Authentication
    - Por fim, clique no botão verde "CRIAR" para finalizar a criação do usuário (e exibir a senha temporária).
7.	Quando você tiver preenchido o formulário “Adicionar usuário” e recebido a senha temporária para o novo usuário administrativo, certifique-se de registrar essa senha, pois você precisará fazer logon com esse novo usuário para alterá-la. Você também pode enviar a senha diretamente para o usuário, usando um email alternativo.
8.	Para alterar a senha temporária, faça logon no https://login.microsoftonline.com com essa nova conta de usuário e altere a senha quando solicitado.


## Use uma assinatura organizacional do Azure
Se você já se inscreveu anteriormente para uma assinatura do Azure com sua conta organizacional, você já tem um locatário! No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), você deve encontrar um locatário listado em "Todos os itens" e "Active Directory". Você é livre para usar esse locatário como desejar. Você também poderá criar um novo locatário usando o botão "Novo" no canto inferior esquerdo do portal.


## Começar do zero
Se todos os itens acima forem sem sentido para você, não se preocupe. Basta visitar [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) para inscrever-se no Azure com uma nova organização. Depois de concluir o processo, você terá seu próprio locatário do AD do Azure com o nome de domínio escolhido durante a inscrição. No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), você pode localizar seu locatário navegando para "Active Directory" no painel de navegação à esquerda.

Como parte do processo de inscrição para o Azure, será solicitado que você forneça detalhes de cartão de crédito. Você pode prosseguir com confiança - você não será cobrado para publicar aplicativos no AD do Azure nem para criar novos locatários.

<!---HONumber=August15_HO6-->