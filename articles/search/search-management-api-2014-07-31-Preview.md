<properties pageTitle="Visualização da versão da API REST de Gerenciamento da Pesquisa do Azure de 31-07-2014" description="API REST do Gerenciamento da Pesquisa do Azure: versão 2014-07-31-Visualização" services="search" documentationCenter="" authors="HeidiSteen" manager="mblythe" editor=""/>

<tags ms.service="search" ms.devlang="rest-api" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="na" ms.date="07/08/2015" ms.author="heidist" />

# Gerenciamento de API: versão 2014-07-31-Visualização

Este documento descreve a versão **2014-07-31-Visualização** da API REST do Serviço de Pesquisa do Azure. Desde então, foi substituído por versões mais recentes. Para obter a versão mais recente, consulte [API REST de gerenciamento da Pesquisa do Azure 2015 08-19](https://msdn.microsoft.com/library/dn832684.aspx) no MSDN. \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

## Operações de gerenciamento de serviço

A API REST de Gerenciamento do Serviço de Pesquisa do Azure fornece acesso programático a grande parte das funcionalidades disponíveis no portal, permitindo que os administradores automatizem as seguintes operações:

- Criar ou excluir um serviço de Pesquisa do Azure.
- Criar, alterar ou recuperar `api-keys` para automatizar alterações regulares nas chaves administrativas usadas para autenticar operações de dados de pesquisa.
- Ajustar a escala de um serviço de Pesquisa do Azure em resposta a alterações nos requisitos de volume de consulta ou de armazenamento.

Para administrar o serviço de forma totalmente programática, você precisará de duas APIs: a API REST de Gerenciamento da Pesquisa do Azure e a [API REST comum do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx). A API do Gerenciador de Recursos é usada para operações de uso geral que não são específicas de serviços, como consultar dados de assinatura, listar localizações geográficas e assim por diante. Para criar e gerenciar os serviços da Pesquisa do Azure em sua assinatura, verifique se a solicitação HTTP inclui o ponto de extremidade do Gerenciador de Recursos, a ID da assinatura, o provedor (nesse caso, a Pesquisa do Azure) e a operação específica do serviço de pesquisa.

[Introdução à API REST do Gerenciamento da Pesquisa do Azure](http://go.microsoft.com/fwlink/p/?linkID=516968) é uma explicação passo a passo de um código de exemplo que demonstra operações de configuração de aplicativos e o gerenciamento de serviços. O aplicativo de exemplo emite solicitações para a API do Gerenciador de Recursos do Azure, bem como a API de gerenciamento de serviço para a Pesquisa do Azure, dando a você uma noção de como criar um aplicativo coeso que tire proveito de ambas as APIs.

### Ponto de extremidade

O ponto de extremidade para operações de administração de serviços é a URL do Gerenciador de Recursos do Azure, `https://management.azure.com`.

Observe que todas as chamadas à API de gerenciamento devem incluir a ID da assinatura e uma versão da API.

### Versões

A `api-version=2014-07-31-Preview` foi introduzida durante a visualização pública da Pesquisa do Azure. Para saber mais sobre as alterações de uma versão para outra, consulte [Novidades na Pesquisa do Azure](search-latest-updates.md).

### Autenticação e controle de acesso

A API REST de Gerenciamento da Pesquisa do Azure é uma extensão do Gerenciador de Recursos do Azure e compartilha suas dependências. Assim, o Active Directory é um pré-requisito para a administração do serviço de Pesquisa do Azure. Todas as solicitações administrativas do código do cliente devem ser autenticadas usando o Active Directory do Azure antes que a solicitação atinja o gerenciador de recursos.

Observe que se o seu código de aplicativo lidar com *operações de gerenciamento de serviço* bem como *operações de dados* em índices ou em documentos, você usará duas abordagens de autenticação para cada uma das APIs da Pesquisa do Azure:

- A administração de chaves e de serviços, devido à dependência em relação ao Gerenciador de Recursos, depende do Active Directory para autenticação.
- As solicitações de dados em relação ao ponto de extremidade do serviço de Pesquisa do Azure, como Criar Índice ou Pesquisar Documentos, usam uma `api-key` no cabeçalho da solicitação. Confira [API REST do serviço de Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre como autenticar uma solicitação de dados.

O aplicativo de exemplo documentado em [Introdução à API REST de Gerenciamento da Pesquisa do Azure](http://go.microsoft.com/fwlink/p/?linkID=516968) demonstra as técnicas de autenticação para cada tipo de operação. As instruções sobre como configurar um aplicativo cliente para usar o Active Directory estão incluídas na introdução.

O controle de acesso para o Gerenciador de Recursos do Azure usa as funções internas Proprietário, Colaborador e Leitor. Por padrão, todos os administradores de serviço são membros da função Proprietário. Para mais detalhes, confira [Controle de acesso baseado em função no portal de Visualização do Azure](../role-based-access-control-configure.md).


### Resumo das APIs

As operações incluem as APIs a seguir.

- <a name="CreateService">Criar Serviço de Pesquisa</a>

    `PUT	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview`

- <a name="GetService">Obter Serviço de Pesquisa</a>

    `GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview`

- <a name="ListService">Listar Serviços de Pesquisa</a>

    `GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices?api-version=2014-07-31-Preview`

- <a name="DeleteService">Excluir Serviço de Pesquisa</a>

    `DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview`

- <a name="UpdateService">Atualizar Serviço de Pesquisa</a>

    `PATCH https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview`

- <a name="ListAdminKey">Listar Chaves de Administração</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listAdminKeys?api-version=2014-07-31-Preview`

- <a name="RegenAdminKey">Regenerar Chave de Administração</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/regenerateAdminKey/[keyKind]?api-version=2014-07-31-Preview`

- <a name="CreateQueryKey">Criar Chave de Consulta</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/createQueryKey/[name]?api-version=2014-07-31-Preview`

- <a name="ListQueryKey">Listar Chaves de Consulta</a>

    `GET	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listQueryKeys?api-version=2014-07-31-Preview`

- <a name="DeleteQueryKey">Excluir Chaves de Consulta</a>

    `DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/deleteQueryKey/[key]?api-version=2014-07-31-Preview`

<a name="ServiceOps"></a>
## Operações de serviço

Você pode provisionar ou desprovisionar serviços de Pesquisa do Azure emitindo solicitações HTTP em relação à sua assinatura do Azure. Os cenários habilitados por meio dessas operações incluem a criação de ferramentas de administração personalizadas ou a preparação de um ambiente de produção ou desenvolvimento de ponta a ponta (desde criar o serviço até preencher um índice). Da mesma forma, os fornecedores de soluções que criam e vendem soluções de nuvem podem querer uma abordagem automatizada e reprodutível para provisionar serviços para cada novo cliente.

**Operações em um serviço**

As opções relacionadas ao serviço incluem as seguintes APIs:

- <a name="CreateService">Criar Serviço de Pesquisa</a>
- <a name="GetService">Obter Serviço de Pesquisa</a>
- <a name="ListService">Listar Serviços de Pesquisa</a>
- <a name="DeleteService">Excluir Serviço de Pesquisa</a>
- <a name="UpdateService">Atualizar Serviço de Pesquisa</a>


<a name="CreateService"></a>
## Criar Serviço de Pesquisa

A operação **Criar Serviço de Pesquisa** provisiona um novo serviço de pesquisa com os parâmetros especificados. Essa API também pode ser usada para atualizar uma definição de serviço existente.

    PUT	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview

### Solicitar parâmetros de URI

`subscriptionId`: obrigatório. O `subscriptionID` para o usuário do Azure. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`resourceGroupName`: obrigatório. O nome do grupo de recursos na assinatura do usuário. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`serviceName`: obrigatório. O nome do serviço de pesquisa dentro do grupo de recursos especificado. Nomes de serviço devem conter apenas letras minúsculas, dígitos ou traços, não podem usar um traço como os dois primeiros caracteres ou como o último caractere, não podem conter traços consecutivos e devem ter de 2 a 15 caracteres. Como todos os nomes acabam sendo <name>. search.windows.net, os nomes de serviço devem ser globalmente exclusivos. Não pode haver dois serviços em uma ou mais assinaturas ou em grupos de recursos com o mesmo nome. Você não pode alterar o nome do serviço após a sua criação.

`api-version`: obrigatório. Especifica a versão do protocolo usado para esta solicitação. À medida que novas versões se tornarem disponíveis, você poderá especificar qual delas usar em cada solicitação para obter comportamentos específicos da versão.


### Cabeçalhos da solicitação

`Content-Type`: obrigatório. Defina este cabeçalho como application/json.

`x-ms-client-request-id`: opcional. Um valor de GUID gerado pelo cliente que identifica esta solicitação. Se especificado, esse valor será incluído nas informações de resposta como uma maneira de mapear a solicitação.


### Corpo da solicitação

{ "location": "location of search service", "tags": { "key": "value", ... }, "properties": { "sku": { "name": "free | standard | standard2" }, "replicaCount": 1 | 2 | 3 | 4 | 5 | 6, "partitionCount": 1 | 2 | 3 | 4 | 6 | 12 } }

### Parâmetros do corpo da solicitação

`location`: obrigatório. Uma das regiões geográficas do Azure registradas e com suporte (por exemplo, oeste dos EUA, leste dos EUA, sudeste da Ásia e assim por diante). Observe que o local de um recurso não pode ser alterado depois de ser criado.

`tags`: opcional. Uma lista de pares de chave e valor que descrevem o recurso. Essas marcas podem ser usadas para exibir e agrupar um recurso entre grupos de recursos. Podem ser fornecidas no máximo 10 marcas para um recurso. Cada marca deve ter uma chave com no máximo 128 caracteres e um valor com no máximo 256 caracteres.

`sku`: obrigatório. Os valores válidos são `free` e `standard`. `standard2` também é válido, mas só pode ser usado quando é habilitado para sua assinatura do Azure pelo suporte da Microsoft. `free` provisiona o serviço em clusters compartilhados. `standard` provisiona o serviço em clusters dedicados. Você só pode criar um serviço de pesquisa na camada de preços gratuita. Serviços adicionais devem ser criados na camada de preços padrão. Por padrão, um serviço é criado com uma partição e uma réplica. Réplicas e partições adicionais são cobradas em termos de unidades de pesquisa. Confira [Limites e restrições](http://msdn.microsoft.com/library/azure/dn798934.aspx) para obter detalhes. Você não pode alterar o `sku` depois que o serviço é criado.

`replicaCount`: opcional. O padrão é UTF-1. Os valores válidos estão incluídos entre 1 e 6. Válido somente quando `sku` é `standard`.

`partitionCount`: opcional. O padrão é UTF-1. Os valores válidos são 1, 2, 3, 4, 6 ou 12. Válido somente quando `sku` é `standard`.


### Resposta 

HTTP 200 (OK) é retornado quando uma definição de serviço é atualizada. HTTP 201 (criado) é retornado quando um novo serviço é criado.


### Cabeçalhos de resposta 

`Content-Type`: esse cabeçalho é sempre definido como application/json.

`x-ms-request-id`: um identificador exclusivo para a operação atual, gerado pelo serviço.


### Corpo da resposta

Para HTTP 200 e 201, o corpo da resposta contém a definição de serviço.
    
    { 
      "id": "/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]", 
    
      "name": "service name", 
      "location": "location of search service", 
    "type": " Microsoft.Search/searchServices", 
      "tags": {
        "key": "value",
        ...
      },  
    "properties": { 
    	"sku": { 
          "name": "free | standard | standard2" 
       		 }, 
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6, 
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12, 
        "status": "running | provisioning | deleting | degraded | disabled | error", 
        "statusDetails": "details of the status", 
        "provisioningState": "succeeded | provisioning | failed" 
      } 
    } 


### Elementos do corpo da resposta

`id`: a ID é a URL (exceto o nome do host/esquema) para esse serviço de Pesquisa.

`name`: o nome do serviço de pesquisa.

`location`: uma das regiões geográficas do Azure registradas e com suporte (por exemplo, oeste dos EUA, leste dos EUA, sudeste da Ásia e assim por diante).

`tags`: uma lista de pares de valores de chave que descrevem o recurso, usada para exibir e agrupar recursos entre grupos de recursos.

`sku`: indica a camada de preços. Os valores válidos incluem:

- `free`: cluster compartilhado
- `standard`: cluster dedicado
- `standard2`: use somente de acordo com as diretrizes do suporte da Microsoft. 

`replicaCount`: indica quantas réplicas o serviço tem. Os valores válidos estão incluídos entre 1 e 6.

`partitionCount`: Indica quantas partições o serviço tem. Os valores válidos são 1, 2, 3, 4, 6 ou 12.

`status`: o status do serviço de pesquisa no momento em que a operação foi chamada. Os valores válidos incluem:

- `running`: o serviço de pesquisa é criado.
- `provisioning`: o serviço de pesquisa está sendo provisionado.
- `deleting`: o serviço de pesquisa está sendo excluído.
- `degraded`: o serviço de pesquisa é degradado. Isso pode ocorrer quando o cluster encontra um erro que pode ou não impedir que o serviço funcione corretamente.
- `disabled`: a pesquisa está desabilitada. Nesse estado, o serviço rejeita todas as solicitações da API.
- `error`: o serviço de pesquisa está em estado de erro. 

**Observação**: se o serviço estiver no estado `degraded`, `disabled` ou `error`, significa que a equipe da Pesquisa do Azure está investigando ativamente o problema subjacente. Serviços dedicados nesses estados ainda são passíveis de cobrança com base no número de unidades de pesquisa provisionadas.

`statusDetails`: os detalhes do status.

`provisioningState`: indica o estado atual do provisionamento de serviço. Os valores válidos incluem:

- `succeeded`: o provisionamento é concluído com êxito.
- `provisioning`: o serviço está sendo provisionado.
- `failed`: o serviço não foi provisionado. 

O provisionamento é um estado intermediário que ocorre enquanto a capacidade de serviço está sendo estabelecida. Depois que a capacidade é configurada, o `provisioningState` é alterado para "êxito" ou "falha". Aplicativos cliente podem pesquisar o status de provisionamento (o intervalo de pesquisa recomendado é de 30 segundos a um minuto) usando a operação **Obter Serviço de Pesquisa** para ver quando uma operação é concluída. Se você estiver usando o serviço gratuito, esse valor tenderá a ser retornado como "êxito" diretamente na chamada para criar o serviço. Isso ocorre porque o serviço gratuito usa a capacidade que já está configurada.

<a name="GetService"></a>
## Obter Serviço de Pesquisa

A operação **Obter Serviço de Pesquisa** retorna as propriedades para o serviço de pesquisa especificado. Observe que chaves de administração não são retornadas. Use a operação **Obter Chaves de Administração** para recuperar chaves de administração.

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview

### URI da solicitação 

`subscriptionId`: obrigatório. A subscriptionID do usuário do Azure. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`resourceGroupName`: obrigatório. O nome do grupo de recursos na assinatura do usuário. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`serviceName`: obrigatório. O nome do serviço de Pesquisa dentro do grupo de recursos especificado. Se não souber o nome do serviço, você poderá obter uma lista usando Listar Serviços de Pesquisa (API da Pesquisa do Azure).

`api-version`: obrigatório. Especifica a versão do protocolo usado para esta solicitação. À medida que novas versões se tornarem disponíveis, você poderá especificar qual delas usar em cada solicitação para obter comportamentos específicos da versão.

### Cabeçalhos da solicitação 

`x-ms-client-request-id`: opcional. Um valor de GUID gerado pelo cliente que identifica esta solicitação. Se especificado, esse valor será incluído nas informações de resposta como uma maneira de mapear a solicitação.


### Corpo da solicitação 

Nenhum.


### Código de status de resposta 

HTTP 200 (OK), caso bem-sucedido.


### Cabeçalhos de resposta 

`Content-Type`: esse cabeçalho é sempre definido como application/json.

`x-ms-request-id`: um identificador exclusivo para a operação atual, gerado pelo serviço.

### Corpo da resposta 

    {
      "id": "/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]",
    
      "name": "service name",
      "location": "location of search service",
    "type": " Microsoft.Search/searchServices",
      "tags": {
        "key": "value",
        ... 
      }, 
    "properties": {
    	"sku": {
          "name": "free | standard | standard2"
    	},
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
        "status": "running | provisioning | deleting | degraded | disabled | error",
        "statusDetails": "details of the status",
        "provisioningState": "succeeded | provisioning | failed"
      }
    }

### Elementos do corpo da resposta

`id`: a ID é a URL (exceto o nome do host/esquema) para esse serviço de Pesquisa.

`name`: o nome do serviço de Pesquisa.

`location`: o local do recurso. Essa será uma das regiões geográficas do Azure registradas e com suporte (por exemplo, oeste dos EUA, leste dos EUA, sudeste da Ásia e assim por diante).

`tags`: as marcas são uma lista de pares de valores de chave que descrevem o recurso. Essas marcas podem ser usadas para exibir e agrupar um recurso entre grupos de recursos.

`sku`: indica a camada de preços. Os valores válidos incluem:

- `free`: cluster compartilhado
- `standard`: cluster dedicado
- `standard2`: use somente de acordo com as diretrizes do suporte da Microsoft.

`replicaCount`: indica quantas réplicas o serviço tem. Os valores válidos vão de 1 a 6.

`partitionCount`: Indica quantas partições o serviço tem. Os valores válidos são 1, 2, 3, 4, 6 ou 12.

`status`: o status do serviço de pesquisa no momento em que a operação foi chamada. Os valores válidos incluem:

- `running`: o serviço de pesquisa é criado.
- `provisioning`: o serviço de pesquisa está sendo provisionado.
- `deleting`: o serviço de pesquisa está sendo excluído.
- `degraded`: o serviço de pesquisa é degradado. Isso pode ocorrer quando o cluster encontra um erro que pode ou não impedir que o serviço funcione corretamente.
- `disabled`: a pesquisa está desabilitada. Nesse estado, o serviço rejeita todas as solicitações da API.
- `error`: o serviço de pesquisa está em estado de erro. 
 
**Observação**: se o serviço estiver no estado `degraded`, `disabled` ou `error`, significa que a equipe da Pesquisa do Azure está investigando ativamente o problema subjacente. Serviços dedicados nesses estados ainda são passíveis de cobrança com base no número de unidades de pesquisa provisionadas.
 
`statusDetails`: os detalhes do status.

`provisioningState`: os valores válidos incluem:

- `succeeded`: o provisionamento foi bem-sucedido.
- `provisioning`: o serviço está sendo provisionado.
- `failed`: o provisionamento falhou.


<a name="ListService"></a>
## Listar serviços de pesquisa

A operação **Listar Serviços** retorna uma lista de todos os serviços de pesquisa na assinatura de um grupo de recursos específico. Essa operação retorna as definições de serviço, menos as chaves de api de administração. Use a operação **Obter Chaves de Administração** para recuperar chaves de administração.

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices?api-version=2014-07-31-Preview
    
### Solicitar parâmetros de URI 

`subscriptionId`: obrigatório. O `subscriptionID` para o usuário do Azure. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`resourceGroupName`: obrigatório. O nome do grupo de recursos na assinatura do usuário. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`api-version`: obrigatório. Especifica a versão do protocolo usado para esta solicitação. À medida que novas versões se tornarem disponíveis, você poderá especificar qual delas usar em cada solicitação para obter comportamentos específicos da versão.

### Cabeçalhos da solicitação

`x-ms-client-request-id`: opcional. Um valor de GUID gerado pelo cliente que identifica esta solicitação. Se especificado, esse valor será incluído nas informações de resposta como uma maneira de mapear a solicitação.

###Corpo da solicitação

Nenhum.

###Resposta

O código de status é HTTP 200 (OK), caso bem-sucedido.

### Cabeçalhos de resposta

`Content-Type`: esse cabeçalho é sempre definido como application/json.

`x-ms-request-id`: um identificador exclusivo para a operação atual, gerado pelo serviço.


### Corpo da resposta

O corpo da resposta é uma lista de serviços, retornados como uma matriz JSON, em que cada serviço segue o formato na operação **Obter serviço de pesquisa**.

Observe que o campo `nextLink` é sempre nulo porque a versão atual não dá suporte à paginação. Ele é retornado com um valor vazio para preservar a compatibilidade futura.

    {
      "value": [
     	{
          "id": "id of service 1",
          "name": "service name",
          "location": "location of search service 1",
    	"type": " Microsoft.Search/searchServices",
          "tags": {
            "key": "value",
            ...
          }, 
    	"properties": {
            "sku": {
              "name": "free | standard | standard2"
            },
            "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
            "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
            "status": "running | provisioning | deleting | degraded | disabled | error",
            "statusDetails": "details of the status",
            "provisioningState": "succeeded | provisioning | failed"
    	}
      },   
      {
          "id": "id of service 2",
          "name": "service name 2",
          "location": "location of search service 2",
   	 	"type": " Microsoft.Search/searchServices",
          "tags": {
            "key": "value",
            ...
          },
    	"properties": {
            "sku": { 
              "name": "free | standard | standard2"
            },
            "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
            "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
            "status": "running | provisioning | deleting | degraded | disabled | error",
            "statusDetails": "details of the status",
            "provisioningState": "succeeded | provisioning | failed"
    	}
      }
    ],
    "nextLink": null
    }


<a name="DeleteService"></a>
## Excluir serviço 

A operação **Excluir serviço** exclui os dados de pesquisa e do serviço de pesquisa, incluindo todos os índices e documentos.
    
    DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview

**Observação:** administradores e desenvolvedores estão acostumados a fazer backup dos dados de aplicativos antes de excluí-los de um servidor de produção. Na Pesquisa do Azure, não há operação de backup. Se estiver usando o índice como armazenamento primário para seu aplicativo, você precisará usar uma operação de pesquisa para retornar todos os dados no índice, que podem ser armazenados externamente.

###Solicitar parâmetros de URI

`subscriptionId`: obrigatório. A subscriptionID do usuário do Azure. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`resourceGroupName`: obrigatório. O nome do grupo de recursos na assinatura do usuário. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`serviceName`: obrigatório. O nome do serviço de pesquisa dentro do grupo de recursos especificado. Se não souber o nome do serviço, você poderá obter uma lista usando Listar Serviços de Pesquisa (API da Pesquisa do Azure).

`api-version`: obrigatório. Especifica a versão do protocolo usado para esta solicitação. À medida que novas versões se tornarem disponíveis, você poderá especificar qual delas usar em cada solicitação para obter comportamentos específicos da versão.

###Cabeçalhos da solicitação

`x-ms-client-request-id`: opcional. Um valor de GUID gerado pelo cliente que identifica esta solicitação. Se especificado, esse valor será incluído nas informações de resposta como uma maneira de mapear a solicitação.

###Corpo da solicitação

Nenhum.

###Resposta

Para HTTP 200, o corpo da resposta estará vazio. HTTP 200 (OK) será a resposta correta se o recurso não existir.

Você pode usar **Obter API do Serviço de Pesquisa** para sondar o status do serviço de exclusão. Recomendamos intervalos de sondagem de 30 segundos a um minuto.

###Cabeçalhos de resposta

`x-ms-request-id`: um identificador exclusivo para a operação atual, gerado pelo serviço.

###Corpo da resposta

Nenhum.

<a name="UpdateService"></a>
## Atualizar Serviço

A operação **Atualizar Serviço** altera a configuração do serviço de pesquisa. Alterações válidas incluem alterar as marcas, a partição ou a contagem de réplicas, o que adiciona ou remove unidades de pesquisa do serviço como um evento faturável. Se você tentar diminuir as partições abaixo da quantidade necessária para armazenar o corpus de pesquisa existente, ocorrerá um erro, bloqueando a operação. Alterações na topologia do serviço podem levar algum tempo. Leva tempo para realocar dados e configurar ou subdividir clusters no data center.

Observe que você não pode alterar o nome, o local e a SKU. A alteração de uma dessas propriedades requer que você crie um novo serviço.

    PATCH https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview

Como alternativa, você pode usar PUT.

    PUT https://management.azure.com/subscriptions/[subscriptionId]/resourcegroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2014-07-31-Preview

###Solicitar parâmetros de URI

`subscriptionId`: obrigatório. O `subscriptionID` para o usuário do Azure. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`resourceGroupName`: obrigatório. O nome do grupo de recursos na assinatura do usuário. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`serviceName`: obrigatório. O nome do serviço de pesquisa dentro do grupo de recursos especificado. Se não souber o nome do serviço, você poderá obter uma lista usando Listar Serviços de Pesquisa (API da Pesquisa do Azure).

`api-version`: obrigatório. Especifica a versão do protocolo usado para esta solicitação. À medida que novas versões se tornarem disponíveis, você poderá especificar qual delas usar em cada solicitação para obter comportamentos específicos da versão.

###Cabeçalhos da solicitação

`Content-Type`: obrigatório. Defina este cabeçalho como application/json.

`x-ms-client-request-id`: opcional. Um valor de GUID gerado pelo cliente que identifica esta solicitação. Se especificado, esse valor será incluído nas informações de resposta como uma maneira de mapear a solicitação.

###Corpo da solicitação

    {
      "tags": {
        "key": "value",
        ...
      }, 
    	"properties": {
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12
     	}
    }

###Parâmetros do corpo da solicitação

`tags`: opcional. Uma lista de pares de chave e valor que descrevem o recurso. Essas marcas podem ser usadas para exibir e agrupar esse recurso (entre grupos de recursos). Podem ser fornecidas no máximo 10 marcas para um recurso. Cada marca deve ter uma chave com no máximo 128 caracteres e um valor com no máximo 256 caracteres.

`replicaCount`: opcional. O padrão é UTF-1. Os valores válidos estão incluídos entre 1 e 6. Válido somente quando `sku` é `standard`.

`partitionCount`: opcional. O padrão é UTF-1. Os valores válidos são 1, 2, 3, 4, 6 ou 12. Válido somente quando `sku` é `standard`.

###Resposta

HTTP 200 (OK) será retornado se a operação for bem-sucedida. Você pode usar **Obter API do serviço de pesquisa** para sondar o status do serviço de atualização. Recomendamos intervalos de sondagem de 30 segundos a um minuto.


### Cabeçalhos de resposta 

`Content-Type`: esse cabeçalho é sempre definido como application/json.

`x-ms-request-id`: um identificador exclusivo para a operação atual, gerado pelo serviço.

### Corpo da resposta 

O corpo da resposta contém a definição de serviço atualizada. Para obter um exemplo, confira **Obter API do serviço de pesquisa**.


<a name="KeyOps"></a>
## Operações de chave

A autenticação para um serviço de Pesquisa do Azure exige duas informações: uma URL de serviço de pesquisa e uma chave de api. As chaves de api são geradas quando o serviço é criado e podem ser regeneradas sob demanda depois que o serviço é provisionado. Há dois tipos de chave de api.

- chave de administração: concede acesso para todas as operações (máximo de duas por serviço)
- chave de consulta: autentica apenas solicitações de consulta (máximo de 50 por serviço)

A capacidade de gerenciar de forma programática as chaves de administração e consulta de seu serviço de Pesquisa do Azure lhe oferece os meios para criar ferramentas personalizadas, substituir chaves periodicamente como uma prática recomendada de segurança de rotina, substituir chaves quando um funcionário deixa a empresa ou gerar e adquirir chaves durante o provisionamento de serviço quando uma abordagem programática ou com script é usada para implantar a solução.

As chaves de consulta podem ser adquiridas, criadas e excluídas. As operações de chave de administração são restritas à aquisição e à regeneração dos valores de chaves existentes. A exclusão de uma chave de administração pode travá-lo permanentemente do serviço, assim, a operação não está disponível.

As chaves são cadeias de caracteres composta de uma combinação aleatória de números e letras maiúsculas. Uma chave de api só pode ser usada com o serviço para o qual ela foi criada e pode ser alterada regularmente (se você adotar uma estratégia de substituição de chave como uma prática recomendada de segurança).

Trate as chaves de api, principalmente as chaves de administração, como dados confidenciais. Qualquer pessoa que adquirir sua chave de administração terá a capacidade de excluir ou ler dados de seus índices.

**Operações em chaves**

As operações relacionadas às chaves incluem as seguintes APIs:

- <a name="ListAdminKey">Listar Chaves de Administração</a>
- <a name="RegenAdminKey">Regenerar Chave de Administração</a>
- <a name="CreateQueryKey">Criar Chave de Consulta</a>
- <a name="ListQueryKey">Listar Chaves de Consulta</a>
- <a name="DeleteQueryKey">Excluir Chaves de Consulta</a>


<a name="ListAdminKey"></a>
## Listar Chaves de Administração 

A operação **Listar Chaves de Administração** retorna as chaves de administração primárias e secundárias para o serviço de pesquisa especificado. O método POST é usado porque essa ação retorna chaves de leitura/gravação.

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listAdminKeys?api-version=2014-07-31-Preview

Chaves de administração são criadas com o serviço. Há sempre duas chaves, a primária e a secundária. Você pode regenerar essas chaves, mas não pode excluí-las.

###Solicitar parâmetros de URI

`subscriptionId`: obrigatório. A subscriptionID do usuário do Azure. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`resourceGroupName`: obrigatório. O nome do grupo de recursos na assinatura do usuário. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`serviceName`: obrigatório. O nome do serviço de pesquisa dentro do grupo de recursos especificado. Se não souber o nome do serviço, você poderá obter uma lista usando Listar Serviços de Pesquisa (API da Pesquisa do Azure).

`api-version`: obrigatório. Especifica a versão do protocolo usado para esta solicitação. À medida que novas versões se tornarem disponíveis, você poderá especificar qual delas usar em cada solicitação para obter comportamentos específicos da versão.

`listAdminKeys`: obrigatório. Essa ação recupera as chaves de administração primárias e secundárias para o serviço de pesquisa.

###Cabeçalhos da solicitação

`x-ms-client-request-id`: opcional. Um valor de GUID gerado pelo cliente que identifica esta solicitação. Se especificado, esse valor será incluído nas informações de resposta como uma maneira de mapear a solicitação.

###Corpo da solicitação

Nenhum.

###Resposta

HTTP 200 (OK) será retornado se a operação for bem-sucedida.

### Cabeçalhos de resposta 

`Content-Type`: esse cabeçalho é sempre definido como application/json.

`x-ms-request-id`: um identificador exclusivo para a operação atual, gerado pelo serviço.

###Corpo da resposta

    {
      "primaryKey": "api key",
      "secondaryKey": "api key"
    }
    

<a name="RegenAdminKey"></a>
## Regenerar Chaves de Administração 

A operação **Regenerar Chaves de Administração** exclui e regenera a chave primária ou secundária. Você só pode regenerar uma chave de cada vez. Ao regenerar chaves, considere como você manterá o acesso ao serviço. Existe uma chave secundária para que você tenha uma chave disponível ao substituir a chave primária. Cada serviço sempre tem as duas chaves. Você pode regenerar chaves, mas não pode excluí-las nem executar um serviço sem elas.
 
    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/regenerateAdminKey/[keyKind]?api-version=2014-07-31-Preview

###Solicitar parâmetros de URI

`subscriptionId`: obrigatório. A subscriptionID do usuário do Azure. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`resourceGroupName`: obrigatório. O nome do grupo de recursos na assinatura do usuário. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`serviceName`: obrigatório. O nome do serviço de pesquisa dentro do grupo de recursos especificado. Se não souber o nome do serviço, você poderá obter uma lista usando Listar Serviços de Pesquisa (API da Pesquisa do Azure).

`api-version`: obrigatório. Especifica a versão do protocolo usado para esta solicitação. À medida que novas versões se tornarem disponíveis, você poderá especificar qual delas usar em cada solicitação para obter comportamentos específicos da versão.
	
`regenerateKey`: obrigatório. Essa ação especifica que a chave de administração primária ou secundária será regenerada.

`keyKind`: obrigatório. Especifica qual chave deve ser regenerada. Os valores válidos incluem:

- `primary`
- `secondary`

###Cabeçalhos da solicitação

`Content-Type`: obrigatório. Defina este cabeçalho como application/json.

`x-ms-client-request-id`: opcional. Um valor de GUID gerado pelo cliente que identifica esta solicitação. Se especificado, esse valor será incluído nas informações de resposta como uma maneira de mapear a solicitação.

###Corpo da solicitação

Nenhum.

###Resposta

HTTP 200 (OK) será retornado se a operação for bem-sucedida.

### Cabeçalhos de resposta 

`Content-Type`: esse cabeçalho é sempre definido como application/json.

`x-ms-request-id`: um identificador exclusivo para a operação atual, gerado pelo serviço.

###Corpo da resposta

    {
      "primaryKey": "api key",
      "secondaryKey": "api key"
    }
    
###Elementos do corpo da resposta

`primaryKey`: a chave de administração primária, caso tenha sido regenerada.

`secondaryKey`: a chave de administração secundária, caso tenha sido regenerada.



<a name="CreateQueryKey"></a>
## Criar Chave de Consulta

A operação **Criar Chave de Consulta** gera uma nova chave de consulta para o serviço de pesquisa. Você pode criar até 50 chaves de consulta por serviço.

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/createQueryKey/[name]?api-version=2014-07-31-Preview

###Solicitar parâmetros de URI###

`subscriptionId`: obrigatório. A subscriptionID do usuário do Azure. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`resourceGroupName`: obrigatório. O nome do grupo de recursos na assinatura do usuário. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`serviceName`: obrigatório. O nome do serviço de Pesquisa dentro do grupo de recursos especificado. Se não souber o nome do serviço, você poderá obter uma lista usando Listar Serviços de Pesquisa (API da Pesquisa do Azure).

`api-version`: obrigatório. Especifica a versão do protocolo usado para esta solicitação. À medida que novas versões se tornarem disponíveis, você poderá especificar qual delas usar em cada solicitação para obter comportamentos específicos da versão.

`createQueryKey`: obrigatório. Essa ação cria uma chave de consulta para o serviço de pesquisa.

`name`: obrigatório. O nome da nova chave.

###Cabeçalhos da solicitação

`x-ms-client-request-id`: opcional. Um valor de GUID gerado pelo cliente que identifica esta solicitação. Se especificado, esse valor será incluído nas informações de resposta como uma maneira de mapear a solicitação.

###Corpo da solicitação

Nenhum.

###Resposta

O código de status de resposta será HTTP 200 (OK) se a operação for bem-sucedida.

### Cabeçalhos de resposta

`Content-Type`: esse cabeçalho é sempre definido como application/json.

`x-ms-request-id`: um identificador exclusivo para a operação atual, gerado pelo serviço.

###Corpo da resposta

    {
      "name": "name of key",
      "key": "api key"
    }


###Elementos do corpo da resposta

`name`: o nome da chave de consulta.

`key`: o valor da chave de consulta.

<a name="ListQueryKey"></a>
## Listar Chaves de Consulta 


A operação **Listar Chaves de Consulta** retorna as chaves de consulta para o serviço de pesquisa especificado. As chaves de consulta são usadas para enviar chamadas à API de consulta (somente leitura) para um serviço de pesquisa. Pode haver até 50 chaves de consulta por serviço.

    GET	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listQueryKeys?api-version=2014-07-31-Preview

###Solicitar parâmetros de URI

`subscriptionId`: obrigatório. A subscriptionID do usuário do Azure. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`resourceGroupName`: obrigatório. O nome do grupo de recursos na assinatura do usuário. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`serviceName`: obrigatório. O nome do serviço de pesquisa dentro do grupo de recursos especificado. Se não souber o nome do serviço, você poderá obter uma lista usando Listar Serviços de Pesquisa (API da Pesquisa do Azure).

`api-version`: obrigatório. Especifica a versão do protocolo usado para esta solicitação. À medida que novas versões se tornarem disponíveis, você poderá especificar qual delas usar em cada solicitação para obter comportamentos específicos da versão.
	
`listQueryKeys`: obrigatório. Essa ação recupera as chaves de consulta para o serviço de pesquisa.

###Cabeçalhos da solicitação

`x-ms-client-request-id`: opcional. Um valor de GUID gerado pelo cliente que identifica esta solicitação. Se especificado, esse valor será incluído nas informações de resposta como uma maneira de mapear a solicitação.

###Corpo da solicitação

Nenhum.

###Resposta

O código de status de resposta será HTTP 200 (OK) será retornado se a operação for bem-sucedida.

### Cabeçalhos de resposta 

`Content-Type`: esse cabeçalho é sempre definido como application/json.

`x-ms-request-id`: um identificador exclusivo para a operação atual, gerado pelo serviço.

###Corpo da resposta

    {
      "value": [
    	{
          "name": "name of key 1",
          "key": "key 1"
      	},   
      	{   
          "name": "name of key 2",
          "key": "key 2"
      	}
      ],
    "nextLink": null
    }

###Elementos do corpo da resposta

`name`: o nome da chave de consulta.

`key`: o valor da chave de consulta.


<a name="DeleteQueryKey"></a>
## Excluir Chaves de Consulta 

A operação **Excluir Chave de Consulta** exclui a chave de consulta especificada. As chaves de consulta são opcionais e são usadas para consultas somente leitura.

    DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/deleteQueryKey/[key]?api-version=2014-07-31-Preview

Diferentemente das chaves de administração, as chaves de consulta não são regeneradas. O processo para regenerar uma chave de consulta consiste em excluí-la e recriá-la.

###Solicitar parâmetros de URI

`subscriptionId`: obrigatório. A subscriptionID do usuário do Azure. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`resourceGroupName`: obrigatório. O nome do grupo de recursos na assinatura do usuário. Você pode obter esse valor por meio da API do Gerenciador de Recursos do Azure ou por meio do portal.

`serviceName`: obrigatório. O nome do serviço de Pesquisa dentro do grupo de recursos especificado. Se não souber o nome do serviço, você poderá obter uma lista usando Listar Serviços de Pesquisa (API da Pesquisa do Azure).

`api-version`: obrigatório. Especifica a versão do protocolo usado para esta solicitação. À medida que novas versões se tornarem disponíveis, você poderá especificar qual delas usar em cada solicitação para obter comportamentos específicos da versão.

`deleteQueryKey`: obrigatório. Essa ação exclui uma chave de consulta existente para o serviço de pesquisa.

`key`: obrigatório. A chave a ser excluída.

###Cabeçalhos da solicitação

`x-ms-client-request-id`: opcional. Um valor de GUID gerado pelo cliente que identifica esta solicitação. Se especificado, esse valor será incluído nas informações de resposta como uma maneira de mapear a solicitação.

###Corpo da solicitação

Nenhum.

###Resposta

O código de status de resposta será HTTP 200 (OK) se tiver êxito.

### Cabeçalhos de resposta 

`Content-Type`: esse cabeçalho é sempre definido como application/json.

`x-ms-request-id`: um identificador exclusivo para a operação atual, gerado pelo serviço.

###Corpo da resposta

Nenhum.


 

<!---HONumber=Sept15_HO3-->