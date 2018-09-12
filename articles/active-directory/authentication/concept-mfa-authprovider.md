---
title: Quando e como usar um Provedor de Autenticação Multifator do Microsoft Azure?
description: Quando você deve usar um Provedor de Autenticação com o Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: b601a3d23b23faa16925881a54e2ceba85c800f8
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669058"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Quando usar um Provedor de Autenticação Multifator do Microsoft Azure

A autenticação em duas etapas está disponível por padrão para os administradores globais que têm o Azure Active Directory e os usuários do Office 365. No entanto, se você quiser aproveitar os [recursos avançados](howto-mfa-mfasettings.md), então, deverá adquirir a versão completa da Autenticação Multifator do Azure (MFA).

Um Provedor de Autenticação Multifator do Azure é usado para aproveitar os recursos fornecidos pela Autenticação Multifator do Microsoft Azure para usuários que **não têm licenças**. 

Se você tiver licenças que abrangem todos os usuários em sua organização, em seguida, não será necessário um Provedor de Autenticação Multifator do Microsoft Azure. Crie um Provedor de Autenticação Multifator do Microsoft Azure apenas se também precisar fornecer verificação em duas etapas para alguns usuários que não tenham licenças.

> [!NOTE]
> A partir de 1º de setembro de 2018, novos provedores de autenticação não poderão mais ser criados. Os provedores de autenticação existentes podem continuar sendo usados e atualizados. A Autenticação Multifator continuará sendo um recurso disponível nas licenças do Azure AD Premium.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Limitações relacionadas ao SDK do Azure MFA

Um provedor de Autenticação Multifator do Azure será requerido para baixar o SDK. Observe que o SDK foi preterido e não há suporte para novos clientes e continuará funcionando só até 14 de novembro de 2018. Após essa data, as chamadas para o SDK falharão.

Para baixar o SDK, crie um Provedor de Autenticação Multifator do Microsoft Azure mesmo que você tenha as licenças MFA, AAD Premium ou outras licenças em pacote. Se você criar um Provedor de Autenticação Multifator do Azure para essa finalidade e já tiver licenças, crie o Provedor com o modelo **Por Usuário Habilitado**. Em seguida, vincule o Provedor ao diretório que contém as licenças MFA, Azure AD Premium ou outras licenças em pacote. Esta configuração garante que você não será cobrado, caso tenha mais usuários exclusivos executando a verificação em duas etapas do que o número de licenças possuídas.

## <a name="what-is-an-mfa-provider"></a>O que é um Provedor MFA?

Se não tiver licenças para a Autenticação Multifator do Microsoft Azure, você poderá criar um provedor de autenticação para exigir a verificação em duas etapas para seus usuários.

Há dois tipos de provedores de autenticação e a diferença está em como a assinatura do Azure é cobrada. A opção por autenticação calcula o número de autenticações executadas em seu locatário em um mês. Essa será a melhor opção se você tiver um número de usuários que se autenticam apenas ocasionalmente. A opção por usuário calcula o número de pessoas no seu locatário que executam a verificação em duas etapas em um mês. Essa é a melhor opção se você tiver alguns usuários com licenças, mas precisar estender MFA para mais usuários, além de seus limites de licença.

## <a name="create-an-mfa-provider"></a>Criar um Provedor MFA

Use as etapas a seguir para criar um Provedor de Autenticação Multifator do Azure no portal do Azure:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador global.
2. Selecione **Azure Active Directory** > **Servidor MFA** > **Provedores**.

   ![Provedores][Providers]

3. Selecione **Adicionar**.
4. Preencha os seguintes campos e selecione **Adicionar**:
   - **Nome** - o nome do Provedor.
   - **Modelo de Uso** – Escolha uma das duas opções:
      * Por Autenticação: modelo de compra que cobra por autenticação. Normalmente usado para cenários que usam o Autenticação Multifator do Azure em um aplicativo voltado para o consumidor.
      * Por Usuário Habilitado: o modelo de compra que cobra por usuário habilitado. Normalmente usado para acesso de funcionários a aplicativos como o Office 365. Escolha esta opção se você tiver alguns usuários que já estão licenciados para o MFA do Azure.
   - **Assinatura** – a assinatura do Azure que é cobrada para a atividade de verificação em duas etapas por meio do Provedor.
   - **Diretório** – o locatário do Azure Active Directory ao qual o Provedor está associado.
      * Você não precisa de um diretório do Azure AD para criar um Provedor. Deixe essa caixa em branco se você planeja baixar apenas o Servidor de Autenticação Multifator do Azure.
      * O Provedor precisa estar associado a um diretório do Azure AD para aproveitar os recursos avançados.
      * Somente um Provedor pode ser associado a um diretório do Azure AD.

## <a name="manage-your-mfa-provider"></a>Gerenciar seu `Provedor MFA

Não é possível alterar o modelo de uso (por usuário habilitado ou por autenticação) após a criação de um provedor de MFA. No entanto, você pode excluir o provedor de MFA e criar um modelo de uso diferente.

Se o Provedor de Autenticação Multifator atual estiver associado a um diretório do Azure AD (também conhecido como locatário do Azure AD) você poderá excluir com segurança o provedor de MFA e criar um vinculado ao mesmo locatário do Azure AD. Como alternativa, se você comprou licenças suficientes para cobrir todos os usuários que estão habilitados para MFA, é possível excluir o provedor MFA completamente.

Se o seu provedor de MFA não estiver vinculado a um locatário do Azure AD, ou você vincula o novo provedor de MFA a um locatário diferente do Azure AD, as opções de definições de usuário e de configuração não serão transferidas. Além disso, os servidores MFA existentes do Azure precisarão ser reativados usando as credenciais de ativação geradas por meio do novo provedor de MFA. Reativar os servidores MFA para vinculá-los ao novo provedor de MFA não afeta a autenticação de chamada telefônica e de mensagens de texto, mas as notificações de aplicativo móvel deixarão de funcionar para todos os usuários até que eles reativem o aplicativo móvel.

## <a name="next-steps"></a>Próximas etapas

[Definir as configurações da Autenticação Multifator](howto-mfa-mfasettings.md)

[Providers]: ./media/concept-mfa-authprovider/add-providers.png "Adicionar provedores MFA"
