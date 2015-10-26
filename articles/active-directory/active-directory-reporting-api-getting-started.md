<properties
   pageTitle="Introdução à API de relatório do AD do Azure"
   description="Como começar a usar a API de relatório do Active Directory do Azure"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/17/2015"
   ms.author="kenhoff;yossib"/>


# Introdução à API de relatório do AD do Azure

O Active Directory do Azure fornece uma variedade de relatórios de atividade, segurança e auditoria. Esses dados podem ser consumidos por meio do portal do Azure, mas também podem ser muito úteis em vários outros aplicativos, como sistemas SIEM, ferramentas de auditoria e de business intelligence.

As APIs de relatórios do AD do Azure fornecem acesso programático a esses dados por meio de um conjunto de APIs baseadas em REST que podem ser chamadas de várias ferramentas e linguagens de programação.

Este artigo o guiará pelo processo de chamada das APIs de relatório do AD do Azure usando o PowerShell. Você pode modificar o exemplo de script do PowerShell para acessar dados de qualquer um dos relatórios disponíveis no formato JSON, XML ou texto, conforme o seu cenário exigir.

Para usar esse exemplo, você precisará de um [Active Directory do Azure](active-directory-whatis.md)

## Criando um aplicativo do AD do Azure para acessar a API

A API de relatório usa [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar o acesso às APIs da Web. Para acessar informações do diretório, crie um aplicativo no Active Directory e conceda as permissões apropriadas para acessar os dados do AAD.


### Criar um aplicativo
- Navegue até o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/).
- Navegue até seu diretório.
- Navegue até os aplicativos.
- Na barra de ferramentas inferior, clique em "Adicionar".
	- Clique em “Adicionar um aplicativo que minha organização está desenvolvendo”.
	- **Nome**: qualquer nome serve. Algo como "Aplicativo de API de relatório" é recomendado.
	- **Tipo**: selecione Aplicativo Web e/ou API Web.
	- Clique na seta para ir para a próxima página.
	- **URL de logon**: ```http://localhost```.
	- **URI da ID do Aplicativo**: ```http://localhost```.
	- Clique na marca de seleção para concluir a adição do aplicativo.

### Conceda permissão ao aplicativo para usar a API
- Navegue até a guia Aplicativos.
- Navegue até o aplicativo recém-criado.
- Clique na guia **Configurar**.
- Na seção “Permissões para outros aplicativos”:
	- Em Active Directory do Microsoft Azure > Permissões de aplicativo, selecione **Ler dados do diretório**.
- Clique em **Salvar** na barra inferior.


### Obter sua ID de diretório, ID do cliente e segredo do cliente

As etapas a seguir guiarão você para obter a ID do cliente do aplicativo e o segredo do cliente. Você também precisará saber o nome do locatário, que pode ser seu *.onmicrosoft.com ou um nome de domínio personalizado. Copie-os em um local separado; você vai usá-los para modificar o script.

#### ID do cliente do aplicativo
- Navegue até a guia Aplicativos.
- Navegue até o aplicativo recém-criado.
- Navegue até a guia **Configurar**.
- Sua ID do cliente do aplicativo está listada no campo **ID do cliente**.

#### Segredo do cliente do aplicativo
- Navegue até a guia Aplicativos.
- Navegue até o aplicativo recém-criado.
- Navegue até a guia Configurar.
- Gere uma nova chave secreta para o seu aplicativo selecionando uma duração na seção "Chaves".
- A chave será exibida ao salvar. Certifique-se de copiá-la e colá-la em um local seguro, porque não há nenhuma maneira de recuperá-la posteriormente.


## Modificar o script
Edite um dos scripts abaixo para trabalhar com seu diretório, substituindo $ClientID, $ClientSecret e $tenantdomain pelos valores corretos de "Delegar Acesso no AD do Azure".

### Script do PowerShell

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID      = "<<YOUR CLIENT ID HERE>>"                # Should be a ~35 character string insert your info here
    $ClientSecret  = "<<YOUR CLIENT SECRET HERE>>"          # Should be a ~44 character string insert your info here
    $loginURL      = "https://login.windows.net"
    $tenantdomain  = "<<YOUR TENANT NAME HERE>>"            # For example, contoso.onmicrosoft.com

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
        $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        # Returns a list of all the available reports
        Write-host List of available reports
        Write-host =========================
        $allReports = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports?api-version=beta")
        Write-host $allReports.Content

        Write-host
        Write-host Data from the AccountProvisioningEvents report
        Write-host ====================================================
        Write-host
        # Returns a JSON document for the "accountProvisioningEvents" report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/accountProvisioningEvents?api-version=beta")
        Write-host $myReport.Content

        Write-host
        Write-host Data from the AuditEvents report with datetime filter
        Write-host ====================================================
        Write-host
        # Returns a JSON document for the "auditEvents" report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&$filter=eventTime gt 2015-05-20")
        Write-host $myReport.Content

        # Options for other output formats

        # to output the JSON use following line
        $myReport.Content | Out-File -FilePath accountProvisioningEvents.json -Force

        # to output the content to a name value list
        ($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath accountProvisioningEvents.txt -Force

        # to output the content in XML use the following line
        (($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath accountProvisioningEvents.xml -Force

    } else {
        Write-Host "ERROR: No Access Token"
        }

### Script Bash

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="<<YOUR CLIENT ID HERE>>"			# Should be a ~35 character string insert your info here
    CLIENT_SECRET="<<YOUR CLIENT SECRET HERE>>"	# Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="<<YOUR TENANT NAME HERE>>"	 # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | jq -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | jq -r '.access_token')

    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta)

    echo $REPORT | jq -r '.value' | jq -r ".[]"




## Execute o script
Quando você terminar de editar o script, execute-o e verifique se os dados esperados do relatório AuditEvents são retornados.

O script retorna listas de todos os relatórios disponíveis e retorna a saída do relatório AccountProvisioningEvents na janela do PowerShell, no formato JSON. Ele também cria arquivos com a mesma saída em XML, JSON e texto. Você pode comentar experimentos de modificação do script para retornar dados de outros relatórios, além de comentar os formatos de saída de que você não precisa.

## Observações

- Não há qualquer limite para o número de eventos retornados pela API de Relatórios do Azure AD (usando a paginação de OData).
	- Para conhecer os limites de retenção em dados de relatório, confira [Políticas de retenção de relatório](active-directory-reporting-retention.md).


## Próximas etapas
- Curioso sobre que relatórios de segurança, auditoria e atividade estão disponíveis? Verifique [Relatórios de segurança, auditoria e atividade do AD do Azure](active-directory-view-access-usage-reports.md)
- Consulte [Eventos de relatório de auditoria do AD do Azure](active-directory-reporting-audit-events.md) para obter mais detalhes sobre o relatório de auditoria
- Consulte [Eventos e relatórios de auditoria do AD do Azure (visualização)](https://msdn.microsoft.com/library/azure/mt126081.aspx) para obter mais detalhes sobre o serviço REST de Graph API

<!---HONumber=Oct15_HO3-->