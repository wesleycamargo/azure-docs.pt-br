---
title: Obter dados usando a API de Relatório do Azure AD com certificados | Microsoft Docs
description: Explica como usar a API de Relatório do Azure AD com credenciais de certificado para obter dados de diretórios sem intervenção do usuário.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 44ec19721ba59898915f6547231fb586cb44eb30
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389745"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Obter dados usando a API de Relatório do Azure AD com certificados

As [APIs de relatório do Azure AD](active-directory-reporting-api-getting-started-azure-portal.md) fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação. Caso deseje acessar a API de Relatório do Azure AD sem a intervenção do usuário, configure o acesso para usar certificados.

Isso envolve as seguintes etapas:

1. [Instalar os pré-requisitos](#install-prerequisites)
2. [Registrar o certificado no aplicativo](#register-the-certificate-in-your-app)
3. [Obter um token de acesso para a API do MS Graph](#get-an-access-token-for-ms-graph-api)
4. [Consultar os pontos de extremidade da API do MS Graph](#query-the-ms-graph-api-endpoints)


## <a name="install-prerequisites"></a>Instalar pré-requisitos

1. Primeiro, conclua os [pré-requisitos para acessar a API de relatório do Azure Active Directory](active-directory-reporting-api-prerequisites-azure-portal.md). 

2. Baixe e instale o Azure AD PowerShell V2, seguindo as instruções descritas em [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md)

3. Instale o MSCloudIDUtils por meio da [PowerShellGallery – MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Esse módulo fornece vários cmdlets do utilitário, incluindo:
    - As bibliotecas ADAL necessárias para autenticação
    - Tokens de acesso do usuário, chaves de aplicativo e certificados usando ADAL
    - Resultados paginados de manipulação da API do Graph

4. Se esse for seu primeiro uso do módulo, execute **Install-MSCloudIdUtilsModule** para concluir a instalação; caso contrário, importe-o usando o comando **Import-Module** do PowerShell.

A sessão deverá se parecer com esta tela:

  ![Windows PowerShell](./media/active-directory-reporting-api-with-certificates/module-install.png)

## <a name="register-the-certificate-in-your-app"></a>Registrar o certificado no aplicativo

1. Primeiro, vá para a página de registro do aplicativo. Faça isso navegando para o [portal do Azure](https://portal.azure.com), clicando em **Azure Active Directory** e, em seguida, clicando em **Registros do aplicativo** e escolhendo o aplicativo na lista. 

2. Depois, siga as etapas para [registrar seu certificado no Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-certificate-credentials#register-your-certificate-with-azure-ad) para o aplicativo. 

3. Anote a ID do Aplicativo e a impressão digital do certificado que você acabou de registrar no aplicativo. Para encontrar a impressão digital, na página do aplicativo no portal, acesse **Configurações** e clique em **Chaves**. A impressão digital estará na lista **Chaves Públicas**.

  
## <a name="get-an-access-token-for-ms-graph-api"></a>Obter um token de acesso para a API do MS Graph

Para obter um token de acesso para a API do MS Graph, use o cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** do módulo MSCloudIdUtils do PowerShell. 

>[!NOTE]
>É necessário usar a ID do Aplicativo (também conhecida como ClientID) e a impressão digital do certificado com a chave privada instalada no repositório de certificados do computador (repositório de certificados CurrentUser ou LocalMachine).
>

 ![Portal do Azure](./media/active-directory-reporting-api-with-certificates/getaccesstoken.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>Usar o token de acesso para chamar a API do Graph

Agora, você pode usar o token de acesso no script do PowerShell para consultar a API do Graph. Veja abaixo um exemplo que usa o cmdlet **Invoke-MSCloudIdMSGraphQuery** do MSCloudIDUtils para enumerar o ponto de extremidade signins ou directoryAudits. Esse cmdlet lida com várias páginas de resultados e os envia para o pipeline do PowerShell.

### <a name="query-the-directoryaudits-endpoint"></a>Consultar o ponto de extremidade DirectoryAudits
 ![Portal do Azure](./media/active-directory-reporting-api-with-certificates/query-directoryAudits.png)

 ### <a name="query-the-signins-endpoint"></a>Consultar o ponto de extremidade SignIns
 ![Portal do Azure](./media/active-directory-reporting-api-with-certificates/query-signins.png)

Agora, você pode optar por exportar esses dados para um CSV e salvá-lo em um sistema SIEM. Você pode também encapsular o script em uma tarefa agendada para obter dados do Azure AD do seu locatário periodicamente sem a necessidade de armazenar as chaves de aplicativo no código-fonte. 


## <a name="next-steps"></a>Próximas etapas

* [Tenha uma primeira impressão das APIs de relatórios](active-directory-reporting-api-getting-started-azure-portal.md)
* [Referência da API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Referência da API de relatório de atividade de entrada](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



