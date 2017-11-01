---
title: "Azure Active Directory B2C: Notas do desenvolvedor sobre o uso de políticas personalizadas | Microsoft Docs"
description: "Observações para os desenvolvedores sobre configuração e manutenção do B2C do Azure AD com políticas personalizadas"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/13/2017
ms.author: joroja
ms.openlocfilehash: 4fa4665115e0682df7c3fe3d8e2664a0f7a77a07
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2017
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Notas de versão para a versão prévia da política personalizada do Azure Active Directory B2C
O conjunto de recursos de política personalizada agora está disponível para avaliação em versão prévia para todos os clientes do Azure Active Directory B2C(Azure AD B2C). Esse conjunto de recursos se destina a desenvolvedores avançados de identidade para criar as soluções de identidade mais complexas.  

Atualmente, esse conjunto de recursos requer que os desenvolvedores configurem a Identity Experience Framework diretamente por meio da edição do arquivo XML. Esse método de configuração é poderoso e complexo. Os desenvolvedores de identidade avançada que usam a Identity Experience Framework devem planejar investir um tempo concluindo passo a passos e lendo os documentos de referência. 

## <a name="features-included-in-this-public-preview"></a>Recursos incluídos nesta versão prévia
Com os novos recursos introduzidos na revisão pública, os desenvolvedores podem executar as seguintes tarefas:<br>

* Criar e carregar percursos do usuário de autenticação personalizada usando políticas personalizadas. 
   * Descrever percursos do usuário passo a passo como trocas entre provedores de declarações. 
   * Definir a ramificação condicional em percursos do usuário. 
* Integrar os serviços habilitados para API REST aos seus percursos do usuário de autenticação personalizada.  
* Adicionar a federação a provedores de identidade em conformidade com o padrão OpenIDConnect. <br>
* Adicionar a federação com provedores de identidade que seguem o protocolo SAML 2.0. 

## <a name="terms-of-the-public-preview"></a>Termos da versão prévia

* Incentivamos o uso de novos recursos somente para fins de avaliação.<br>
* Os novos recursos não se destinam ao uso em um ambiente de produção.<br>
* Os contratos de nível de serviço (SLAs) não se aplicam aos novos recursos. <br>
* As solicitações de suporte podem ser arquivadas por meio dos canais de suporte normais. <br>
* Não há nenhuma data prometida para disponibilidade geral.<br>
* A nosso critério, e por qualquer motivo, a Microsoft pode sinalizar e rejeitar ou restringir os cenários e percursos do usuário que excedam o escopo da lista de produtos Azure AD B2C para servir como uma plataforma de gerenciamento de identidade e acesso de cliente (CIAM).

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilidades dos desenvolvedores de conjunto de recursos de política personalizada
A configuração de política manual concede acesso de nível inferior para a plataforma subjacente do Azure AD B2C e resulta na criação de uma estrutura de confiança exclusiva e totalmente personalizável. As permutas possíveis de provedores de identidade personalizada, relações de confiança, integrações com serviços externos e fluxos de trabalho passo a passo criam demandas ainda maiores para que os desenvolvedores avançados as consumam.

Para se beneficiar totalmente da versão prévia, sugerimos que os desenvolvedores que utilizarem o conjunto de recursos de política personalizada sigam as seguintes diretrizes:
* Familiarize-se com a linguagem de configuração da Estrutura de Experiência de Identidade e do gerenciamento de chave/segredos.
* Apropriar-se de cenários e de integrações personalizadas.
* Executar testes de cenário metódicos.
* Siga as práticas recomendadas de desenvolvimento/preparo de software com no mínimo um ambiente de desenvolvimento e teste um ambiente de produção.
* Mantenha-se informado sobre novos desenvolvimentos de provedores de identidade e de serviços para integração. Por exemplo, acompanhe as alterações em segredos e alterações programadas e não programadas no serviço.
* Configure o monitoramento ativo e monitore a capacidade de resposta dos ambientes de produção.
* Mantenha atualizados os endereços de email de contato na assinatura do Azure e permaneça responsivo aos emails da equipe de site ativo da Microsoft.
* Execute ação em tempo hábil quando for aconselhado a fazer isso pela equipe de site ativo da Microsoft. 

