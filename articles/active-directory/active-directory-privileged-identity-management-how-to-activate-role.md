<properties
   pageTitle="Como ativar ou desativar uma função | Microsoft Azure"
   description="Aprenda a ativar funções para identidades com privilégios com o aplicativo Azure Privileged Identity Management."
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
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Como ativar ou desativar funções no Azure AD Privileged Identity Management

O Azure AD (Active Directory) Privileged Identity Management simplifica como as empresas gerenciam as identidades com privilégios e o acesso a recursos no Azure AD e em outros Microsoft Online Services, como o Office 365 ou o Microsoft Intune.

Este artigo destina-se a administradores que precisam ativar sua função no Azure AD PIM (Privileged Identity Management). Ele vai orientá-lo pelas etapas para ativar uma função quando você precisar das permissões e desativar a função quando terminar.

Se você tiver sido atribuído a uma função administrativa, poderá ativar essa função quando você precisar executar uma tarefa que exige aquela função. Por exemplo, se você precisa gerenciar o Office 365 apenas algumas vezes, os administradores de segurança da sua organização não desejam tornar um administrador permanente. Em vez disso, eles fazem de você um candidato às funções de Administrador Global ou Administrador do Exchange Online no Azure AD. Isso significa que você pode solicitar uma atribuição de função temporária quando precisar desses privilégios e você terá controle administrativo para o Office 365 por um período de tempo predeterminado.


## Adicionar o aplicativo Privileged Identity Management

Use o aplicativo do Azure AD Privileged Identity Management no [portal do Azure](https://portal.azure.com/) para solicitar uma "ativação" de função mesmo se você for operar em outro portal ou por meio do PowerShell. Se você não tiver o aplicativo Azure AD Privileged Identity Management em seu portal do Azure, siga estas etapas para começar.

1. Entre no [portal do Azure](https://portal.azure.com/) se ainda não tiver feito isso.
2. Se sua organização tiver mais de um diretório, clique em seu nome de usuário no canto superior direito do portal do Azure e selecione o diretório no qual você vai operar.
3. Clique no ícone **Novo** no painel de navegação esquerdo.
4. Selecione **Segurança + Identidade** no menu Criar.
5. Selecione **Azure AD Privileged Identity Management**.
6. Marque **Fixar no painel** e clique no botão **Criar**. O aplicativo Privileged Identity Management será aberto.

## Ativar uma função

Quando você precisa assumir uma função, pode solicitar a ativação usando o botão **Ativar minha função** no aplicativo do Azure AD Privileged Identity Management.


1. Entre no [portal do Azure](https://portal.azure.com/) e selecione o bloco Azure AD Privileged Identity Management.
2. Selecione **Ativar minha função**. Será exibida uma lista das funções que foram atribuídas a você.
3. Selecione a função que você deseja ativar.
4. Selecione **Ativar**. A folha **Solicitar ativação de função** será exibida.
5. Para algumas funções, como Administrador Global, o MFA (Multi-Factor Authentication) é necessário para ativar a função. Se você não executou o MFA ao fazer logon, poderá precisar executar antes de ser possível ativar a função.
6. Insira o motivo para a solicitação de ativação no campo de texto. O administrador de segurança também pode exigir que você forneça um número de tíquete de problema.
7. Selecione **OK**. A função agora será ativada e a alteração de função se tornará visível no Microsoft Online Services.

## Desativar uma função

Após uma função ter sido ativada, ela será automaticamente desativada quando seu limite de tempo for atingido.

Se você terminar antes, também poderá desativar uma função manualmente no aplicativo Azure AD Privileged Identity Management. Selecione **Ativar minha função**, localize a função que não é mais necessária no momento e selecione **Desativar**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Caso você esteja interessado em aprender mais sobre o Azure AD Privileged Identity Management, os links a seguir têm mais informações.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->