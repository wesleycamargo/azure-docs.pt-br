---
title: Script do Python para recuperar dados do Azure Log Analytics | Microsoft Docs
description: "A API da Pesquisa de Logs do Log Analytics permite que qualquer cliente da API REST recupere dados de um espaço de trabalho do Log Analytics.  Este artigo fornece um script de exemplo do Python que usa a API da Pesquisa de Logs."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: bwren
ms.openlocfilehash: a8a4ec7a6ddf2daeca6ead11460fa076a7eb5c94
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2017
---
# <a name="retrieve-data-from-log-analytics-with-a-python-script"></a>Recuperar dados do Log Analytics com um script do Python
A [API da Pesquisa de Logs do Log Analytics](log-analytics-log-search-api.md) permite que qualquer cliente da API REST recupere dados de um espaço de trabalho do Log Analytics.  Este artigo apresenta um script de exemplo do Python que usa a API da Pesquisa de Logs do Log Analytics.  

>[!NOTE]
> Este artigo usa a API de Pesquisa de logs para a linguagem de consulta herdada no Log Analytics.  Uma atualização será fornecida para este artigo para espaços de trabalho que foram atualizados para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md).

## <a name="authentication"></a>Autenticação
Esse script usa uma entidade de serviço no Azure Active Directory para autenticação no espaço de trabalho.  As entidades de serviço permitem que um aplicativo cliente solicite que o serviço autentique uma conta, mesmo se o cliente não tiver o nome da conta. Antes de executar esse script, você deve criar uma entidade de serviço usando o processo descrito em [Usar o portal para criar um aplicativo e uma entidade de serviço do Azure Active Directory que pode acessar recursos](../azure-resource-manager/resource-group-create-service-principal-portal.md).  Você precisará fornecer a ID do Aplicativo, a ID do Locatário e a Chave de Autenticação para o script. 

> [!NOTE]
> Quando você [cria uma conta da Automação do Azure](../automation/automation-create-standalone-account.md), é criada uma entidade de serviço que é adequada para ser usada com esse script.  Se você já tiver uma entidade de serviço criada pela Automação do Azure, poderá usá-la em vez de criar uma nova, embora talvez você precise [criar uma chave de autenticação](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key), caso ela ainda não tenha uma.

## <a name="script"></a>Script
``` python
import adal
import requests
import json
import datetime
from pprint import pprint

# Details of workspace.  Fill in details for your workspace.
resource_group = 'xxxxxxxx'
workspace = 'xxxxxxxx'

# Details of query.  Modify these to your requirements.
query = "Type=Event"
end_time = datetime.datetime.utcnow()
start_time = end_time - datetime.timedelta(hours=24)
num_results = 100  # If not provided, a default of 10 results will be used.

# IDs for authentication.  Fill in values for your service principal.
subscription_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
tenant_id = 'xxxxxxxx-xxxx-xxxx-xxx-xxxxxxxxxxxx'
application_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx'
application_key = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'

# URLs for authentication
authentication_endpoint = 'https://login.microsoftonline.com/'
resource  = 'https://management.core.windows.net/'

# Get access token
context = adal.AuthenticationContext('https://login.microsoftonline.com/' + tenant_id)
token_response = context.acquire_token_with_client_credentials('https://management.core.windows.net/', application_id, application_key)
access_token = token_response.get('accessToken')

# Add token to header
headers = {
    "Authorization": 'Bearer ' + access_token,
    "Content-Type":'application/json'
}

# URLs for retrieving data
uri_base = 'https://management.azure.com'
uri_api = 'api-version=2015-11-01-preview'
uri_subscription = 'https://management.azure.com/subscriptions/' + subscription_id
uri_resourcegroup = uri_subscription + '/resourcegroups/'+ resource_group
uri_workspace = uri_resourcegroup + '/providers/Microsoft.OperationalInsights/workspaces/' + workspace
uri_search = uri_workspace + '/search'

# Build search parameters from query details
search_params = {
        "query": query,
        "top": num_results,
        "start": start_time.strftime('%Y-%m-%dT%H:%M:%S'),
        "end": end_time.strftime('%Y-%m-%dT%H:%M:%S')
        }

# Build URL and send post request
uri = uri_search + '?' + uri_api
response = requests.post(uri,json=search_params,headers=headers)

# Response of 200 if successful
if response.status_code == 200:

    # Parse the response to get the ID and status
    data = response.json()
    search_id = data["id"].split("/")
    id = search_id[len(search_id)-1]
    status = data["__metadata"]["Status"]

    # If status is pending, then keep checking until complete
    while status == "Pending":

        # Build URL to get search from ID and send request
        uri_search = uri_search + '/' + id
        uri = uri_search + '?' + uri_api
        response = requests.get(uri,headers=headers)

        # Parse the response to get the status
        data = response.json()
        status = data["__metadata"]["Status"]

else:

    # Request failed
    print (response.status_code)
    response.raise_for_status()

print ("Total records:" + str(data["__metadata"]["total"]))
print ("Returned top:" + str(data["__metadata"]["top"]))
pprint (data["value"])
```
## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre a [API da Pesquisa de Logs do Log Analytics](log-analytics-log-search-api.md).