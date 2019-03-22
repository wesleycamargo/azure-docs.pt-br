---
title: Versões e planos de consumo do Azure MFA | Microsoft Docs
description: Informações sobre o cliente de autenticação multifator e os diferentes métodos e as versões disponíveis.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e68175471b24c48f23cc48cff9d0b47c937f588a
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217182"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Como obter a Autenticação Multifator do Azure

Quando se trata de proteger suas contas, verificação em duas etapas deve ser padrão em sua organização. Esse recurso é especialmente importante para contas que tenham acesso privilegiado a recursos. Por esse motivo, a Microsoft oferece recursos básicos de verificação em duas etapas aos administradores do Office 365 e Azure AD (Azure Active Directory) sem nenhum custo adicional. Se você quiser atualizar os recursos para os administradores ou estender a verificação em duas etapas para o restante dos usuários, poderá adquirir a Autenticação Multifator do Microsoft Azure de várias maneiras.

> [!IMPORTANT]
> Este artigo deve ser um guia para ajudá-lo a entender as diferentes maneiras de comprar autenticação multifator do Azure. Para obter detalhes específicos sobre preços e cobrança, você deve sempre consultar a [página de preços da Autenticação Multifator](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versões disponíveis da Autenticação Multifator do Azure

A tabela a seguir descreve as diferenças entre as três versões de autenticação multifator:

| Versão | DESCRIÇÃO |
| --- | --- |
| Autenticação Multifator para Office 365 <br> Microsoft 365 Business |Esta versão funciona exclusivamente com os aplicativos do Office 365 e é gerenciada do portal do Office 365 ou do Microsoft 365. Os administradores podem [proteger recursos do Office 365 com a verificação de duas etapas](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Essa versão é parte de uma assinatura do Office 365 ou do Microsoft 365 Business. |
| Autenticação Multifator para Administradores do Azure AD | Os usuários atribuídos à função Administrador global do Azure AD nos locatários do Azure AD podem habilitar a verificação em duas etapas sem custo adicional.|
| Autenticação Multifator do Azure | Geralmente conhecida como a versão "completa", a Autenticação Multifator do Azure oferece o conjunto mais avançado de recursos. Ela fornece opções de configuração adicionais por meio do [portal do Azure](https://portal.azure.com), relatórios avançados e suporte para uma variedade de aplicativos locais e na nuvem. A Autenticação Multifator do Microsoft Azure é um recurso do [Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) e pode ser implantada na nuvem ou no local. |

> [!NOTE]
> Novos clientes não poderão mais comprar Autenticação Multifator do Microsoft Azure como uma oferta independente a partir de 1º de setembro de 2018. A autenticação multifator continuará disponível como um recurso nas licenças do Microsoft Azure Active Directory Premium.

## <a name="feature-comparison-of-versions"></a>Comparação de recursos dasversões

A tabela a seguir fornece uma lista dos recursos que estão disponíveis em várias versões do Autenticação Multifator do Azure.

> [!NOTE]
> Esta tabela de comparação aborda os recursos que fazem parte de cada versão da Autenticação Multifator. Se você tiver o serviço completo da Autenticação Multifator do Azure, alguns recursos podem não estar disponíveis caso você use [MFA na nuvem ou MFA no local](concept-mfa-whichversion.md).
>

| Recurso | Autenticação Multifator para Office 365 | Autenticação Multifator para Administradores do Azure AD | Autenticação Multifator do Azure |
| --- |:---:|:---:|:---:|
| Proteger contas de administrador do Azure AD com MFA |● |● (Apenas para contas de Administrador Global do Azure AD) |● |
| Aplicativos móveis como um fator secundário |● |● |● |
| Chamada telefônica como um fator secundário |● |● |● |
| SMS como um fator secundário |● |● |● |
| Senhas de aplicativos para clientes que não oferecem suporte a MFA |● |● |● |
| Controle do administrador sobre métodos de verificação |● |● |● |
| Proteger contas que não são de administrador com MFA |● (Somente para aplicativos do Office 365) | |● |
| Modo PIN | | |● |
| Alerta de fraude | | |● |
| Relatórios de MFA | | |● |
| Desvio único | | |● |
| Saudações personalizadas para chamadas telefônicas | | |● |
| ID do chamador personalizado para chamadas telefônicas | | |● |
| IPs confiáveis | | |● |
| Lembrar MFA para dispositivos confiáveis |● |● |● |
| MFA para aplicativos locais | | |● |

> [!IMPORTANT]
> A partir de março de 2019 as opções de chamada telefônica não estará disponível para os usuários do MFA e o SSPR em locatários gratuitos/avaliação do Azure AD. Mensagens SMS não são afetadas por essa alteração. Chamada telefônica continuará disponível para os usuários no pago locatários do Azure AD. Essa alteração só afeta o locatários gratuitos/avaliação do Azure AD.

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Como ativar Autenticação Multifator do Azure para administradores do Azure AD

Os usuários atribuídos à função de administrador Global nos locatários do Azure AD podem habilitar a verificação em duas etapas para suas contas de administrador global sem custo adicional. Se estiver usando uma conta Microsoft, você poderá registrar-se para a autenticação multifator usando as diretrizes encontradas no artigo de suporte da conta Microsoft, [Sobre a verificação em duas etapas](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). Se não estiver usando uma conta Microsoft, ative a autenticação multifator para Administradores Globais usando as diretrizes encontradas no artigo [Como exigir verificação em duas etapas para um usuário ou grupo](howto-mfa-userstates.md).

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Como comprar a autenticação de vários fatores do Azure

Se você quiser a funcionalidade completa oferecida pela autenticação multifator do Azure, há várias opções:

### <a name="option-1---licenses-that-include-mfa"></a>Opção 1 - Licenças que incluem MFA

Compre licenças que incluem a Autenticação Multifator do Microsoft Azure, como Azure Active Directory Premium ou um pacote de licença que inclui o Azure AD Premium, e atribua aos usuários no Azure Active Directory.

### <a name="option-2---mfa-consumption-based-model"></a>Opção 2 - modelo baseado no consumo de MFA

Essa opção não está mais disponível para novos clientes em vigor em 1 de setembro de 2018.

A partir de 1º de setembro de 2018, novos provedores de autenticação não poderão mais ser criados. Os provedores de autenticação existentes podem continuar sendo usados e atualizados. A Autenticação Multifator continuará sendo um recurso disponível nas licenças do Azure AD Premium.

Ao usar um Provedor de Autenticação Multifator do Azure, há dois modelos de uso disponíveis que são cobrados por meio de sua assinatura do Azure:

1. **Por usuário habilitado** – Para as empresas que desejam habilitar a verificação em duas etapas para um número fixo de funcionários que precisam regularmente de autenticação. A cobrança por usuário é baseada no número de usuários habilitados para o MFA no locatário do Azure AD e no Servidor do MFA do Azure. Se os usuários estão habilitados para MFA no Azure AD e o Azure MFA Server e sincronização de domínio (Azure AD Connect) estiver habilitada, podemos contar o maior conjunto de usuários. Se a sincronização de domínio não estiver habilitada, então podemos contar a soma de todos os usuários habilitados para MFA no Azure AD e o Azure MFA Server. O faturamento é rateado e registrado no sistema Comércio diariamente.

   > [!NOTE]
   > Exemplo de cobrança 1: Atualmente, você tem 5.000 usuários habilitados para MFA. O sistema MFA divide esse número por 31 e relatórios 161.29 usuários para esse dia. No dia seguinte, você habilita mais 15 usuários e, portanto, o sistema MFA relata 161,77 usuários para esse dia. No final do ciclo de cobrança, o número total de usuários cobrado em sua assinatura do Azure até adiciona em torno de 5.000.
   >
   > Exemplo de cobrança 2: Você tem uma mistura de usuários com e sem licenças, para que você tenha um provedor de MFA do Azure por usuário para fazer a diferença. Há 4.500 licenças do Enterprise Mobility + Security em seu locatário, mas 5.000 usuários habilitados para MFA. Sua assinatura do Azure é cobrada por 500 usuários, rateada e considerada 16.13 usuários diariamente.
   >

1. **Por Autenticação** - para empresas que querem habilitar a verificação em duas etapas para um grupo grande de usuários que raramente precisam de autenticação. A cobrança é baseada no número de solicitações de verificação em duas etapas, independentemente de essas verificações serem bem-sucedidas ou negadas. Essa cobrança é exibida no demonstrativo de uso do Azure em pacotes de 10 autenticações e é relatada diariamente.

   > [!NOTE]
   > Exemplo de cobrança 3: Atualmente, o serviço do Azure MFA recebeu 3.105 solicitações de verificação em duas etapas. Sua assinatura do Azure é cobrada por 310,5 pacotes de autenticação.
   >

É importante observar que você pode ter licenças, mas ainda é cobrado pela configuração com base no consumo. Se você configurar um provedor de MFA por autenticação do Azure, você será cobrado para cada solicitação de verificação em duas etapas, mesmo aquelas feitas por usuários com licenças. Se você configurar um provedor de MFA do Azure por usuário em um domínio que não está vinculado a seu locatário do Azure AD, você será cobrado por usuário habilitado, mesmo se os usuários possuem licenças no Azure AD.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais detalhes sobre preços, veja [Preços do Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

- Escolha se deseja implantar o Azure MFA [na nuvem ou no local](concept-mfa-whichversion.md)
