---
title: Exemplos de API de relatório de atividade de entrada do Azure Active Directory | Microsoft Docs
description: Como começar a usar a API de relatório do Active Directory do Azure
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/31/2018
ms.author: dhanyahk;rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 466755d7d1cc7fbf4006826ac849b74ba306bae9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698570"
---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Exemplos de API de relatório de atividade de entrada do Azure Active Directory
Este artigo faz parte de uma coleção de artigos sobre a API de relatório do Azure Active Directory.  
Os relatórios do Azure AD fornecem uma API que permite a você acessar dados de atividade de entrada usando código ou ferramentas relacionadas.  
O escopo deste artigo é fornecer código de exemplo para a **API de atividade de entrada**.

Consulte:

* [Logs de auditoria](active-directory-reporting-azure-portal.md#activity-reports) para obter mais informações conceituais
* [Introdução à API de relatório do Azure Active Directory](active-directory-reporting-api-getting-started.md) para saber mais sobre a API de relatório.


## <a name="prerequisites"></a>pré-requisitos
Antes de usar os exemplos deste artigo, você precisará atender aos [pré-requisitos para acessar a API de relatório do Azure AD](active-directory-reporting-api-prerequisites.md).  

## <a name="powershell-script"></a>Script do PowerShell

```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfSignInDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/signIns"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into SignIns$i.json"
        if($docCount -le $countOfSignInDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath SignIns$i.json -append  -Force       
        }
        else
        {           
            $docCount=0
            $i = $i+1
        }
            
        #Get url from next link
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'         
    }while($url -ne $null)
    Write-Output "Total Fetched record count is : $totalFetchedRecordCount"
                
} else {
    Write-Host "ERROR: No Access Token"
}

```




## <a name="executing-the-script"></a>Executando o script
Quando terminar de editar o script, execute-o e verifique se os dados esperados do relatório de logs de entrada são retornados.

O script retorna a saída do relatório de entradas no formato JSON. Ele também cria um arquivo `SignIns.json` com a mesma saída. Você pode experimentar ao modificar o script para retornar dados de outros relatórios, além de comentar os formatos de saída de que você não precisa.

## <a name="next-steps"></a>Próximas etapas
* Gostaria de personalizar os exemplos deste artigo? Confira a [referência da API de atividade de entrada do Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). 
* Se você quiser uma visão geral de como usar a API de relatório do Azure Active Directory, confira [Introdução à API de relatório do Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Se você quiser saber mais sobre os relatórios do Azure Active Directory, confira o [Guia de relatórios do Azure Active Directory](active-directory-reporting-guide.md).  