## <a name="features-by-stage-and-known-issues"></a>Recursos por estágio e problemas conhecidos
Recursos de Política Personalizada/Estrutura de Experiência de Identidade estão em constante e rápido desenvolvimento.  Esta tabela é um índice de disponibilidade de recursos/componente.

Poste perguntas no Stack Overflow em [aka.ms/aadb2cso](http://aka.ms/aadb2cso)


### <a name="identity-providers-tokens-protocols"></a>Provedores de Identidade, Tokens, Protocolos
Interfaces com aplicativos e componentes externos

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|---------------------------------------------|-------------|---------|----|-------|
| IDP-OpenIDConnect |  | x |  | por exemplo, Google+ |
| IDP-OAUTH2 |  | x |  | por exemplo, Facebook  |
| IDP-OAUTH1 |  | x |  | por exemplo, Twitter |
| IDP-SAML |  | x |  | por exemplo, Salesforce, ADFS |
| IDP-WSFED | x |  |  |  |
| OAUTH de terceira parte confiável |  | x |  |  |
| OIDC de terceira parte confiável |  | x |  |  |
| SAML de terceira parte confiável | x |  |  |  |
| WSFED de terceira parte confiável | x |  |  |  |
| API REST com autenticação basic e cert. |  | x |  | por exemplo, Azure Functions |


### <a name="component-support"></a>Suporte do componente


| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------------------------------------------|-------------|---------|----|-------|
| Autenticação Multifator do Azure |  | x |  |  |
| Azure Active Directory como diretório local |  | x |  |  |
| Subsistema de Email do Azure para 2FA |  | x |  |  |
| Suporte a vários idiomas|  | x |  |  |
| Complexidade de senha | x |  |  |  |


### <a name="content-definition"></a>Definição de conteúdo

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-----------------------------------------------------------------------------|-------------|---------|----|-------|
|   Página de erro, api.error |  | x |  |  |
|   Página de seleção de IDP, api.idpselections |  | x |  |  |
|   Seleção de IDP para inscrição, api.idpselections.signup |  | x |  |  |
|   Esqueceu a senha, api.localaccountpasswordreset |  | x |  |  |
|   Entrada na Conta Local, api.localaccountsignin |  | x |  |  |
|   Inscrição na Conta Local, api.localaccountsignup |  | x |  |  |
|   Página MFA, api.phonefactor |  | x |  |  |
|   Autodeclarada, por exemplo, entrada em conta social, api.selfasserted |  | x |  |  |
|   Atualização de perfil autodeclarada, api.selfasserted.profileupdate |  | x |  |  |
|   Página de inscrição ou entrada unificada, api.signuporsignin |  | x |  |  |


### <a name="app-ief-integration"></a>Integração de aplicativo IEF
| Recurso | Desenvolvimento | Visualização | GA | Observações |
|--------------------------------------------------|-------------|---------|----|-------------------------------------------------|
| Parâmetro de cadeia de consulta id_token_hint | x |  |  |  |
| Parâmetro de cadeia de consulta domain_hint |  | x |  | disponível como declaração, pode ser passado para IDP |
| Parâmetro de cadeia de consulta login_hint |  | x |  | disponível como declaração, pode ser passado para IDP |
| Inserir JSON UserJourney via client_assertion | x |  |  | será preterido |
| Inserir JSON em UserJourney como id_token_hint | x |  |  | abordagem avançada para passar JSON |


### <a name="session-management"></a>Gerenciamento da sessão

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|---------------------------------|-------------|---------|----|-------|
| Provedor de sessão SSO |  | x |  |  |
| Provedor de sessão de logon externo |  | x |  |  |
| Provedor de sessão de SSO do SAML |  | x |  |  |


### <a name="security"></a>Segurança
| Recurso | Desenvolvimento | Visualização | GA | Observações |
|---------------------------------------------|-------------|---------|----|-------|
| Chaves de política – gerar, manual, upload |  | x |  |  |
| Chaves de política – RSA/Cert, segredos |  | x |  |  |


### <a name="developer-interface"></a>Interface do desenvolvedor
| Recurso | Desenvolvimento | Visualização | GA | Observações |
|---------------------------------------------|-------------|---------|----|-------|
| Portal do Azure – IEF UX |  | x |  |  |
| Logs UserJourney do Application Insights  |  | x |  |  |
| Logs de eventos do Application Insights |x|  |  |  |



## <a name="next-steps"></a>Próximas etapas
[Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).
