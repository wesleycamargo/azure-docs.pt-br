---
title: "Introdução ao Provedor de Autenticação Multifator do Azure | Microsoft Docs"
description: "Saiba como criar um Provedor de Autenticação Multifator do Azure."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: dc1664d382c6e59c125ef00d02a8848079d8bf8d
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2017
---
# <a name="getting-started-with-an-azure-multi-factor-authentication-provider"></a>Introdução a um Provedor de Autenticação Multifator do Azure
A autenticação em duas etapas está disponível por padrão para os administradores globais que têm o Azure Active Directory e os usuários do Office 365. No entanto, se você quiser aproveitar os [recursos avançados](multi-factor-authentication-whats-next.md), então, deverá adquirir a versão completa da Autenticação Multifator do Azure (MFA).

Um Provedor do Autenticação Multifator do Azure é usado para aproveitar as vantagens dos recursos fornecidos pela versão completa do Azure MFA. É para os usuários que **não têm licenças por meio do Azure MFA, Azure AD Premium ou Enterprise Mobility + Security (EMS)**.  O Azure MFA, o Azure AD Premium e o EMS incluem a versão completa do Azure MFA por padrão. Se você tiver licenças, não precisará de um Provedor de Autenticação Multifator do Azure.

Um provedor de Autenticação Multifator do Azure será requerido para baixar o SDK.

> [!IMPORTANT]
> A reprovação do Software Development Kit (SDK) da Autenticação Multifator do Azure foi anunciada. Não há mais suporte para esse recurso para novos clientes. Os clientes atuais podem continuar usando o SDK até 14 de novembro de 2018. Depois dessa data, as chamadas para o SDK falharão.
> [!IMPORTANT]
>Para baixar o SDK, crie um Provedor de Autenticação Multifator do Azure mesmo que você tenha as licenças MFA, AAD Premium ou EMS do Azure.  Se você criar um Provedor de Autenticação Multifator do Azure para essa finalidade e já tiver licenças, crie o Provedor com o modelo **Por Usuário Habilitado**. Em seguida, vincule o Provedor ao diretório que contém as licenças MFA, Azure AD Premium ou EMS do Azure. Esta configuração garante que você não será cobrado, caso tenha mais usuários exclusivos executando a verificação em duas etapas do que o número de licenças possuídas. 

## <a name="what-is-an-mfa-provider"></a>O que é um Provedor MFA?

Se você não tiver licenças para a Autenticação Multifator do Azure, poderá criar um provedor de autenticação para exigir a verificação em duas etapas para seus usuários.

Há dois tipos de provedores de autenticação e a diferença está em torno de como a sua assinatura do Azure é cobrada. A opção por autenticação calcula o número de autenticações executadas em seu locatário em um mês. Essa será a melhor opção se você tiver um número de usuários que se autenticam apenas ocasionalmente. A opção por usuário calcula o número de pessoas no seu locatário que executam a verificação em duas etapas em um mês. Essa é a melhor opção se você tiver alguns usuários com licenças, mas precisar estender MFA para mais usuários, além de seus limites de licença.

## <a name="create-an-mfa-provider---public-preview"></a>Criar um Provedor de MFA - Versão prévia pública

Use as etapas a seguir para criar um Provedor de Autenticação Multifator do Azure no portal do Azure:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador. 
2. Selecione **Azure Active Directory** > **Servidor MFA**.
3. Selecione **Provedores**.
4. Selecione **Adicionar**.
5. Preencha os seguintes campos e selecione **Adicionar**:
   - **Nome** - o nome do Provedor.
   - **Modelo de Uso** – Escolha uma das duas opções:
      * Por Autenticação: modelo de compra que cobra por autenticação. Normalmente usado para cenários que usam o Autenticação Multifator do Azure em um aplicativo voltado para o consumidor.
      * Por Usuário Habilitado: o modelo de compra que cobra por usuário habilitado. Normalmente usado para acesso de funcionários a aplicativos como o Office 365. Escolha esta opção se você tiver alguns usuários que já estão licenciados para o MFA do Azure.
   - **Assinatura** – a assinatura do Azure que é cobrada para a atividade de verificação em duas etapas por meio do Provedor. 
   - **Diretório** – o locatário do Azure Active Directory ao qual o Provedor está associado.
      * Você não precisa de um diretório do Azure AD para criar um Provedor. Deixe essa caixa em branco se você planeja baixar apenas o Servidor de Autenticação Multifator do Azure.
      * O Provedor precisa estar associado a um diretório do Azure AD para aproveitar os recursos avançados.
      * Somente um Provedor pode ser associado a um diretório do Azure AD.

