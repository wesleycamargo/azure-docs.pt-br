---
title: Notas do desenvolvedor para políticas personalizadas – Azure Active Directory B2C | Microsoft Docs
description: Observações para os desenvolvedores sobre a configuração e a manutenção do Azure AD B2C com políticas personalizadas.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: cf9c6f6a54c38f00e477e2a9d62e72ab5faccdef
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418917"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Notas do desenvolvedor para políticas personalizadas no Azure Active Directory B2C

Configuração de política personalizada no Azure Active Directory B2C agora está geralmente disponível. Esse método de configuração é destinado a desenvolvedores avançados de identidade criando soluções complexas de identidade. Políticas personalizadas disponibilizar o poder da estrutura de experiência de identidade nos locatários do Azure AD B2C. Avançada de identidade usando políticas personalizadas dos desenvolvedores devem planejar investir algum tempo Concluindo passo a passos e lendo os documentos de referência.

Embora a maioria das opções de política personalizada disponíveis agora está disponível, há recursos subjacentes, como tipos de perfil técnico e as APIs que estão em diferentes estágios do ciclo de vida do software de definição de conteúdo. Muitos mais estarão disponíveis. A tabela a seguir especifica o nível de disponibilidade em um nível mais granular.  

## <a name="features-that-are-generally-available"></a>Recursos que estão disponíveis

- Criar e carregar percursos do usuário de autenticação personalizada usando políticas personalizadas.  
    - Descrever percursos do usuário passo a passo como trocas entre provedores de declarações.  
    - Definir a ramificação condicional em percursos do usuário.  
- Interoperar com serviços habilitados para API REST no seu percurso do usuário de autenticação personalizada.  
- Federação com provedores de identidade que estão em conformidade com o protocolo OpenIDConnect.  
- Federação com provedores de identidade que seguem o protocolo SAML 2.0.   

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilidades dos desenvolvedores de conjunto de recursos de política personalizada

Configuração de política manual concede acesso de nível inferior para a plataforma subjacente do Azure AD B2C e resulta na criação de uma estrutura exclusiva, de confiança. As muitas permutações possíveis de provedores de identidade personalizada, relações de confiança, integrações com serviços externos e fluxos de trabalho passo a passo exigem uma abordagem metódica para design e configuração. 

Os desenvolvedores que utilizarem o conjunto de recursos de política personalizada devem cumprir as diretrizes a seguir:

- Familiarize-se com a linguagem de configuração do gerenciamento de políticas e a chave/segredos personalizado. Para obter mais informações, consulte [TrustFrameworkPolicy](trustframeworkpolicy.md). 
- Apropriar-se de cenários e de integrações personalizadas. Documente seu trabalho e informar sua organização de site ativo.  
- Executar testes de cenário metódicos. 
- Siga as práticas recomendadas de desenvolvimento/preparo de software com no mínimo um ambiente de desenvolvimento e teste um ambiente de produção. 
- Mantenha-se informado sobre novos desenvolvimentos de provedores de identidade e de serviços para integração. Por exemplo, acompanhe as alterações em segredos e alterações programadas e não programadas no serviço. 
- Configure o monitoramento ativo e monitore a capacidade de resposta dos ambientes de produção. Para obter mais informações sobre a integração com o Application Insights, consulte [do Azure Active Directory B2C: Coletando Logs](active-directory-b2c-custom-guide-eventlogger-appins.md). 
- Mantenha atualizados os endereços de email de contato na assinatura do Azure e permaneça responsivo aos emails da equipe de site ativo da Microsoft. 
- Execute ação em tempo hábil quando for aconselhado a fazer isso pela equipe de site ativo da Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Termos de recursos em visualização pública

- Incentivamos você a usar os recursos de visualização pública apenas a fins de avaliação. 
- Contratos de nível de serviço (SLAs) não se aplicam aos recursos de visualização pública.
- Solicitações de suporte para recursos de visualização pública podem ser arquivadas por meio de canais de suporte normais. 

## <a name="features-by-stage-and-known-issues"></a>Recursos por estágio e problemas conhecidos

Recursos de estrutura de experiência de identidade/diretiva personalizada estão sob constante e rápido desenvolvimento. A tabela a seguir é um índice de recursos e a disponibilidade do componente.

