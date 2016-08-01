<properties
   pageTitle="Introdução à API de Relatório do Azure AD | Microsoft Azure"
   description="Como começar a usar a API de relatório do Active Directory do Azure"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/18/2016"
   ms.author="dhanyahk"/>

# Introdução à API de Relatório do Azure Active Directory

*Esta documentação é parte do [Guia de Relatórios do Active Directory do Azure](active-directory-reporting-guide.md).*

O AD (Azure Active Directory) fornece diversos relatórios de atividade, segurança e auditoria. Esses dados podem ser consumidos por meio do portal clássico do Azure, mas também podem ser muito úteis em vários outros aplicativos, como sistemas SIEM, ferramentas de auditoria e de business intelligence.

As [APIs de relatórios do Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) fornecem acesso programático a esses dados por meio de um conjunto de APIs baseadas em REST que podem ser chamadas de várias ferramentas e linguagens de programação.

Este artigo o guiará pelo processo de chamada das APIs de relatório do AD do Azure usando o PowerShell. Você pode modificar o exemplo de script do PowerShell para acessar dados de qualquer um dos relatórios disponíveis no formato JSON, XML ou texto, conforme o seu cenário exigir.

Para usar esse exemplo, você precisará de um locatário do [Azure Active Directory](active-directory-whatis.md).

## Criando um aplicativo do AD do Azure para acessar a API

