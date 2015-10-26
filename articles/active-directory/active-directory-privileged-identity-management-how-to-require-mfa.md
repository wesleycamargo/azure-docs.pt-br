<properties
   pageTitle="Privileged Identity Management do Azure: Como Solicitar MFA"
   description="Aprenda a exigir MFA (autenticação multifator) para identidades com privilégios com a extensão de Privileged Identity Management do Azure."
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="inhenk"/>

# Privileged Identity Management do Azure: Como Solicitar MFA
É altamente recomendável que você exija a autenticação multifator para todos os administradores.

##Solicitação de MFA no Privileged Identity Management do Azure
Quando você efetuar logon como administrador PIM, você receberá alertas que sugerem que contas com privilégios devem exigir MFA (autenticação multifator). Clique no alerta de segurança no painel PIM e uma nova folha será aberta com uma lista das contas de administrador que devem exigir MFA. É possível exigir MFA selecionando várias funções e, em seguida, clicando no botão Corrigir, ou você pode clicar nas reticências ao lado das funções individuais e, em seguida, clicar no botão Corrigir.

Além disso, você pode alterar o requisito de MFA por função, clicando em uma função na seção Funções do painel e então habilitando MFA para essa função, clicando em configurações na folha da função e, em seguida, selecionando Habilitar sob autenticação multifator.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Oct15_HO3-->