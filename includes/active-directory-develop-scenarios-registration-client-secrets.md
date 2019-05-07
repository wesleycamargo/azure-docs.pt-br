---
title: Arquivo de inclusão
description: Incluir arquivo para o cenário de cliente confidencial (daemon, aplicativo Web, API da Web) de páginas de aterrissagem
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 9ee7422b372993d60c629524eb036b9678e5776c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074644"
---
## <a name="registration-of-secrets-or-certificates"></a>Registro de segredos ou certificados

Como qualquer aplicativo cliente confidencial, você precisa registrar um segredo ou certificado. Você pode registrar seus segredos de aplicativo por meio da experiência interativa na [portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview), ou usando ferramentas de linha de comando (como o PowerShell)

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>Registrando os segredos de cliente usando o portal de registro de aplicativo

O gerenciamento de credenciais do cliente ocorre na **certificados e segredos** página para um aplicativo:

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- o segredo do aplicativo (segredo do cliente nomeado também) é gerado pelo AD do Azure, durante o registro do aplicativo cliente confidencial. Essa geração ocorre quando você seleciona **novo segredo do cliente**. Nesse ponto, você deve copiar a cadeia de caracteres secreta na área de transferência para uso em seu aplicativo, antes de selecionar **salvar**. Essa cadeia de caracteres não será mais apresentada.
- o certificado é carregado no registro de aplicativo usando o **carregar um certificado** botão

Para obter detalhes, consulte [guia de início rápido: Configurar um aplicativo cliente para acessar APIs da web | Adicionar credenciais ao seu aplicativo](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)

### <a name="registering-client-secrets-using-powershell"></a>Registrando os segredos de cliente usando o PowerShell

Como alternativa, você pode registrar seu aplicativo com o Azure AD usando as ferramentas de linha de comando. O [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) exemplo mostra como registrar um segredo do aplicativo ou um certificado com um aplicativo do Azure AD:

- Para obter detalhes sobre como registrar um segredo do aplicativo, consulte [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- Para obter detalhes sobre como registrar um certificado com o aplicativo, consulte [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)