A API de relatório usa [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar o acesso às APIs da Web. Para acessar informações do seu diretório, será necessário criar um aplicativo no locatário do Azure AD e conceder permissões apropriadas para acessar os dados do Azure AD.


### Registrar um aplicativo Azure AD
Para concluir o trabalho de registro de aplicativo do Azure AD, você deve entrar portal clássico do Azure com uma conta de administrador de assinatura do Azure que também é um membro da função de diretório de Administrador Global no seu locatário do Azure AD. Isso ocorre porque você registrará o aplicativo do Azure AD com permissões que exigem registro/consentimento usando uma conta com privilégios de Administrador Global.

> [AZURE.IMPORTANT] Aplicativos em execução com credenciais com privilégios de "admin" como esse podem ser muito poderosos, portanto certifique-se de proteger as credenciais de ID/segredo do aplicativo.


1. Navegue até o [Portal Clássico do Azure](https://manage.windowsazure.com/).
2. Navegue em seu locatário do Azure AD, na extensão do **Active Directory** no painel esquerdo.
3. Navegue até a guia **Aplicativos**.
4. Na barra de ferramentas inferior, clique em **Adicionar**.
	- Clique em “Adicionar um aplicativo que minha organização está desenvolvendo”.
	- **Nome**: qualquer nome serve. Algo como "Aplicativo de API de relatório" é recomendado.
	- **Tipo**: selecione Aplicativo Web e/ou API Web.
	- Clique na seta para ir para a próxima página.
	- **URL de logon**: ```https://localhost```.
	- **URI da ID do Aplicativo**: ```https://localhost/ReportingApiApp```.
	- Clique na marca de seleção para concluir a adição do aplicativo.

### Conceda permissão ao aplicativo para usar a API
1. Navegue até a guia **Aplicativos**.
2. Navegue até o aplicativo recém-criado.
3. Clique na guia **Configurar**.
4. Na seção “Permissões para outros aplicativos”:
	- Para o recurso "Microsoft Azure Active Directory" (permissões para a API do Azure AD Graph), clique na lista suspensa "Permissões de Aplicativo", selecione **Ler dados do diretório**.

        > [AZURE.IMPORTANT] Especifique as permissões corretas aqui. Aplique "Permissões de Aplicativo" e não "Permissões Delegadas", caso contrário, o aplicativo não receberá o nível de permissão necessário para acessar a API de Relatório e seu script receberá um erro *"Não é possível verificar o acesso Leitura do Diretório para appId"*.


5. Clique em **Salvar** na barra inferior.

### Obter sua ID de diretório, ID do cliente e segredo do cliente

As etapas a seguir guiarão você para obter a ID do cliente do aplicativo e o segredo do cliente. Você também precisará saber o nome do locatário, que pode ser seu *.onmicrosoft.com ou um nome de domínio personalizado. Copie esses valores em um local separado; você vai usá-los para modificar o script posteriormente.

#### Obter o nome de domínio do seu locatário Azure AD
1. Navegue em seu locatário do Azure AD, na extensão do **Active Directory** no painel esquerdo.
2. Navegue até a guia **Domínios**.
4. O nome de domínio "<tenant-name>.onmicrosoft.com" do seu locatário será exibido, juntamente com nomes de domínio personalizados que foram configurados.

#### Obter a ID do cliente do aplicativo
1. Navegue até a guia **Aplicativos**.
2. Navegue até o aplicativo recém-criado.
3. Navegue até a guia **Configurar**.
4. Sua ID do cliente do aplicativo está listada no campo **ID do cliente**.

#### Obter o segredo do cliente do aplicativo
1. Navegue até a guia **Aplicativos**.
2. Navegue até o aplicativo recém-criado.
3. Navegue até a guia **Configurar**.
4. Gere uma nova chave secreta para o seu aplicativo selecionando uma duração na seção "Chaves".
5. A chave será exibida ao salvar. Certifique-se de copiá-la e colá-la em um local seguro, porque não há nenhuma maneira de recuperá-la posteriormente.

## Modificar o script
Edite um dos scripts abaixo para trabalhar com seu diretório, substituindo $ClientID, $ClientSecret e $tenantdomain pelos valores corretos da seção acima.

### Script do PowerShell
    # This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

    # Constants
    $ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
    $ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
    $loginURL       = "https://login.microsoftonline.com"     # AAD Instance; for example https://login.microsoftonline.com
    $tenantdomain   = "your-tenant-domain.onmicrosoft.com"    # AAD Tenant; for example, contoso.onmicrosoft.com
    $resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
    $7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
    Write-Output "Searching for events starting $7daysago"

    # Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    # Parse auditEvents report items, save output to file(s): auditEventsX.json, where X = 0 thru n for number of nextLink pages
    if ($oauth.access_token -ne $null) {   
        $i=0
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
        $url = 'https://graph.windows.net/' + $tenantdomain + '/reports/auditEvents?api-version=beta&`$filter=eventTime gt ' + $7daysago

        # loop through each query page (1 through n)
        Do{
            # display each event on the console window
            Write-Output "Fetching data using Uri: $url"
            $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
            foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
                Write-Output ($event | ConvertTo-Json)
            }
        
            # save the query page to an output file
            Write-Output "Save the output to a file auditEvents$i.json"
            $myReport.Content | Out-File -FilePath auditEvents$i.json -Force
            $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
            $i = $i+1
        } while($url -ne $null)
    } else {
        Write-Host "ERROR: No Access Token"
        }

    Write-Host "Press any key to continue ..."
    $x = $host.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown")

### Script Bash

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&$filter=eventTime%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"

### Python
	# Author: Michael McLaughlin (michmcla@microsoft.com)
	# Date: January 20, 2016
	# This requires the Python Requests module: http://docs.python-requests.org

	import requests
	import datetime
	import sys

	client_id = 'your-application-client-id-here'
	client_secret = 'your-application-client-secret-here'
	login_url = 'https://login.windows.net/'
	tenant_domain = 'your-directory-name-here.onmicrosoft.com'

	# Get an OAuth access token
	bodyvals = {'client_id': client_id,
	            'client_secret': client_secret,
	            'grant_type': 'client_credentials'}

	request_url = login_url + tenant_domain + '/oauth2/token?api-version=1.0'
	token_response = requests.post(request_url, data=bodyvals)

	access_token = token_response.json().get('access_token')
	token_type = token_response.json().get('token_type')

	if access_token is None or token_type is None:
	    print "ERROR: Couldn't get access token"
	    sys.exit(1)

	# Use the access token to make the API request
	yesterday = datetime.date.strftime(datetime.date.today() - datetime.timedelta(days=1), '%Y-%m-%d')

	header_params = {'Authorization': token_type + ' ' + access_token}
	request_string = 'https://graph.windows.net/' + tenant_domain + '/reports/auditEvents?api-version=beta&$filter=eventTime%20gt%20' + yesterday   
	response = requests.get(request_string, headers = header_params)

	if response.status_code is 200:
	    print response.content
	else:
	    print 'ERROR: API request failed'


## Execute o script
Quando você terminar de editar o script, execute-o e verifique se os dados esperados do relatório AuditEvents são retornados.

O script retorna a saída do relatório auditEvents no formato JSON. Ele também cria um arquivo `auditEvents.json` com a mesma saída. Você pode experimentar ao modificar o script para retornar dados de outros relatórios, além de comentar os formatos de saída de que você não precisa.

## Observações

- Não há qualquer limite para o número de eventos retornados pela API de Relatórios do Azure AD (usando a paginação de OData).
- Para conhecer os limites de retenção em dados de relatório, confira [Políticas de retenção de relatório](active-directory-reporting-retention.md).


## Próximas etapas
- Curioso sobre quais relatórios de segurança, auditoria e atividade estão disponíveis? Verifique [Relatórios de Segurança, Auditoria e Atividade do Azure AD](active-directory-view-access-usage-reports.md). Você também pode ver todos os pontos de extremidade disponíveis da API do Azure AD Graph, navegando até [https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta](https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta), que estão documentadas no artigo [Azure AD Reports and Events (Preview)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview) (Eventos e Relatórios de Auditoria do Azure AD [Preview]).
- Consulte [Eventos de relatório de auditoria do AD do Azure](active-directory-reporting-audit-events.md) para obter mais detalhes sobre o relatório de auditoria
- Consulte [Azure AD Reports and Events (Preview)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) (Eventos e Relatórios de Auditoria do Azure AD [Preview]) para obter mais detalhes sobre o serviço da API REST do Azure AD Graph.

<!---HONumber=AcomDC_0720_2016-->