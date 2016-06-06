<properties
   pageTitle="Como executar uma análise de segurança | Microsoft Azure"
   description="Saiba como executar uma revisão com o aplicativo Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Como executar uma revisão de segurança no Azure AD Privileged Identity Management

O Azure AD (Active Directory) Privileged Identity Management simplifica como as empresas gerenciam as identidades com privilégios e o acesso a recursos no Azure AD e em outros Microsoft Online Services, como o Office 365 ou o Microsoft Intune.

Se uma função administrativa tiver sido atribuída a você, o administrador de função com privilégios da sua organização poderá solicitar que você revise e confirme regularmente a necessidade dessa função para seu trabalho. Você pode receber um email que inclui um link ou acessar diretamente o [portal do Azure](https://portal.azure.com). Siga as etapas neste artigo para executar a autorrevisão das suas funções atribuídas.

Se você for um administrador de função com privilégios interessado nas revisões de segurança, obtenha mais detalhes em [Como iniciar uma análise de segurança](active-directory-privileged-identity-management-how-to-start-security-review.md).

## Adicionar o aplicativo Privileged Identity Management

Você pode usar o aplicativo Azure AD PIM (Privileged Identity Management) no [portal do Azure](https://portal.azure.com/) para executar a revisão. Se você não tiver o aplicativo Azure AD Privileged Identity Management em seu portal, siga estas etapas para começar.

1. Entre no [portal do Azure](https://portal.azure.com/) se ainda não tiver feito isso.
2. Se sua organização tiver mais de um diretório, clique em seu nome de usuário no canto superior direito do portal do Azure e selecione o diretório no qual você vai operar.
3. Selecione o ícone **Novo** no painel de navegação esquerdo.
4. Selecione **Segurança + Identidade** no menu.
5. Selecione **Azure AD Privileged Identity Management**.
6. Deixe a opção **Fixar no painel** marcada e clique no botão **Criar**. O aplicativo Privileged Identity Management será aberto.


## Aprovar ou negar acesso

O acesso não será alterado até [a revisão ser concluída](active-directory-privileged-identity-management-how-to-complete-review.md). Esse processo é simplesmente criar uma lista de verificação para aqueles cujo acesso à função deseja-se alterar. Quando pelo menos um usuário tiver sido selecionado, os botões **Aprovar acesso** e **Negar acesso** serão habilitados.

1. No aplicativo PIM, clique em **Examinar acesso administrativo** no menu principal. Será exibida uma lista das análises de segurança.
2. Selecione o(s) **usuário(s)** na lista para o(s) qual(is) você deseja alterar o acesso.
3. Clique em **Aprovar acesso** ou **Negar acesso** para os usuários que você selecionou. Uma notificação será exibida no menu principal do portal do Azure e os nomes selecionados na lista de revisão desaparecerão (você pode tê-los de volta alterando a opção de filtro). Feche a folha **Funções de análise do AD do Azure**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->