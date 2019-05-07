---
title: Integração à plataforma de identidade da Microsoft | Azure
description: Saiba mais sobre as práticas recomendadas e descuidos comuns ao integrar com a plataforma de identidade da Microsoft (v2.0).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: celested
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev
ms.openlocfilehash: 1f5a6d7501fd387548b111fcb0888515c8f8db25
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076399"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Lista de verificação de integração do Microsoft identity platform

A lista de verificação de integração de plataforma de identidade de Microsoft destina-se para guiá-lo para uma integração segura e de alta qualidade. Ele destaca as práticas recomendadas e descuidos comuns ao se integrar com a plataforma de identidade da Microsoft então, examine a lista regularmente para certificar-se de que manter a qualidade e segurança da integração do seu aplicativo com a plataforma de identidade. A lista de verificação não se destina a examinar todo o seu aplicativo. O conteúdo da lista de verificação está sujeitos a alterações à medida que fizermos melhorias para a plataforma.

Se você estiver apenas começando, confira a [documentação](index.yml) para saber mais sobre conceitos básicos de autenticação, cenários de aplicativos na plataforma de identidade da Microsoft e muito mais.

## <a name="testing-your-integration"></a>Testar sua integração

Use a seguinte lista de verificação para garantir que seu aplicativo com eficiência é integrado com o [plataforma de identidade Microsoft](https://docs.microsoft.com/legal/mdsa).

### <a name="basics"></a>Noções básicas

|   |   |
|---|---|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Ler e entender as [as políticas da plataforma Microsoft](https://docs.microsoft.com/legal/mdsa). Certifique-se de que seu aplicativo cumpre com os termos descritos conforme eles são projetados para proteger os usuários e a plataforma. |

### <a name="ownership"></a>Propriedade

|   |   |
|---|---|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Verifique se as informações associadas à conta usada para registrar e gerenciar aplicativos estão atualizadas. |

### <a name="branding"></a>Identidade visual

|   |   |
|---|---|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Seguem os [diretrizes para aplicativos de identidade visual](howto-add-branding-in-azure-ad-apps.md). |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Forneça um nome significativo e o logotipo para seu aplicativo. Essas informações são exibidas na solicitação de consentimento do seu aplicativo. Verifique se que o nome e logotipo são representativas de sua empresa/produto para que os usuários podem tomar decisões informadas. Certifique-se de que você não está violando qualquer registradas. |

### <a name="privacy"></a>Privacidade

|   |   |
|---|---|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Fornece links para os termos do seu aplicativo de serviço e a declaração de privacidade. |

### <a name="security"></a>Segurança

|   |   |
|---|---|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Mantenha a propriedade de todos os seus URIs de redirecionamento e manter os registros DNS para que eles atualizados. Não use caracteres curinga (*) em seus URIs. Para aplicativos web, verifique se todos os URIs são protegidas e criptografadas (por exemplo, usando esquemas de https). Para clientes públicos, use os URIs de redirecionamento de específico da plataforma se aplicável (principalmente para iOS e Android). Caso contrário, use URIs de redirecionamento com uma grande quantidade de aleatoriedade para impedir colisões quando chamar de volta ao seu aplicativo. Se seu aplicativo estiver sendo usado por um agente da web isolado, você pode usar https://login.microsoftonline.com/nativeclient. Examine e cortar todos os não usados ou desnecessários URIs de redirecionamento regularmente. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Se seu aplicativo for registrado em um diretório, minimizar e monitorar manualmente a lista de proprietários de registro de aplicativo. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Não habilite o suporte para o [fluxo de concessão implícita OAuth2](v2-oauth2-implicit-grant-flow.md) , a menos que explicitamente necessárias. Saiba mais sobre o cenário válido [aqui](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Não use [o fluxo da credencial de senha de proprietário do recurso (ROPC)](v2-oauth-ropc.md), que manipula diretamente as senhas dos usuários. Este fluxo requer um alto grau de exposição de confiança e de usuário e só deve ser usado quando os fluxos de outros, mais seguros, não podem ser usados. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Proteja e gerencie suas credenciais do aplicativo. Use [credenciais de certificado](active-directory-certificate-credentials.md), não as credenciais de senha (segredos do cliente). Se você precisar usar uma credencial de senha, não defini-lo manualmente. Não armazenar credenciais no código ou configuração e nunca permitir que eles sejam tratadas por seres humanos. Se possível, use [identidades para recursos do Azure gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ou [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) para armazenar e regularmente gire suas credenciais. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Verifique se que seu aplicativo solicita as permissões com privilégios mínimos. Solicitar apenas as permissões que seu aplicativo sejam absolutamente necessárias e somente quando precisar deles. Entender os diferentes [tipos de permissões](v1-permissions-and-consent.md#types-of-permissions). Usar somente permissões de aplicativo se necessário; Use permissões delegadas, sempre que possível. Para obter uma lista completa de permissões do Microsoft Graph, consulte este [referência de permissões](https://docs.microsoft.com/graph/permissions-reference). |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Se você estiver protegendo uma API usando a plataforma de identidade da Microsoft, considere criteriosamente as permissões que ele deve expor. Considere o que é a granularidade correta para sua solução e quais permissões exigem o consentimento do administrador. Verificar permissões esperadas nos tokens de entrada antes de tomar qualquer decisão de autorização. |

### <a name="implementation"></a>Implementação

|   |   |
|---|---|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Usar soluções de autenticação moderna (OAuth 2.0 [OpenID Connect](v2-protocols-oidc.md)) para conectar com segurança os usuários. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Não implementar os protocolos por conta própria – use [bibliotecas de autenticação com suporte da Microsoft](reference-v2-libraries.md) (MSAL, middleware de servidor). Verifique se que você estiver usando a versão mais recente da biblioteca de autenticação que você tenha integrado com o. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Se os dados que requer que seu aplicativo estão disponíveis por meio [Microsoft Graph](https://developer.microsoft.com/graph), solicitar permissões para esses dados usando o ponto de extremidade do Microsoft Graph em vez da API individual. |

### <a name="end-user-experience"></a>Experiência do usuário final

|   |   |
|---|---|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | [Entender a experiência de consentimento](application-consent-experience.md) e configure as partes da solicitação de consentimento do seu aplicativo para que os usuários finais e administradores têm informações suficientes para determinar se confiam em seu aplicativo. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Minimizar o número de vezes que um usuário precisa inserir credenciais de logon ao usar o seu aplicativo pela tentativa de silenciosa autenticação (aquisição de token silenciosa) antes de fluxos interativos. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Não use "prompt = consent" para cada entrada. Usar somente o prompt = consent se você tiver determinado que você precisa pedir consentimento para permissões adicionais (por exemplo, se você tiver alterado as permissões necessárias do aplicativo). |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Quando aplicável, enriquecer seu aplicativo com dados de usuário. Use o [API do Microsoft Graph](https://developer.microsoft.com/graph) é uma maneira fácil de fazer isso. O [Graph explorer](https://developer.microsoft.com/graph/graph-explorer) ferramenta que pode ajudá-lo a começar. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Registre-se o conjunto completo de permissões que seu aplicativo requer para que os administradores podem conceder autorização facilmente ao seu locatário. Use [consentimento incremental](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) em tempo de execução para ajudar os usuários a entender por que seu aplicativo está solicitando permissões que podem referem-se ou confundir os usuários quando solicitado na primeira inicialização. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementar uma [limpar a experiência de logout único](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Ele é de privacidade e um requisito de segurança e torna-se para uma boa experiência do usuário. |

### <a name="testing"></a>Testando

|   |   |
|---|---|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Testar [políticas de acesso condicional](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) que podem afetar a capacidade dos usuários para usar o aplicativo. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Teste seu aplicativo com todas as contas possíveis que você planeja oferecer suporte (por exemplo, trabalho ou contas de estudante, contas pessoais da Microsoft, contas da criança e soberanas contas). |

## <a name="additional-resources"></a>Recursos adicionais

Explore informações detalhadas sobre a v2.0:

* [Plataforma de identidade da Microsoft (visão geral do v 2.0)](v2-overview.md)
* [Referência de protocolos de plataforma de identidade Microsoft](active-directory-v2-protocols.md)
* [Referência de tokens de acesso](access-tokens.md)
* [Referência de tokens de ID](id-tokens.md)
* [Referência de bibliotecas de autenticação](reference-v2-libraries.md)
* [Permissões e consentimento na plataforma de identidade da Microsoft](v2-permissions-and-consent.md)
* [API do Microsoft Graph](https://developer.microsoft.com/graph)