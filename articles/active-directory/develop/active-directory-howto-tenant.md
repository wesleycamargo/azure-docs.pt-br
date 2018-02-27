---
title: "Como obter um locatário do Azure AD| Microsoft Docs"
description: "Como obter um locatário do Active Directory do Azure para registrar e criar aplicativos."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/19/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 85783d58b2b02a9d0c6230429bebf2806514dee5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Como obter um locatário do Active Directory do Azure
No AD do Azure, um [locatário](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) é representativo de uma organização.  Ele é uma instância dedicada do serviço Azure AD que uma organização recebe e detém como sua propriedade quando se inscreve em um serviço de nuvem da Microsoft, como o Azure, o Microsoft InTune ou o Office 365.  Cada locatário do AD do Azure é distinto e separado de outros diretórios do AD do Azure.  

Um locatário acomoda os usuários em uma empresa e as informações sobre eles - suas senhas, dados de perfil do usuário, permissões e assim por diante.  Ele também contém grupos, aplicativos e outras informações referentes a uma organização e à sua segurança.

Para permitir que os usuários do AD do Azure entrem no aplicativo, você deve registrá-lo em um locatário de sua propriedade.  Publicar um aplicativo em um locatário do AD do Azure é **totalmente gratuito**.  Na verdade, a maioria dos desenvolvedores cria vários locatários e aplicativos para fins de experimentação, teste, desenvolvimento e preparo.  As organizações que se inscreverem em seu aplicativo e tornarem-se clientes dele podem optar por adquirir licenças, se desejarem tirar proveito dos recursos avançados de diretório.

Então, como você faria para obter um locatário do AD do Azure?  O processo pode ser um pouco se diferente se você:

* [Tem uma assinatura existente do Office 365](#use-an-existing-office-365-subscription)
* [Tem uma assinatura existente do Azure associada a uma conta da Microsoft](#use-an-msa-azure-subscription)
* [Tem uma assinatura existente do Azure associada a uma conta organizacional](#use-an-organizational-azure-subscription)
* [Não tem nenhum deles e deseja começar do zero](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>Usar uma assinatura existente do Office 365
Se houver uma assinatura do Office 365, você já terá um locatário do Azure AD! Você pode entrar no [Portal do Azure](https://portal.azure.com) com sua conta do O365 e começar a usar o Azure AD.

## <a name="use-an-msa-azure-subscription"></a>Usar uma assinatura do Azure MSA
Se você já se inscreveu anteriormente para uma assinatura do Azure com sua conta individual da Microsoft, você já tem um locatário!  Quando você faz logon no [Portal do Azure](https://portal.azure.com), é automaticamente conectado ao seu locatário padrão. Você é livre para usar esse locatário como achar melhor - mas você talvez queira criar uma conta de administrador organizacional.

Para fazer isso, siga essas etapas.  Como alternativa, você poderá criar um novo locatário e criar um administrador nesse locatário seguindo um processo semelhante.

1. Faça logon no [Portal do Azure](https://portal.azure.com) com sua conta individual
2. Navegue até a seção "Azure Active Directory" do portal (localizado na barra de navegação à esquerda, em **Todos os serviços**)
3. Você deve entrar automaticamente no "Diretório Padrão". Se isso não acontecer, alterne pastas clicando no nome da sua conta no canto superior direito.
4. Na seção **Tarefas Rápidas**, escolha **Adicionar um usuário**.
5. No formulário Adicionar Usuário, forneça os detalhes a seguir:

   * Nome: (escolha um valor apropriado)
   * Nome de usuário: (escolha um nome de usuário para esse administrador)
   * Perfil: (preencha os valores apropriados para Nome, Sobrenome, Cargo e Departamento)
   * Função: administrador global
6. Quando você tiver preenchido o formulário “Adicionar usuário” e recebido a senha temporária para o novo usuário administrativo, certifique-se de registrar essa senha, pois você precisa fazer logon com esse novo usuário para alterá-la. Você também pode enviar a senha diretamente para o usuário, usando um email alternativo.
7. Clique em **Criar** para criar o novo usuário.
8. Para alterar a senha temporária, acesse [https://login.microsoftonline.com](https://login.microsoftonline.com) usando essa nova conta de usuário e altere a senha quando for solicitado.

## <a name="use-an-organizational-azure-subscription"></a>Use uma assinatura organizacional do Azure
Se você já se inscreveu anteriormente para uma assinatura do Azure com sua conta organizacional, você já tem um locatário!  No [Portal do Azure](https://portal.azure.com), você deve encontrar um locatário ao navegar até "Todos os serviços" e "Azure Active Directory".  Você é livre para usar esse locatário como desejar.

## <a name="start-from-scratch"></a>Começar do zero
Se todos os itens acima forem sem sentido para você, não se preocupe. Visite o [Portal do Azure](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) para criar um novo diretório do Azure AD. Depois de concluir o processo, você terá seu próprio locatário do Azure AD com o nome de domínio escolhido durante a inscrição.  No [Portal do Azure](https://portal.azure.com), você pode localizar seu locatário navegando até **Azure Active Directory** no painel de navegação à esquerda.
