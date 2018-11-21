---
title: 'Tutorial: Obter dados usando a API de Relatório do Azure AD com certificados | Microsoft Docs'
description: Este tutorial explica como usar a API de Relatório do Azure AD com credenciais de certificado para obter dados de diretórios sem intervenção do usuário.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0ee756828a50cdf62471923614afbe88e238b9ef
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624550"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Tutorial: Obter dados usando a API de Relatório do Azure Active Directory com certificados

As [APIs de relatório do Azure AD](concept-reporting-api.md) fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação. Se você quiser acessar a API de Relatório do Azure AD sem intervenção do usuário, deverá configurar o acesso para usar certificados.

Neste tutorial, você aprende como usar um certificado de teste para acessar a API do Graph para relatórios. Não recomendamos o uso de certificados de teste em um ambiente de produção. 

## <a name="prerequisites"></a>Pré-requisitos

1. Primeiro, preencha os [pré-requisitos para acessar a API de Relatório do Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

2. Baixe e instale o [PowerShell V2 do Azure AD](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

3. Instale o [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Esse módulo fornece vários cmdlets do utilitário, incluindo:
    - As bibliotecas ADAL necessárias para autenticação
    - Tokens de acesso do usuário, chaves de aplicativo e certificados usando ADAL
    - Resultados paginados de manipulação da API do Graph

4. Se é a primeira vez que você está usando o módulo, execute **Install-MSCloudIdUtilsModule**, caso contrário, importe-o usando o comando do Powershell **Import-Module**. A sessão deverá se parecer com esta tela:

        ![Windows Powershell](./media/tutorial-access-api-with-certificates/module-install.png)
  
5. Use o commandlet do Powershell **New-SelfSignedCertificate** para criar um certificado de teste.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

6. Use o commandlet **Export-Certificate** para exportá-lo a um arquivo de certificado.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Obter dados usando a API de Relatório do Azure AD com certificados

1. Navegue até o [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory** e, em seguida, selecione **Registros de aplicativo** e escolha o aplicativo na lista. 

2. Selecione **Configurações** > **Chaves** e selecione **Carregar chave pública**.

3. Selecione o arquivo de certificado da etapa anterior e selecione **Salvar**. 

4. Anote a ID do Aplicativo e a impressão digital do certificado que você acabou de registrar no aplicativo. Para encontrar a impressão digital, na página do aplicativo no portal, acesse **Configurações** e clique em **Chaves**. A impressão digital estará na lista **Chaves Públicas**.

5. Abra o manifesto do aplicativo no editor de manifesto sequencial e substitua a propriedade *keyCredentials* pelas novas informações de certificado usando o esquema a seguir. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ```

6. Salve o manifesto. 
  
7. Agora, você pode obter um token de acesso para a API Graph usando este certificado. Use o cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** do módulo MSCloudIdUtils PowerShell, passando a ID do aplicativo e a impressão digital obtida na etapa anterior. 

 ![Portal do Azure](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

8. Use o token de acesso em seu script Powershell para consultar a API do Graph. Use o cmdlet **Invoke-MSCloudIdMSGraphQuery** do MSCloudIDUtils para enumerar o ponto de extremidade de directoryAudits e signins. Esse cmdlet manipula resultados com várias páginas e envia esses resultados para o pipeline do PowerShell.

9. Consulte o ponto de extremidade directoryAudits para recuperar os logs de auditoria. 
 ![Portal do Azure](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

10. Consulte o ponto de extremidade signins para recuperar os logs de entrada.
 ![Portal do Azure](./media/tutorial-access-api-with-certificates/query-signins.png)

11. Agora, você pode optar por exportar esses dados para um CSV e salvá-lo em um sistema SIEM. Você pode também encapsular o script em uma tarefa agendada para obter dados do Azure AD do seu locatário periodicamente sem a necessidade de armazenar as chaves de aplicativo no código-fonte. 

## <a name="next-steps"></a>Próximas etapas

* [Tenha uma primeira impressão das APIs de relatórios](concept-reporting-api.md)
* [Referência da API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Referência da API de relatório de atividade de entrada](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