## <a name="create-an-mfa-provider"></a>Criar um Provedor MFA
Use as etapas a seguir para criar um Provedor de Autenticação Multifator do Azure no portal clássico:

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com) como um administrador.
2. Selecione **Active Directory**à esquerda.
3. Na página do Active Directory, na parte superior, selecione **Provedores de Autenticação Multifator**.
   
   ![Criação de um provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)

4. Na parte inferior, clique em **Novo**.
   
   ![Criação de um provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)

5. Em Serviços de Aplicativos, selecione **Provedor de Autenticação Multifator**
   
   ![Criação de um provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)

6. Selecione **Criação Rápida**.
   
   ![Criação de um provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)

7. Preencha os seguintes campos e selecione **Criar**.
   1. **Nome** - o nome do Provedor.
   2. **Modelo de Uso** – Escolha uma das duas opções:
      * Por Autenticação: modelo de compra que cobra por autenticação. Normalmente usado para cenários que usam o Autenticação Multifator do Azure em um aplicativo voltado para o consumidor.
      * Por Usuário Habilitado: o modelo de compra que cobra por usuário habilitado. Normalmente usado para acesso de funcionários a aplicativos como o Office 365. Escolha esta opção se você tiver alguns usuários que já estão licenciados para o MFA do Azure.
   3. **Diretório** – o locatário do Azure Active Directory ao qual o Provedor está associado.
      * Você não precisa de um diretório do Azure AD para criar um Provedor. Deixe essa caixa em branco se você planeja baixar apenas o Servidor de Autenticação Multifator do Azure.
      * O Provedor precisa estar associado a um diretório do Azure AD para aproveitar os recursos avançados.
      * Somente um Provedor pode ser associado a um diretório do Azure AD.  
      ![Criação de um provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)

8. Depois que você clicar em criar, o Provedor de Autenticação Multifator será criado e você deverá ver uma mensagem informando: **Provedor de Autenticação Multifator criado com êxito**. Clique em **OK**.  
   
   ![Criação de um provedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)  

## <a name="manage-your-mfa-provider"></a>Gerenciar seu `Provedor MFA

Não é possível alterar o modelo de uso (por usuário habilitado ou por autenticação) após a criação de um provedor de MFA. No entanto, você pode excluir o provedor de MFA e criar um modelo de uso diferente.

Se o Provedor de Autenticação Multifator atual estiver associado a um diretório do Azure AD (também conhecido como locatário do Azure AD) você poderá excluir com segurança o provedor de MFA e criar um vinculado ao mesmo locatário do Azure AD. Como alternativa, se você tiver adquirido suficiente MFA, Azure AD Premium, ou licenças do Premium (EMS) para cobrir todos os usuários que estão habilitados para MFA, você pode excluir o provedor MFA completamente.

Se o seu provedor de MFA não estiver vinculado a um locatário do Azure AD, ou você vincula o novo provedor de MFA a um locatário diferente do Azure AD, as opções de definições de usuário e de configuração não serão transferidas. Além disso, os servidores MFA existentes do Azure precisarão ser reativados usando as credenciais de ativação geradas por meio do novo provedor de MFA. Reativar os servidores MFA para vinculá-los para o novo provedor de MFA não afete de telefonema e autenticação de mensagens de texto, mas as notificações de aplicativo móvel deixará de funcionar para todos os usuários até que eles reativarem o aplicativo móvel.

## <a name="next-steps"></a>Próximas etapas

[Definir as configurações da Autenticação Multifator](multi-factor-authentication-whats-next.md)
