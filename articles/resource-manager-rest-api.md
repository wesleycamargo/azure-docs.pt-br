<properties
   pageTitle="APIs REST do Gerenciador de Recursos| Microsoft Azure"
   description="Uma visão geral dos exemplos de autenticação e de uso de APIs REST do Gerenciador de Recursos"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="navale;tomfitz;"/>
   
# APIs REST do Gerenciador de Recursos
Por trás de todas as chamadas ao Gerenciador de Recursos do Azure, de cada modelo implantado, de todas as contas de armazenamento configuradas há uma ou várias chamadas à API RESTful do Gerenciador de Recursos do Azure. Este tópico é dedicado a essas APIs e como você pode chamá-las sem precisar usar o SDK. Isso poderá ser muito útil se você quiser ter controle total de todas as solicitações no Azure ou se o SDK para seu idioma não estiver disponível ou não der suporte às operações que você deseja executar.

Este artigo não abordará todas as APIs expostas no Azure, mas usará algumas como exemplo de como você pode prosseguir e se conectar a elas. Se você compreender os fundamentos, poderá então prosseguir com a leitura de [Referência da API REST do Gerenciamento de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx) para encontrar informações detalhadas sobre como usar o restante das APIs.

## Autenticação
A autenticação para ARM é tratada pelo Azure AD (Active Directory). Para se conectar a uma API, você primeiro precisa se autenticar com o Azure AD para receber um token de autenticação que poderá ser passado em cada solicitação. Como estamos descrevendo uma chamada simples diretamente às APIs REST, podemos também supor que você não queira autenticar com uma senha de nome de usuário normal, em que uma tela pop-up pode solicitar o nome de usuário e a senha ou até outros mecanismos de autenticação usados em cenários de autenticação de dois fatores. Portanto, vamos criar o que chamamos de Aplicativo do Azure AD e uma Entidade de Serviço que será usada para fazer logon. Mas lembre-se de que o Azure AD dá suporte a vários procedimentos de autenticação e todos eles podem ser usados para recuperar esse token de autenticação necessário para solicitações subsequentes de API. Siga [Criar Aplicativo do Azure AD e Entidade de Serviço](./resource-group-create-service-principal-portal.md) para obter instruções detalhadas.

### Gerando um token de acesso 
A autenticação no Azure AD é feita chamando o Azure AD localizado em login.microsoftonline.com. Para autenticar, você precisa ter as seguintes informações:

* A ID do Locatário do Azure AD (o nome deste Azure AD que você está usando para fazer logon, geralmente o mesmo da sua empresa, mas não necessariamente)
* ID do Aplicativo (obtida durante a etapa de criação do aplicativo do Azure AD)
* Senha (que você selecionou ao criar o Aplicativo do Azure AD)

Na solicitação HTTP abaixo, não deixe de substituir "ID de Locatário do Azure AD", "ID do Aplicativo" e "Senha" pelos valores corretos.

**Solicitação HTTP Genérica:**

```HTTP
POST /<Azure AD Tenant ID>.onmicrosoft.com/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

... resultará (se a autenticação for bem-sucedida) em uma resposta semelhante a esta:

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(O access\_token na resposta acima foi reduzido para aumentar a legibilidade)

**Gerando um token de acesso usando Bash:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token?api-version=1.0
```

**Gerando um token de acesso usando o PowerShell:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

A resposta contém um Token de Acesso, informações sobre o tempo de validade desse token e sobre quais recursos podem ser usados com o token. O token de acesso recebido na chamada HTTP anterior deve ser passado para todas as solicitações à API do ARM como um cabeçalho denominado "Portador YOUR\_ACCESS\_TOKEN". Observe o espaço entre "Portador" e o Token de Acesso.

Como você pode ver no Resultado HTTP acima, o token é válido por um período de tempo durante o qual você deve armazená-lo em cache e reutilizá-lo. Mesmo se fosse possível autenticar no Azure AD para cada chamada à API, isso seria muito ineficiente.

## Chamando as APIs REST do ARM