### <a name="identity-providers-tokens-protocols"></a>Provedores de Identidade, Tokens, Protocolos

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------- | ----------- | ------- | -- | ----- |
| IDP-OpenIDConnect |  |  | X | Por exemplo, Google +.  |
| IDP-OAUTH2 |  |  | X | Por exemplo, o Facebook.  |
| IDP-OAUTH1 (twitter) |  | X |  | Por exemplo, do Twitter. |
| IDP-OAUTH1 (twitter ex) |  |  |  | Sem suporte |
| IDP-SAML |  |   | X | Por exemplo, Salesforce, ADFS. |
| IDP-WSFED | X |  |  |  |
| Festa OAUTH1 da terceira parte confiável |  |  |  | Sem suporte. |
| Festa OAUTH2 da terceira parte confiável |  |  | X |  |
| OIDC de terceira parte confiável |  |  | X |  |
| SAML de terceira parte confiável | X |  |  |  |
| WSFED de terceira parte confiável | X |  |  |  |
| A API REST com o basic e autenticação de certificado |  |  | X | Por exemplo, aplicativos lógicos do Azure. |

### <a name="component-support"></a>Suporte do componente

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | ----------- | ------- | -- | ----- |
| Autenticação Multifator do Azure |  |  | X |  |
| Azure Active Directory como diretório local |  |  | X |  |
| Subsistema de Email do Azure para verificação de email |  |  | X |  |
| Suporte a vários idiomas|  |  | X |  |
| Validações de predicado |  |  | X | Por exemplo, a complexidade de senha. |
| Usando provedores de serviços de email de terceiros | X |  |  |  |

### <a name="content-definition"></a>Definição de conteúdo

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | ----------- | ------- | -- | ----- |
| Página de erro, api.error |  |  | X |  |
| Página de seleção de IDP, api.idpselections |  |  | X |  |
| Seleção de IDP para inscrição, api.idpselections.signup |  |  | X |  |
| Esqueceu a senha, api.localaccountpasswordreset |  |  | X |  |
| Entrada na Conta Local, api.localaccountsignin |  |  | X |  |
| Inscrição na Conta Local, api.localaccountsignup |  |  | X |  |
| Página MFA, api.phonefactor |  |  | X |  |
| Autodeclarada api.selfasserted de inscrição, conta social |  |  | X |  |
| Atualização de perfil autodeclarada, api.selfasserted.profileupdate |  |  | X |  |
| Página de entrada, API. signuporsignin, com o parâmetro "disableSignup" ou de inscrição unificada |  |  | X |  |
| JavaScript / página de contrato |  | X |  |  |

### <a name="app-ief-integration"></a>Integração de aplicativo IEF

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | ----------- | ------- | -- | ----- |
| Parâmetro de cadeia de consulta domain_hint |  |  | X | Disponível como declaração, pode ser passado para IDP. |
| Parâmetro de cadeia de consulta login_hint |  |  | X | Disponível como declaração, pode ser passado para IDP. |
| Inserir JSON UserJourney via client_assertion | X |  |  | Será preterido. |
| Inserir JSON em UserJourney como id_token_hint |  | X |  | Abordagem avançada para passar JSON. |
| Passar um TOKEN do IDP para o aplicativo |  | X |  | Por exemplo, do Facebook para o aplicativo. |

### <a name="session-management"></a>Gerenciamento da sessão

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | ----------- | ------- | -- | ----- |
| Provedor de sessão SSO |  |  | X |  |
| Provedor de sessão de logon externo |  |  | X |  |
| Provedor de sessão de SSO do SAML |  |  | X |  |
| Provedor de sessão SSO padrão |  |  | X |  |

### <a name="security"></a>Segurança

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------- | ----------- | ------- | -- | ----- |
| Chaves de política – gerar, manual, upload |  |  | X |  |
| Chaves de política – RSA/Cert, segredos |  |  | X |  |
| Upload de Política |  |  | X |  |

### <a name="developer-interface"></a>Interface do desenvolvedor

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | ----------- | ------- | -- | ----- |
| Portal do Azure – IEF UX |  |  | X |  |
| Logs UserJourney do Application Insights |  | X |  | Usado para solucionar problemas durante o desenvolvimento.  |
| Logs de evento do Application Insights (por meio de etapas de orquestração) |  | X |  | Usado para monitorar os fluxos de usuário na produção. |

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre as políticas personalizadas e as diferenças com fluxos de usuário](active-directory-b2c-overview-custom.md).
