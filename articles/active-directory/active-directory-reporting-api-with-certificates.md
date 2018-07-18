---
title: Obter dados usando a API de Relatório do Azure AD com certificados | Microsoft Docs
description: Explica como usar a API de Relatório do Azure AD com credenciais de certificado para obter dados de diretórios sem intervenção do usuário.
services: active-directory
documentationcenter: ''
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/07/2018
ms.author: ramical
ms.openlocfilehash: 54e661284c539b835089e858ba7b5e0016e89a83
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Obter dados usando a API de Relatório do Azure AD com certificados

As [APIs de relatório do Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação.

Se você quiser acessar a API de relatório do Azure AD sem a intervenção do usuário, você pode configurar o acesso para usar certificados

Este artigo:

- Fornece as etapas necessárias para acessar o Azure AD API de relatório usando certificados.
- Presume que você tenha concluído os [pré-requisitos para acessar o Azure AD API de relatório](active-directory-reporting-api-prerequisites-azure-portal.md). 


Para acessar a API de relatório com certificados, você precisa:

1. Instalar os pré-requisitos
2. Definir o certificado em seu aplicativo 
3. Conceder permissões
4. Obter um token de acesso




Para saber mais sobre o código-fonte, confira [Aproveitar o módulo da API de Relatório](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils). 

## <a name="install-prerequisites"></a>Instalar pré-requisitos

Você precisa ter o Azure AD PowerShell V2 e o módulo AzureADUtils instalados.

1. Baixe e instale o Azure AD Powershell V2, seguindo as instruções em [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md).

2. Baixe o módulo Azure AD Utils em [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1). 
  Esse módulo fornece vários cmdlets do utilitário, incluindo:
    - A versão mais recente do ADAL usando o Nuget
    - Tokens de acesso do usuário, chaves de aplicativo e certificados usando ADAL
    - Resultados paginados de manipulação da API do Graph

**Para instalar o módulo Azure AD Utils:**

1. Crie um diretório para salvar o módulo de utilitários (por exemplo, c:\azureAD) e baixe o módulo do GitHub.
2. Abra uma sessão do PowerShell e vá para o diretório que você acabou de criar. 
3. Importe o módulo e instale-o no caminho do módulo do PowerShell usando o cmdlet Install-AzureADUtilsModule. 

A sessão deve fica mais ou menos como esta tela:

  ![Windows PowerShell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

## <a name="set-the-certificate-in-your-app"></a>Definir o certificado em seu aplicativo

**Para definir o certificado em seu aplicativo:**

1. [Obter a ID de Objeto](active-directory-reporting-api-prerequisites-azure-portal.md#get-your-applications-client-id) do seu aplicativo do Portal do Azure. 

  ![Portal do Azure](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. Abra uma sessão do PowerShell e conecte-se ao Azure AD usando o cmdlet Connect-AzureAD.

  ![Portal do Azure](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. Use o cmdlet New-AzureADApplicationCertificateCredential de AzureADUtils para adicionar uma credencial de certificado a ele. 

>[!Note]
>Você precisa fornecer a ID de objeto capturada anteriormente, bem como o objeto de certificado do aplicativo (obter isso usando a unidade Cert:).
>


  ![Portal do Azure](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
## <a name="get-an-access-token"></a>Obter um token de acesso

Para obter um token de acesso, use o cmdlet **Get-AzureADGraphAPIAccessTokenFromCert** de AzureADUtils. 

>[!NOTE]
>Você precisa usar a ID do aplicativo em vez da ID de objeto que você usou na última seção.
>

 ![Portal do Azure](./media/active-directory-report-api-with-certificates/application-id.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>Usar o token de acesso para chamar a API do Graph

Agora, você pode criar o script. Abaixo vemos um exemplo usando o cmdlet **Invoke-AzureADGraphAPIQuery** do AzureADUtils. Esse cmdlet lida com várias páginas de resultados e os envia para o pipeline do PowerShell. 

 ![Portal do Azure](./media/active-directory-report-api-with-certificates/script-completed.png)

Agora você está pronto para exportar para um CSV e salvar em um sistema SIEM. Você pode também encapsular o script em uma tarefa agendada para obter dados do Azure AD do seu locatário periodicamente sem a necessidade de armazenar as chaves de aplicativo no código-fonte. 

## <a name="next-steps"></a>Próximas etapas

- [Tenha uma primeira impressão das APIs de relatórios](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Crie sua própria solução](active-directory-reporting-api-getting-started-azure-portal.md#customize)




