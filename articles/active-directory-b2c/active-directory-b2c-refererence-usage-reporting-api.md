---
title: "Definições e exemplos de API de Relatórios de Uso do Azure AD B2C | Microsoft Docs"
description: "Guia e exemplo sobre como obter relatórios sobre usuários, autenticações e MFA B2C locatário."
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 4b2cbf5f62ee63bada42b2a06506b793d4349fdb
ms.openlocfilehash: 7db9a45a4c80ea8d01937837dfa7a15c171fb66b
ms.contentlocale: pt-br
ms.lasthandoff: 02/10/2017


---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Acessando os relatórios de uso do Azure AD B2C por meio da API de geração de relatórios

O Azure Active Directory B2C fornece logon e autenticação baseada em MFA para todos os usuários finais de sua família de aplicativos em provedores de identidade.  Perguntas de respostas saber o número de usuários registrados no locatário, os provedores que eles usaram para registrar e o número de autenticações por tipo, como:
* Quantos usuários de cada tipo de provedor de identidade (por exemplo, Microsoft Account, LinkedIn) foram registradas nos últimos 10 dias?
* Quantas Autenticações Multifator foram concluídas com êxito no último mês?
* Quantas autenticações baseadas em logon foram concluídas neste mês? Por dia? Por aplicativo?
* Como é possível aproximar o custo mensal esperado da minha atividade B2C locatário?

Este artigo se concentra em relatórios mais intimamente ligados a atividades de cobrança, que é baseada no número de usuários, número de autenticações faturáveis baseado em logon e número de autenticações de vários fatores.


## <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Pré-requisitos para acessar a API de relatório do Azure AD
Antes de começar, você precisa concluir os [Pré-requisitos para acessar o APIs de relatório do Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).  Criar um aplicativo, obter um segredo para ele e conceder acesso direitos para relatórios do locatário B2C do AD do Azure. Também são fornecidos aqui os exemplos de *script Bash* e de *script Python*.

## <a name="powershell-script"></a>Script do PowerShell
Este script demonstra os relatórios de uso de quatro usando o parâmetro **TimeStamp** e o filtro **-ApplicationId**.

```
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.windows.net"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"  
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
       $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>Definições de relatório de uso
**tenantUserCount** – a contagem do número de usuários no locatário por tipo de provedor de identidade por dia nos últimos 30 dias. (opcionalmente, um filtro de carimbo de data/hora fornece contas de usuário de uma data especificada para a data atual). Relatório fornece:
 * TotalUserCount = contagem de todos os objetos de usuário
 * OtherUserCount = n º de usuários do diretório do AAD (B2C não usuários)
 * LocalUserCount = # B2C de contas de usuário criadas com as credenciais locais ao locatário B2C

**AlternateIdUserCount** = n º de usuários B2C registrado com provedores de identidade externas (por exemplo, facebook, Account da Microsoft, outros locatários AAD - também conhecido como OrgId)

**b2cAuthenticationCountSummary** – soma a contagem diária de autenticações faturáveis nos últimos 30 dias por dia e por tipo de fluxo de autenticação

**b2cAuthenticationCount** -contar o número de autenticações em um período de tempo. Padrão é últimos 30 dias.  (opcional: início e fim de carimbo de data/hora (s) definem um período específico de contagens desejado) Saída inclui um StartTimeStamp (data mais antiga da atividade para este Locatário) e EndTimeStamp (última atualização)

**b2cMfaRequestCountSummary** -soma a contagem diária de Autenticações Multifator por dia e por tipo de MFA (SMS ou voz)


## <a name="limitations"></a>Limitações
* Dados de contagem de usuário são atualizados a cada 24 a 48 horas.  As autenticações são atualizadas várias vezes ao dia.
* Ao usar o filtro ApplicationId, uma resposta de relatório em branco pode ser devido a uma das seguintes condições:
 * A Id do aplicativo não existe no locatário. Verifique se que ele está correto.
 * A Id do aplicativo existe, mas nenhum dado foi encontrado no período do relatório. Verifique os parâmetros de tempo de data.


## <a name="next-steps"></a>Próximas etapas
### <a name="estimating-your-azure-ad-monthly-bill"></a>Estimando sua fatura mensal do AD do Azure.
Quando combinado com [a mais atual do Azure AD B2C preços disponíveis](https://azure.microsoft.com/pricing/details/active-directory-b2c/), você pode estimar diários, semana e mensal de consumo do Azure.  Uma estimativa é especialmente útil ao planejar alterações no comportamento de locatário que pode causar impacto no custo geral.  Os custos reais podem ser analisados em seu [vinculado a assinatura do Azure.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing)

### <a name="options-for-other-output-formats"></a>Opções para outros formatos de saída
```
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath b2cUserJourneySummaryEvents.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    

