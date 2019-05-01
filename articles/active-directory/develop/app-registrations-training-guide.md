---
title: Registros de aplicativo no guia de treinamento do portal do Azure - Azure
description: Crie fluxos de autenticação inseridos e sem navegador usando a concessão de código do dispositivo.
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 118c6ecb16d325a384246a0b3d9e685f6f6f04ee
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870111"
---
# <a name="training-guide-app-registrations-in-the-azure-portal"></a>Guia de treinamento: Registros de aplicativo no portal do Azure  

Você pode encontrar várias melhorias no novo [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência no portal do Azure. Se você estiver mais familiarizado com a experiência herdada, use este guia de treinamento para ajudá-lo a começar a usar a nova experiência.

## <a name="key-changes"></a>Alterações de chave

- Registros de aplicativo não estão limitados a que está sendo a um **aplicativo web/API** ou um **nativo** aplicativo. Você pode usar o mesmo registro do aplicativo para todos esses, registrando o respectivo URIs de redirecionamento.
- Os aplicativos de experiência herdada com suporte que a entrada do organizacional (contas do Azure AD) apenas. Aplicativos que foram registrados como locatário único (que dão suporte a contas institucionais somente do aplicativo foi registrado no diretório) e pode ser modificado para ser multilocatário (que dão suporte a todas as contas organizacionais). A nova experiência lhe permite registrar aplicativos que podem dar suporte a ambos os essas opções, bem como uma terceira opção: todas as contas organizacionais, bem como contas pessoais da Microsoft.
- A experiência herdada só estava disponível depois de entrar no portal do Azure usando uma conta organizacional. Com a nova experiência, você pode usar contas pessoais da Microsoft que não estão associadas um diretório.

## <a name="list-of-applications"></a>Lista de aplicativos

- A nova lista de aplicativos mostra os aplicativos que foram registrados por meio do aplicativo herdado experiência de registros no portal do Azure (aplicativos que entrar em contas do Azure AD), bem como aplicativos registrados no entanto o [Portal de registro de aplicativo](https://apps.dev.microsoft.com/) (aplicativos que a entrada no Azure AD e contas pessoais da Microsoft).
- A nova lista de aplicativo não tem um **tipo de aplicativo** coluna (uma vez que um único registro de aplicativo pode ser vários tipos) e tem duas colunas adicionais: um **criado na** coluna e um **certificados & segredos** coluna que mostra o status (atual, expirando logo ou expirado) de credenciais que foram registradas no aplicativo.

## <a name="new-app-registration"></a>Novo registro de aplicativo

Na experiência herdada, para registrar um aplicativo, você foi solicitado a fornecer: **Nome da**, **tipo de aplicativo**, e **fazer logon no URI de redirecionamento deURL/**. Os aplicativos que foram criados eram aplicativos de locatário único somente do Azure AD que significa que eles só tem suporte a contas institucionais do diretório em que o aplicativo foi registrado no.

Na nova experiência, você deve fornecer um **nome** para o aplicativo e escolha o **suporte para tipos de conta**. Opcionalmente, você pode fornecer um **URI de redirecionamento**. Se você fornecer um URI de redirecionamento, você precisará especificar se é web/pública (móvel e desktop). Para obter mais informações sobre como registrar um aplicativo usando os registros de aplicativo nova experiência, consulte [este guia de início rápido](quickstart-register-app.md).

## <a name="the-legacy-properties-page"></a>A página de propriedades herdada

A experiência herdada tinha uma **propriedades** página que não tem a nova experiência. O **propriedades** folha teve os seguintes campos: **Nome da**, **ID do objeto**, **ID do aplicativo**, **URI da ID do aplicativo**, **logotipo**, **URL da Home page** , **URL de logoff**, **termos da URL de serviço**, **política de privacidade**, **tipo de aplicativo**, e  **Vários locatários.**

Aqui é onde você pode encontrar a funcionalidade equivalente na nova experiência:

- **Nome da**, **logotipo**, **URL da Home page**, **termos de URL do serviço**, e **política de privacidade** agora, é o aplicativo  **Identidade visual** página.
- **ID de objeto** e **ID do aplicativo (cliente)** é sobre o **visão geral** página.
- A funcionalidade controlada pela **multilocatário** alternância na experiência herdada foi substituída pelo **tipos de conta com suporte** no **autenticação** página. Para obter mais informações sobre como multilocatário é mapeada para as opções de tipo de conta com suporte, consulte [este guia de início rápido](quickstart-modify-supported-accounts.md).
- **URL de logoff** agora, é o **autenticação** página.
- **Tipo de aplicativo** não é mais um campo válido. Em vez disso, os URIs de redirecionamento (que pode ser encontrado na **autenticação** página) determinam quais tipos de aplicativo têm suporte.
- **URI da ID do aplicativo** agora é chamado **URI da ID do aplicativo** e você pode encontrá-lo sobre o **expor uma API** folha. Na experiência herdada, essa propriedade foi registrado automaticamente usando o seguinte formato: `https://{tenantdomain}/{appID}` (por exemplo, `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). No novo formato, ele é gerado automaticamente como `api://{appID}`, mas ele precisa ser salvo explicitamente.

## <a name="reply-urlsredirect-urls"></a>URls de redirecionamento/URLs de resposta

Na experiência herdada, um aplicativo tinha uma **URLs de resposta** página. Na nova experiência, URLs de resposta podem ser encontradas em um aplicativo **autenticação** seção. Além disso, eles são denominados **URIs de redirecionamento**. Além disso, o formato para redirecionamento de URIs foi alterado. Elas são necessárias a serem associados com um tipo de aplicativo (web ou público). Além disso, por motivos de segurança, os esquemas de curingas e http:// não dá suporte (com exceção do http://localhost).

## <a name="keyscertificates--secrets"></a>Os certificados/chaves e segredos

Na experiência herdada, um aplicativo tinha **chaves** página. Na nova experiência, ele foi renomeado para **certificados e segredos**. Além disso, **chaves públicas** são denominados **certificados** e **senhas** são denominados **segredos de cliente**.

## <a name="required-permissionsapi-permissions"></a>Permissões de API/permissões necessárias

- Na experiência herdada, um aplicativo tinha uma **permissões necessárias** página. Na nova experiência, ele foi renomeado para **permissões de API**.
- Ao selecionar uma API em experiência herdada, você pode escolher entre uma pequena lista de APIs da Microsoft ou a pesquisa por meio de entidades de serviço no locatário. Na nova experiência, você pode escolher entre várias guias: **APIs da Microsoft**, **APIs minha organização utiliza**, ou **Minhas APIs**. A barra de pesquisa no **APIs minha organização** usa guia pesquisas por meio de entidades de serviço no locatário. 

   > [!NOTE]
   > Se seu aplicativo não está associado a um locatário, você não verá essa guia. Para obter mais informações sobre como solicitar permissões usando a nova experiência, consulte [este guia de início rápido](quickstart-configure-app-access-web-apis.md).

- A experiência herdada tinha uma **conceder permissões** botão na parte superior do **permissões solicitadas** página. Na nova experiência, há uma **conceder consentimento** seção com um **conceder consentimento do administrador** botão em um aplicativo **permissões de API** seção. Além disso, há algumas diferenças das maneiras a função de botões:
   - Na experiência herdada, a lógica variam, dependendo do usuário conectado e as permissões que está sendo solicitadas. A lógica era:
      - Se apenas permissões capaz de consentimento do usuário foram sendo solicitadas e o usuário conectado não era um administrador, o usuário foi capaz de conceder consentimento do usuário para as permissões solicitadas.
      - Se pelo menos uma permissão que requer o consentimento do administrador estava sendo solicitada e o usuário conectado não era um administrador, o usuário obteve um erro ao tentar conceder autorização.
      - Se o usuário conectado for um administrador, o consentimento do administrador foi concedido para todas as permissões solicitadas.
   - Na nova experiência, somente um administrador pode conceder consentimento. Quando um administrador seleciona os **conceder consentimento do administrador** botão, o consentimento do administrador é concedido para todas as permissões solicitadas.

## <a name="deleting-an-app-registration"></a>Excluindo um registro de aplicativo

Na experiência herdada, um aplicativo tinha que ser o único locatário a ser excluído. O botão de exclusão foi desabilitado para aplicativos multilocatário. Na nova experiência, os aplicativos podem ser excluídos em qualquer estado, mas você deve confirmar a ação. Para obter mais informações sobre como excluir registros do aplicativo, consulte [este guia de início rápido](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifesto do aplicativo

As experiências herdadas e novas usam versões diferentes para o formato do JSON no editor de manifesto. Para obter mais informações, veja [Manifesto do aplicativo](reference-app-manifest.md).

## <a name="new-ui"></a>Nova interface do usuário

Há uma nova interface do usuário para propriedades que anteriormente só pode ser definida usando o editor de manifesto ou a API ou não existe.

- **Fluxo de concessão implícita** (oauth2AllowImplicitFlow) podem ser encontrados na **autenticação** página. Ao contrário a experiência herdada, você pode habilitar **tokens de acesso** ou **tokens de id**, ou ambos.
- **Escopos definidos por essa API** (oauth2Permissions) e **autorizado a aplicativos cliente** (preAuthorizedApplications) pode ser configurado por meio de **expor uma API** página. Para obter mais informações sobre como configurar um aplicativo para ser uma API da web e expor os escopos/permissões, consulte [este guia de início rápido](quickstart-configure-app-expose-web-apis.md).
- **Domínio do publicador** (que é exibido aos usuários na [solicitação de consentimento do aplicativo](application-consent-experience.md)) pode ser encontrada no **folha de identidade visual** página. Para obter mais informações sobre como configurar um domínio de publicador, consulte [estas instruções](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Limitações

A nova experiência tem as seguintes limitações:

- A nova experiência no momento não está disponível nos locatários do Azure AD B2C.
- O formato de segredos (senhas de aplicativo) do cliente é diferente da experiência herdada e quebras de CLI.
- Não há suporte para alterar o valor para contas com suporte na interface do usuário. Você precisará usar o manifesto do aplicativo, a menos que você alterna entre o Azure AD locatário único e multilocatário.
