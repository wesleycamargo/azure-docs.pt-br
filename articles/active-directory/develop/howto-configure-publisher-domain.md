---
title: Configurar domínio de Editor do aplicativo | Azure
description: Saiba como configurar o domínio de Editor do aplicativo para que os usuários saibam onde suas informações estão sendo enviadas.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: efbf448770bfcf797d6bf01cd3c28dc98023adff
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502272"
---
# <a name="how-to-configure-an-applications-publisher-domain-preview"></a>Como: Configurar o domínio de Editor do aplicativo (visualização)

Domínio do Editor do aplicativo é exibido aos usuários sobre as [solicitação de consentimento do aplicativo](application-consent-experience.md) para que os usuários saibam onde suas informações estão sendo enviadas. Aplicativos multilocatários registrados após 21 de maio de 2019 que não têm um domínio de editor aparecem como **não verificado**. Os aplicativos de multilocatário são aplicativos que dão suporte a contas de fora um único diretório organizacional; Por exemplo, dá suporte a todas as contas do Azure AD ou dar suporte a todas as contas do Azure AD e contas pessoais da Microsoft.

## <a name="new-applications"></a>Novos aplicativos

Quando você registra um novo aplicativo, o domínio do Editor de seu aplicativo pode ser definido como um valor padrão. O valor depende de onde o aplicativo é registrado, especialmente se o aplicativo é registrado em um locatário e se o locatário tem locatário domínios verificados.

Se houver domínios verificados de locatário, domínio de Editor do aplicativo padrão será o domínio primário verificado do locatário. Se há nenhum locatário verificado domínios (que é o caso quando o aplicativo não está registrado em um locatário), o domínio do aplicativo de publicador será definido como null.

A tabela a seguir resume o comportamento padrão do valor de domínio do publicador.  

| Domínios verificados de locatário | Valor padrão do domínio de publicador |
|-------------------------|----------------------------|
| nulo | nulo |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>-domain2.com (primário) | domain2.com |

Se o domínio de publicador de um aplicativo de multilocatário não está definido ou se ele for definido como um domínio que termina em. onmicrosoft.com, a solicitação de consentimento do aplicativo mostrará **não verificado** no lugar de domínio do Editor.

## <a name="grandfathered-applications"></a>Herdado de aplicativos

Se seu aplicativo foi registrado antes de 21 de maio de 2019, solicitação de consentimento do seu aplicativo não mostrarão **não verificado** se você não tiver definido um domínio de publicador. É recomendável que você defina o publicador valor de domínio para que os usuários podem ver essas informações na solicitação de consentimento do seu aplicativo.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Configurar o domínio de editor usando o portal do Azure

Para definir o domínio de publicador do seu aplicativo, siga estas etapas.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.

1. Se sua conta estiver presente em mais de um locatário do AD do Azure:
   1. Selecione seu perfil no menu no canto superior direito da página e, em seguida **mudar diretório**.
   1. Altere sua sessão para o locatário do Azure AD onde você deseja criar seu aplicativo.

1. Navegue até [Azure Active Directory > registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) para localizar e selecionar o aplicativo que você deseja configurar.

   Depois de selecionar o aplicativo, você verá o aplicativo **visão geral** página.

1. Do aplicativo do **visão geral** página, selecione o **identidade visual** seção.

1. Localizar o **domínio de publicador** do campo e selecione uma das seguintes opções:

   - Selecione **configurar um domínio** se você já não tiver configurado um domínio.
   - Selecione **domínio de atualização** se um domínio já foi configurado.

Se seu aplicativo for registrado em um locatário, você verá duas guias para selecionar de: **Selecione um domínio verificado** e **verificar um novo domínio**.

Se seu aplicativo não estiver registrado em um locatário, você verá apenas a opção de verificar um novo domínio para seu aplicativo.

### <a name="to-verify-a-new-domain-for-your-app"></a>Para verificar se um novo domínio do aplicativo

1. Crie um arquivo chamado `microsoft-identity-association.json` e cole o trecho de código JSON a seguir.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Substitua o espaço reservado *{YOUR-APP-ID-HERE}* com a ID do aplicativo (cliente) que corresponde ao seu aplicativo.

1. Hospedar o arquivo em: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`. Substitua o espaço reservado *{YOUR-DOMAIN-HERE}* para coincidir com o domínio verificado.

1. Clique o **verificar e salvar o domínio** botão.

### <a name="to-select-a-verified-domain"></a>Para selecionar um domínio verificado

- Se seu locatário tem domínios verificados, selecione um dos domínios do **selecione um domínio verificado** lista suspensa.

## <a name="implications-on-the-app-consent-prompt"></a>Solicitação de consentimento implicações no aplicativo

Configurando o domínio de publicador tem um impacto no que os usuários veem no prompt de consentimento do aplicativo. Para entender completamente os componentes do prompt de consentimento, consulte [Noções básicas sobre o consentimento de aplicativo passa por](application-consent-experience.md).

A tabela a seguir descreve o comportamento para aplicativos criados antes de 21 de maio de 2019.

![Solicitação de consentimento para aplicativos criados antes de 21 de maio de 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

O comportamento para novos aplicativos criadas após 21 de maio de 2019 dependerá do domínio de publicador e o tipo de aplicativo. A tabela a seguir descreve as alterações que você deve esperar ver com diferentes combinações de configurações.

![Solicitação de consentimento para aplicativos criados após 21 de maio de 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Implicações de URIs de redirecionamento

Aplicativos que os usuários com contas pessoais da Microsoft ou conta de escola ou trabalho entrar ([multilocatário](single-and-multi-tenant-apps.md)) estão sujeitos a algumas restrições ao especificar URIs de redirecionamento.

### <a name="single-root-domain-restriction"></a>Restrição de domínio de raiz única

Quando o valor de domínio do publicador para aplicativos de multilocatário é definido como nulos, aplicativos são restritos a compartilhar um único domínio raiz para URIs de redirecionamento. Por exemplo, a seguinte combinação de valores não é permitida porque o domínio raiz, contoso.com, não corresponde ao fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Restrições de subdomínio

Subdomínios são permitidos, mas você deve registrar explicitamente o domínio raiz. Por exemplo, embora os URIs a seguir compartilhem um único domínio raiz, a combinação não é permitida.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

No entanto, se o desenvolvedor adiciona explicitamente o domínio raiz, a combinação será permitida.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Exceções

Os casos a seguir não estão sujeitos à restrição de domínio de raiz única:

- Aplicativos de locatário único, ou aplicativos que contas em um único diretório de destino
- Uso de localhost como URIs de redirecionamento
- URIs de redirecionamento com esquemas personalizados (não-HTTP ou HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Configurar o domínio do Editor de forma programática

Atualmente, não há nenhum suporte de API REST ou PowerShell para configurar o domínio de publicador por meio de programação.
