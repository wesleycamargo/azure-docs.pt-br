---
title: Aplicativos de página única no Azure Active Directory
description: Descreve quais são os SPAs (aplicativos de página única) e as noções básicas sobre fluxo de protocolo, registro e expiração de token para esse tipo de aplicativo.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6704c00734d18b9918378db6e056696407421014
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207170"
---
# <a name="single-page-applications"></a>Aplicativos de página única

Os SPAs (aplicativos de página única) normalmente são estruturados como uma camada de apresentação do JavaScript (front-end) que é executada no navegador e um back-end de API Web que é executado em um servidor e implementa a lógica de negócios do aplicativo. Para saber mais sobre a concessão de autorização implícita e ajudá-lo a decidir se ela é adequada para seu cenário de aplicativo, veja [Noções básicas sobre o fluxo de concessão implícita do OAuth2 no Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

Nesse cenário, quando o usuário faz logon, o front-end de JavaScript usa a [Biblioteca de Autenticação do Active Directory para JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) e a concessão de autorização implícita para obter um token de ID (id_token) do Azure AD. O token é armazenado em cache e o cliente o anexa à solicitação como o token de portador ao fazer chamadas paro o back-end de sua API da Web, que é protegido usando o middleware OWIN.

## <a name="diagram"></a>Diagrama

![Diagrama de aplicativo de página única](./media/authentication-scenarios/single_page_app.png)

## <a name="protocol-flow"></a>Fluxo do protocolo

1. O usuário navega até o aplicativo Web.
1. O aplicativo retorna o front-end de JavaScript (camada de apresentação) para o navegador.
1. O usuário inicia o logon, por exemplo, clicando em um link de logon. O navegador envia um GET ao ponto de extremidade de autorização do Azure AD para solicitar um token de ID. Essa solicitação inclui a ID do aplicativo e a URL de resposta nos parâmetros de consulta.
1. O Azure AD valida a URL de Resposta em relação à URL de Resposta registrada que foi configurada no portal do Azure.
1. O usuário faz logon na página de logon.
1. Se a autenticação for bem-sucedida, o Azure AD cria um token de ID e o retorna como um fragmento de URL (#) à URL de resposta do aplicativo. Para um aplicativo de produção, essa URL de resposta deve ser HTTPS. O token retornado inclui declarações sobre o usuário e o Azure AD que são exigidas pelo aplicativo para validar o token.
1. O código de cliente do JavaScript em execução no navegador extrai o token da resposta para usá-lo para proteger chamadas ao back-end da API da Web do aplicativo.
1. O navegador chama o back end da API da Web do aplicativo com o token de ID no cabeçalho de autorização. O serviço de autenticação do Azure AD emite um token de ID que pode ser usado como um token de portador se o recurso for o mesmo que o ID do cliente (nesse caso, isso ocorre porque a API da Web é o próprio back-end do aplicativo).

## <a name="code-samples"></a>Exemplos de código

Veja os [exemplos de código para cenários de aplicativo de página única](sample-v1-code.md#single-page-applications). Volte sempre, pois novos exemplos são adicionados com frequência.

## <a name="app-registration"></a>Registro do aplicativo

* Locatário único: se você estiver criando um aplicativo apenas para a sua organização, ele deverá ser registrado no diretório da sua empresa usando o portal do Azure.
* Multilocatário – Se você estiver criando um aplicativo que pode ser usado por usuários fora da sua organização, ele deverá ser registrado no diretório da sua empresa, mas também deverá ser registrado no diretório de cada organização que usará o aplicativo. Para disponibilizar seu aplicativo em seu diretório, você pode incluir um processo de inscrição para os clientes que os permita ter autorização para seu aplicativo. Ao se inscreverem para seu aplicativo, uma caixa de diálogo será apresentada, mostrando as permissões exigidas pelo aplicativo e, em seguida, a opção de consentimento. Dependendo das permissões necessárias, um administrador na outra organização talvez precise dar consentimento. Quando o usuário ou administrador der seu consentimento, o aplicativo é registrado em seu diretório.

Depois de registrar o aplicativo, ele deve ser configurado para usar o protocolo OAuth 2.0 de concessão implícita. Por padrão, esse protocolo é desabilitado para aplicativos. Para habilitar o protocolo de concessão implícita OAuth2, edite o manifesto do aplicativo no portal do Azure e defina o valor de "oauth2AllowImplicitFlow" como true. Para obter mais informações, veja [Manifesto do aplicativo](reference-app-manifest.md).

## <a name="token-expiration"></a>Expiração do token

Usar o ADAL.js ajuda a:

* Atualizar um token expirado
* Solicitar um token de acesso para chamar um recurso da API Web

Após uma autenticação bem-sucedida, o Azure AD grava um cookie no navegador do usuário para estabelecer uma sessão. A sessão existe entre o usuário e o Azure AD, e não entre o usuário e o aplicativo Web. Quando um token expira, o ADAL.js usa essa sessão para obter silenciosamente outro token. O ADAL.js faz isso usando um iFrame oculto para enviar e receber a solicitação usando o protocolo de concessão implícita OAuth. O ADAL.js também pode usar o mesmo mecanismo para obter silenciosamente os tokens de acesso para outros recursos da API da Web que o aplicativo chama, contanto que esses recursos ofereçam suporte a CORS (compartilhamento de recursos entre origens), que eles estejam registrados no diretório do usuário e que qualquer consentimento necessário tenha sido fornecido pelo usuário durante o logon.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [Tipos e cenários de aplicativo](app-types.md)
* Saiba mais sobre as [noções básicas de autenticação](authentication-scenarios.md) do Azure AD
