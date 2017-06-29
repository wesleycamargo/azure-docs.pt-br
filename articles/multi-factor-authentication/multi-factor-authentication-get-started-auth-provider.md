---
title: "Introdução ao Provedor de Autenticação Multifator do Azure | Microsoft Docs"
description: "Saiba como criar um Provedor de Autenticação Multifator do Azure."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/14/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 160b4a0f7db327f5114bb45f1d2a7f6633aee17c
ms.contentlocale: pt-br
ms.lasthandoff: 06/20/2017

---

# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>Introdução a um Provedor de Autenticação Multifator do Azure
A autenticação em duas etapas está disponível por padrão para os administradores globais que têm o Azure Active Directory e os usuários do Office 365. No entanto, se você quiser aproveitar os [recursos avançados](multi-factor-authentication-whats-next.md), então, deverá adquirir a versão completa da Autenticação Multifator do Azure (MFA).

Um Provedor do Azure Multi-Factor Auth é usado para aproveitar as vantagens dos recursos fornecidos pela versão completa do Azure MFA. É para os usuários que **não têm licenças por meio do Azure MFA, Azure AD Premium ou Enterprise Mobility + Security (EMS)**.  O Azure MFA, o Azure AD Premium e o EMS incluem a versão completa do Azure MFA por padrão. Se você tiver licenças, não precisará de um Provedor de Autenticação Multifator do Azure.

Um provedor de Autenticação Multifator do Azure será requerido para baixar o SDK.

> [!IMPORTANT]
> Para baixar o SDK, crie um Provedor de Autenticação Multifator do Azure mesmo que você tenha as licenças MFA, AAD Premium ou EMS do Azure.  Se você criar um Provedor de Autenticação Multifator do Azure para essa finalidade e já tiver licenças, crie o Provedor com o modelo **Por Usuário Habilitado**. Em seguida, vincule o Provedor ao diretório que contém as licenças MFA, Azure AD Premium ou EMS do Azure. Esta configuração garante que você não será cobrado, caso tenha mais usuários exclusivos executando a verificação em duas etapas do que o número de licenças possuídas.

## <a name="what-is-an-azure-multi-factor-auth-provider"></a>O que é um Provedor de Autenticação Multifator do Azure?

Se você não tiver licenças para a Autenticação Multifator do Azure, poderá criar um provedor de autenticação para exigir a verificação em duas etapas para seus usuários. Se você estiver desenvolvendo um aplicativo personalizado e desejar habilitar a Azure MFA, crie um provedor de autenticação e [baixar o SDK](multi-factor-authentication-sdk.md).

Há dois tipos de provedores de autenticação e a diferença está em torno de como a sua assinatura do Azure é cobrada. A opção por autenticação calcula o número de autenticações executadas em seu locatário em um mês. Essa será a melhor opção se você tiver um número de usuários que se autenticam apenas ocasionalmente, como se você exigir MFA para um aplicativo personalizado. A opção por usuário calcula o número de pessoas no seu locatário que executam a verificação em duas etapas em um mês. Essa é a melhor opção se você tiver alguns usuários com licenças, mas precisar estender MFA para mais usuários, além de seus limites de licença.

## <a name="create-a-multi-factor-auth-provider"></a>Criar um Provedor de Autenticação Multifator
Use as etapas a seguir para criar um Provedor de Autenticação Multifator do Azure.

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com) como um administrador.
2. Selecione **Active Directory**à esquerda.
3. Na página do Active Directory, na parte superior, selecione **Provedores de Multi-Factor Authentication**.
   ![Criação de um provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. Na parte inferior, clique em **Novo**.
   ![Criação de um provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. Em Serviços de Aplicativos, selecione **Provedor de Autenticação Multifator**
   ![Criando um Provedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Selecione **Criação Rápida**.
   ![Criação de um provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
7. Preencha os seguintes campos e selecione **Criar**.
   1. **Nome** – o nome do Provedor de Multi-Factor Authentication.
   2. **Modelo de Uso** – Escolha uma das duas opções:
      * Por Autenticação: modelo de compra que cobra por autenticação. Normalmente usado para cenários que usam o Azure Multi-Factor Authentication em um aplicativo voltado para o consumidor.
      * Por Usuário Habilitado: o modelo de compra que cobra por usuário habilitado. Normalmente usado para acesso de funcionários a aplicativos como o Office 365. Escolha esta opção se você tiver alguns usuários que já estão licenciados para o MFA do Azure.
   3. **Diretório** – o locatário do Azure Active Directory ao qual o Provedor de Multi-Factor Authentication está associado. Esteja ciente do seguinte:
      * Não é necessário um diretório do AD do Azure para criar um Provedor do Multi-Factor Auth. Deixe a caixa em branco se você só estiver planejando usar o Servidor de Autenticação Multifator do Azure ou o SDK.
      * O Provedor do Multi-Factor Auth precisa estar associado a um diretório do Azure AD para aproveitar os recursos avançados.
      * O Azure AD Connect, o AAD Sync ou o DirSync são apenas um requisito se você estiver sincronizando o ambiente do Active Directory local com um diretório do AD do Azure.  Se você usar apenas um diretório do Azure AD que não esteja sincronizado, isso não será necessário.
        ![Criando um Provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
8. Depois que você clicar em criar, o Provedor de Multi-Factor Authentication será criado e você deverá ver uma mensagem informando: **Provedor de Multi-Factor Authentication criado com êxito**. Clique em **Ok**.
   ![Criação de um provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)

