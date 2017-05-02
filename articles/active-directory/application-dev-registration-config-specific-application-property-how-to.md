---
title: "Como preencher campos específicos para um aplicativo personalizado | Microsoft Docs"
description: "Orientação sobre como preencher campos específicos ao registrar um aplicativo personalizado com o Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 9a57185938ebe9af0ca1395e75545473f45b9889
ms.lasthandoff: 04/17/2017


---

# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Como preencher campos específicos para um aplicativo personalizado

Este artigo oferece uma breve descrição de todos os campos disponíveis no formulário de registro do aplicativo no [portal do Azure](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registrar um novo aplicativo

-   Para registrar um novo aplicativo, navegue até o [portal do Azure](https://portal.azure.com).

-   No painel de navegação à esquerda, clique em **Azure Active Directory.**

-   Clique em **Registros do aplicativo** e clique em **Adicionar**.

-   Isso abre o formulário de registro do aplicativo.

## <a name="fields-in-the-application-registration-form"></a>Campos do formulário de registro do aplicativo


| Campo            | Descrição                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Nome             | O nome do aplicativo. Ele deve ter um mínimo de quatro caracteres.                |
| Tipo de aplicativo | **Aplicativo Web/API Web**: um aplicativo que representa um aplicativo Web, uma API Web ou ambos 
| |**Nativo**: um aplicativo que pode ser instalado no dispositivo ou computador de um usuário           |
| URL de logon      | A URL na qual os usuários podem entrar para usar o aplicativo                                  |

Depois de preencher os campos acima, o aplicativo é registrado no portal do Azure e redirecionado para a página de aplicativos. O botão **Configurações** no painel do aplicativo abre a página Configurações, que tem mais campos para você personalizar seu aplicativo. A tabela a seguir descreve todos os campos na página Configurações. Observe que você verá apenas um subconjunto desses campos, dependendo do aplicativo criado: aplicativo Web ou um aplicativo nativo.

| Campo           | Descrição                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID do aplicativo  | Ao registrar um aplicativo, o Azure AD atribui a seu aplicativo uma ID de aplicativo. A ID do aplicativo pode ser usada para identificar exclusivamente o aplicativo nas solicitações de autenticação no Azure AD, além de acessar recursos, como a API do Graph.                                                          |
| URI de ID do aplicativo      | Deve ser um URI exclusivo, geralmente do formulário **https://&lt;tenant\_name&gt;/&lt;application\_name&gt;.** Isso é usado durante o fluxo de concessão de autorização, como um identificador exclusivo para especificar o recurso para o qual o token deve ser emitido. Ele também torna-se a declaração 'aud' no token de acesso emitido. |
| Carregar novo logotipo | Você pode usar isso para carregar um logotipo no aplicativo. O logotipo deve estar no formato .bmp, .jpg ou .png e o tamanho do arquivo deve ser inferior a 100 KB. As dimensões da imagem devem ser 215x215 pixels, com as dimensões da imagem central de 94x94 pixels.                                                       |
| URL da home page   | Esta é a URL de logon especificada durante o registro do aplicativo.                                                                                                                                                                                                                                              |
| URL de logout      | É o URL de logout único. O Azure AD envia uma solicitação de logout para esse URL quando o usuário limpa a sessão com o Azure AD usando qualquer outro aplicativo registrado.                                                                                                                                       |
| Multilocatário  | Essa opção especifica se o aplicativo pode ser usado por vários locatários. Normalmente, isso significa que as organizações externas podem usar seu aplicativo registrando-o em seu locatário e concedendo acesso aos dados da organização.                                                                   |
| URLs de resposta      | As URLs de resposta são pontos de extremidade em que o Azure AD retorna tokens que seu aplicativo solicita.                                                                                                                                                                                                          |
| URIs de redirecionamento   | Para aplicativos nativos, é para onde o usuário é enviado após autorização bem-sucedida. O Azure AD verifica se o URI de redirecionamento que seu aplicativo fornece no OAuth 2.0 corresponde a um dos valores registrados no portal.                                                            |
| simétricas            | Você pode criar chaves para acessar APIs Web programaticamente protegido pelo Azure AD e sem qualquer interação do usuário. Na página \*\*Chaves\*\*, insira uma descrição da chave e a data de validade e salve para gerar a chave. Certifique-se de salvá-la em algum lugar seguro, pois você não poderá acessá-la depois.             |

## <a name="next-steps"></a>Próximas etapas
[Gerenciando aplicativos com o Azure Active Directory](active-directory-enable-sso-scenario.md)

