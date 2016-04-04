<properties
   pageTitle="Funções no PIM | Microsoft Azure"
   description="Aprenda quais funções são usadas para identidades com privilégios com a extensão de Privileged Identity Management do Azure."
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
   ms.date="03/17/2016"
   ms.author="kgremban"/>

# Privileged Identity Management do Azure AD: Funções

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

## Funções do Active Directory do Azure, Office 365 e outras fontes

O PIM (Privileged Identity Management) do Azure usa as funções a seguir como funções padrão de administrador:

- Administrador global
- Administrador de cobrança
- Administrador de serviços
- Administrador de usuários
- Administrador de senha

Para saber mais sobre funções do Office 365, do Exchange Online, do SharePoint Online e do Skype for Business, acesse [Atribuindo funções de administrador no Office 365](https://support.office.com/pt-BR/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504?ui=pt-BR&rs=pt-BR&ad=US).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## Funções de Usuário e Realização de Logon
> [AZURE.NOTE]Para um usuário poder entrar no PIM do Azure, deverá ter uma licença para o Azure.

## Atribuir uma Licença a um Usuário no AD do Azure

> [AZURE.NOTE] A opção de licença só será exibida se as licenças realmente saírem para essa assinatura.

1. Com uma conta de administrador global ou de coadministrador, entre em [http://manage.windowsazure.com](http://manage.windowsazure.com).
2. Clique em **Todos os itens** no menu principal.
3. Selecione o diretório com o qual você deseja trabalhar e que tem licenças associadas a ele.
4. Clique em **Licenças**. A lista de licenças disponíveis será exibida.
5. Clique no plano de licença que contém as licenças que você deseja distribuir.
6. Clique em **Atribuir Usuários**.
7. Selecione o usuário ao qual você deseja atribuir uma licença.
8. Clique no botão **Atribuir**. O usuário agora pode fazer logon no Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->