As [APIs REST do Gerenciador de Recursos do Azure estão documentadas aqui](https://msdn.microsoft.com/library/azure/dn790568.aspx) e a documentação do uso de cada uma delas está fora do escopo deste tutorial. Este documento usará apenas algumas APIs para explicar o uso básico das APIs e, depois disso, indicaremos a documentação oficial para você.

### Listar todas as assinaturas

Listar as assinaturas disponíveis que você pode acessar é uma das operações mais simples de realizar. Na solicitação a seguir, você pode ver como o Token de Acesso é passado como um cabeçalho.

(Substitua YOUR\_ACCESS\_TOKEN por seu Token de Acesso real.)

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

... e, como resultado, você obterá uma lista de assinaturas que essa Entidade de Serviço tem permissão para acessar

(As IDs de Assinatura abaixo foram reduzidas para facilitar a leitura)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### Listar todos os grupos de recursos em uma assinatura específica

Todos os recursos disponíveis com as APIs do ARM estão aninhados dentro de um Grupo de Recursos. Vamos consultar o ARM sobre Grupos de Recursos existentes em nossa assinatura usando a Solicitação HTTP GET abaixo. Observe como a ID de Assinatura é passada como parte da URL dessa vez.

(Substitua YOUR\_ACCESS\_TOKEN e SUBSCRIPTION\_ID pelo seu Token de Acesso e ID de Assinatura reais)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

A resposta dependerá de você ter ou não grupos de recursos definidos e, em caso afirmativo, quantos.

(As IDs de Assinatura abaixo foram reduzidas para facilitar a leitura)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### Criar um grupo de recursos

Até agora, consultamos as APIs do ARM apenas para obter informações. Agora é hora de criar recursos. Vamos começar com o mais simples, um grupo de recursos. A solicitação HTTP a seguir cria um novo Grupo de Recursos em um região/local de sua escolha e adiciona uma ou mais marcas a ele (o exemplo abaixo, na verdade, apenas adiciona uma marca).

(Substitua YOUR\_ACCESS\_TOKEN, SUBSCRIPTION\_ID, RESOURCE\_GROUP\_NAME pelo Token de Acesso, pela ID de Assinatura e pelo nome do Grupo de Recursos que deseja criar)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

Se for bem-sucedido, você obterá uma resposta semelhante a esta

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Você criou um Grupo de Recursos no Azure com êxito. Parabéns!

### Implantar recursos em um Grupo de Recursos usando um Modelo de ARM

Com o ARM, você pode implantar os recursos usando Modelos de ARM. Um Modelo de ARM define vários recursos e suas dependências. Para esta seção, vamos supor que você já esteja familiarizado com Modelos de ARM e mostraremos como fazer uma chamada à API para iniciar uma implantação. Uma documentação detalhada dos Modelos de ARM pode ser encontrada aqui.

A implantação de um modelo de ARM não é muito diferente da ação de chamar outras APIs. Um aspecto importante é que a implantação de um modelo pode levar muito tempo, dependendo do que há dentro do modelo, e a chamada à API apenas retornará. Cabe a você, como desenvolvedor, consultar o status da implantação para descobrir quando a implantação foi concluída.

Neste exemplo, usaremos um Modelo de ARM exposto publicamente disponível no [GitHub](https://github.com/Azure/azure-quickstart-templates). O modelo que vamos usar implantará uma VM do Linux no Oeste dos EUA. Embora esse modelo terá o modelo disponível em um repositório público como o GitHub, você também pode escolher passar o modelo completo como parte da solicitação. Observe que podemos fornecer valores de parâmetro como parte da solicitação que será usada dentro do modelo usado.

(Substitua SUBSCRIPTION\_ID, RESOURCE\_GROUP\_NAME, DEPLOYMENT\_NAME, YOUR\_ACCESS\_TOKEN, GLOBALY\_UNIQUE\_STORAGE\_ACCOUNT\_NAME, ADMIN\_USER\_NAME,ADMIN\_PASSWORD e DNS\_NAME\_FOR\_PUBLIC\_IP pelos valores adequados para sua solicitação)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

A resposta JSON bastante longa para essa solicitação foi omitida para melhorar a legibilidade deste documento. A resposta conterá informações sobre a implantação do modelo que você acabou de criar.

<!---HONumber=AcomDC_0323_2016-->