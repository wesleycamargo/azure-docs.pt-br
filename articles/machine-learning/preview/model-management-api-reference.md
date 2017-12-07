---
title: Criar uma imagem do Docker para Gerenciamento de Modelos no Azure Machine Learning | Microsoft Docs
description: "Este artigo descreve as etapas para criar uma imagem do Docker para o serviço Web."
services: machine-learning
author: chhavib
ms.author: chhavib
manager: neerajkh
editor: jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 03e51ab298a08386f0094d6d0290aa1ec85d337f
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2017
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Referência da API da conta do Gerenciamento de Modelos do Azure Machine Learning

Para obter informações sobre a configuração do ambiente de implantação, consulte [Configuração da conta do Gerenciamento de Modelos](deployment-setup-configuration.md).

A API da conta do Gerenciamento de Modelos do Azure Machine Learning implementa as seguintes operações:

- Registro do modelo
- Criação do manifesto
- Criação da imagem do Docker
- Criação do serviço Web

Você pode usar essa imagem para criar um serviço Web localmente, em um cluster do Serviço de Contêiner do Azure remoto ou em outro ambiente do Docker com suporte de sua escolha.

## <a name="prerequisites"></a>Pré-requisitos
Verifique se você executou as etapas de instalação no documento [Guia de início rápido para instalação e criação](quickstart-installation.md).

Os seguintes itens são necessários antes de continuar:
1. Provisionamento da conta do Gerenciamento de Modelos
2. Criação do ambiente para implantar e gerenciar de modelos
3. Um modelo de Machine Learning

### <a name="azure-ad-token"></a>Token do Azure AD
Quando você estiver usando a CLI do Azure, faça logon usando `az login`. A CLI usa seu token do Azure AD (Azure Active Directory) do arquivo .azure. Se você quiser usar as APIs, haverá as seguintes opções.

##### <a name="acquire-the-azure-ad-token-manually"></a>Adquirir o token do Azure AD manualmente
Você pode usar `az login` e obter o token mais recente do arquivo .azure no diretório base.

##### <a name="acquire-the-azure-ad-token-programmatically"></a>Adquirir o token do Azure AD programaticamente
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
Depois de criar a entidade de serviço, salve a saída. Agora você pode usá-la para obter o token do Azure AD:

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
O token é colocado no cabeçalho de autorização para chamadas à API.


## <a name="register-a-model"></a>Registrar um modelo

A etapa de registro de modelo registra o modelo do Machine Learning na conta do Gerenciamento de Modelos do Azure que você criou. Esse registro permite acompanhar os modelos e suas versões que são atribuídos no momento do registro. O usuário fornece o nome do modelo. Os próximos registros de modelos com o mesmo nome geram uma nova versão e uma nova ID.

### <a name="request"></a>Solicitação

| Método | URI da solicitação |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Descrição
Registra um modelo.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |
| model | body | Payload usado para registrar um modelo. | Sim | [Modelo](#model) |


### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | OK. O registro do modelo foi bem-sucedido. | [Modelo](#model) |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Consultar a lista de modelos em uma conta
### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Descrição
Consulta a lista de modelos em uma conta. Você pode filtrar a lista de resultados por marca e nome. Se nenhum filtro for passado, a consulta listará todos os modelos na conta. A lista retornada é paginada e a contagem dos itens em cada página é um parâmetro opcional.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |
| name | query | Nome do objeto. | Não | string |
| marca | query | Marca do modelo. | Não | string |
| count | query | Número de itens a serem recuperados em uma página. | Não | string |
| $skipToken | query | Token de continuação para recuperar a próxima página. | Não | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Sucesso. | [PaginatedModelList](#paginatedmodellist) |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Obter detalhes do modelo
### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{id}  
 |

### <a name="description"></a>Descrição
Obtém o modelo por ID.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| ID | path | ID do objeto. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Sucesso. | [Modelo](#model) |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>Registrar um manifesto com o modelo registrado e todas as dependências

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Descrição
Registra um manifesto com o modelo registrado e todas as dependências.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |
| manifestRequest | body | Payload usado para registrar um manifesto. | Sim | [Manifesto](#manifest) |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | O registro do manifesto foi bem-sucedido. | [Manifesto](#manifest) |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>Consultar a lista de manifestos em uma conta

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Descrição
Consulta a lista de manifestos em uma conta. Você pode filtrar a lista de resultados pela ID do modelo e pelo nome do manifesto. Se nenhum filtro for passado, a consulta listará todos os manifestos na conta. A lista retornada é paginada e a contagem dos itens em cada página é um parâmetro opcional.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |
| modelId | query | ID do modelo. | Não | string |
| manifestName | query | Nome do manifesto. | Não | string |
| count | query | Número de itens a serem recuperados em uma página. | Não | string |
| $skipToken | query | Token de continuação para recuperar a próxima página. | Não | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Sucesso. | [PaginatedManifestList](#paginatedmanifestlist) |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Obter detalhes do manifesto

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{id} | 

### <a name="description"></a>Descrição
Obtém o manifesto por ID.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| ID | path | ID do objeto. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Sucesso. | [Manifesto](#manifest) |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Criar uma imagem

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Descrição
Cria uma imagem como uma imagem de Docker no Registro de Contêiner do Azure.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |
| imageRequest | body | Payload usado para criar uma imagem. | Sim | [ImageRequest](#imagerequest) |

### <a name="responses"></a>Respostas
| Código | Descrição | Cabeçalhos | Esquema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL da localização da operação assíncrona. Uma chamada GET mostrará o status da tarefa de criação de imagem. | Operation-Location |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Consultar a lista de imagens em uma conta

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Descrição
Consulta a lista de imagens em uma conta. Você pode filtrar a lista de resultados pela ID e pelo nome do manifesto. Se nenhum filtro for passado, a consulta listará todas as imagens na conta. A lista retornada é paginada e a contagem dos itens em cada página é um parâmetro opcional.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |
| manifestId | query | ID do manifesto. | Não | string |
| manifestName | query | Nome do manifesto. | Não | string |
| count | query | Número de itens a serem recuperados em uma página. | Não | string |
| $skipToken | query | Token de continuação para recuperar a próxima página. | Não | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Sucesso. | [PaginatedImageList](#paginatedimagelist) |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>Obter os detalhes da imagem

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{id} | 

### <a name="description"></a>Descrição
Obtém uma imagem por ID.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| ID | path | ID da imagem. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Sucesso. | [Imagem](#image) |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Criar um serviço

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Descrição
Cria um serviço usando uma imagem.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |
| serviceRequest | body | Payload usado para criar um serviço. | Sim | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Respostas
| Código | Descrição | Cabeçalhos | Esquema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL da localização da operação assíncrona. Uma chamada GET mostrará o status da tarefa de criação de serviço. | Operation-Location |
| 409 | Já existe um serviço com o nome especificado. |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Consultar a lista de serviços em uma conta

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Descrição
Consulta a lista de serviços em uma conta. Você pode filtrar a lista de resultados por nome/ID do modelo, nome/ID do manifesto, ID da imagem, nome do serviço ou ID do recurso de computação do Machine Learning. Se nenhum filtro for passado, a consulta listará todos os serviços na conta. A lista retornada é paginada e a contagem dos itens em cada página é um parâmetro opcional.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |
| serviceName | query | Nome do serviço. | Não | string |
| modelId | query | Nome do modelo. | Não | string |
| modelName | query | ID do modelo. | Não | string |
| manifestId | query | ID do manifesto. | Não | string |
| manifestName | query | Nome do manifesto. | Não | string |
| imageId | query | ID da imagem. | Não | string |
| computeResourceId | query | ID do recurso de computação do Machine Learning. | Não | string |
| count | query | Número de itens a serem recuperados em uma página. | Não | string |
| $skipToken | query | Token de continuação para recuperar a próxima página. | Não | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Sucesso. | [PaginatedServiceList](#paginatedservicelist) |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Obter detalhes do serviço

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Descrição
Obtém o serviço por ID.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| ID | path | ID do objeto. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Sucesso. | [ServiceResponse](#serviceresponse) |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Atualizar um serviço

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| PUT |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Descrição
Atualiza um serviço existente.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| ID | path | ID do objeto. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |
| serviceUpdateRequest | body | Payload usado para atualizar um serviço existente. | Sim |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Respostas
| Código | Descrição | Cabeçalhos | Esquema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL da localização da operação assíncrona. Uma chamada GET mostrará o status da tarefa do serviço de atualização. | Operation-Location |
| 404 | O serviço com a ID especificada não existe. |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>Excluir um serviço

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| EXCLUIR |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Descrição
Exclui um serviço.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| ID | path | ID do objeto. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Sucesso. |  |
| 204 | O serviço com a ID especificada não existe. |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>Obter chaves de serviço

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Descrição
Obtém as chaves de serviço.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| ID | path | ID do serviço. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Sucesso. | [AuthKeys](#authkeys)
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Gerar novamente as chaves de serviço

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Descrição
Gera novamente as chaves de serviço e as retorna.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| ID | path | ID do serviço. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |
| regenerateKeyRequest | body | Payload usado para atualizar um serviço existente. | Sim | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Sucesso. | [AuthKeys](#authkeys)
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>Consultar a lista de implantações em uma conta

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments | 

### <a name="description"></a>Descrição
Consulta a lista de implantações em uma conta. Você pode filtrar a lista de resultados por ID de serviço, que retornará somente as implantações criadas para o serviço específico. Se nenhum filtro for passado, a consulta listará todas as implantações na conta.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |
| serviceId | query | ID do serviço. | Não | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Sucesso. | [DeploymentList](#deploymentlist) |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Obter detalhes de implantação

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments/{id} | 

### <a name="description"></a>Descrição
Obtém a implantação por ID.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| ID | path | ID de implantação. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Sucesso. | [Implantação](#deployment) |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>Obter detalhes da operação

### <a name="request"></a>Solicitação
| Método | URI da solicitação |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/operations/{id} | 

### <a name="description"></a>Descrição
Obtém o status da operação assíncrona por ID da operação.

### <a name="parameters"></a>parâmetros
| Nome | Localizado em | Descrição | Obrigatório | Esquema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | Id de assinatura do Azure. | Sim | string |
| resourceGroupName | path | Nome do grupo de recursos no qual a conta do Gerenciamento de Modelos está localizada. | Sim | string |
| accountName | path | Nome da conta do Gerenciamento de Modelos. | Sim | string |
| ID | path | ID da operação. | Sim | string |
| api-version | query | Versão da API do provedor de recursos do Microsoft.Machine.Learning a ser usada. | Sim | string |
| Autorização | cabeçalho | Token de autorização. Deve ser algo como "Portador XXXXXX". | Sim | string |

### <a name="responses"></a>Respostas
| Código | Descrição | Esquema |
|--------------------|--------------------|--------------------|
| 200 | Sucesso. | [OperationStatus](#asyncoperationstatus) |
| padrão | Resposta de erro que descreve por que a operação falhou. | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Definições

<a name="asset"></a>
### <a name="asset"></a>Ativo
O objeto de ativo que será necessário durante a criação da imagem do Docker.


|Nome|Descrição|Esquema|
|---|---|---|
|**ID**  <br>*opcional*|ID do ativo.|string|
|**mimeType**  <br>*opcional*|O tipo MIME do conteúdo do modelo. Para obter mais informações sobre o tipo MIME, consulte a [lista de tipos de mídia IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|string|
|**unpack**  <br>*opcional*|Indica onde precisamos desempacotar o conteúdo durante a criação da imagem do Docker.|Booliano|
|**url**  <br>*opcional*|URL de localização do ativo.|string|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
O estado da operação assíncrona.

*Tipo*: enum (NotStarted, Em execução, Cancelado, Com êxito, Com falha)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
O status da operação.


|Nome|Descrição|Esquema|
|---|---|---|
|**createdTime**  <br>*opcional*  <br>*read-only*|Hora de criação de operação assíncrona (UTC).|cadeia de caracteres (data e hora)|
|**endTime**  <br>*opcional*  <br>*read-only*|Hora de término da operação assíncrona (UTC).|cadeia de caracteres (data e hora)|
|**error**  <br>*opcional*||[ErrorResponse](#errorresponse)|
|**ID**  <br>*opcional*|ID da operação assíncrona.|string|
|**operationType**  <br>*opcional*|Tipo de operação assíncrona.|Enum (imagem, serviço)|
|**resourceLocation**  <br>*opcional*|Recurso criado ou atualizado pela operação assíncrona.|string|
|**state**  <br>*opcional*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
As chaves de autenticação de um serviço.


|Nome|Descrição|Esquema|
|---|---|---|
|**primaryKey**  <br>*opcional*|Chave primária.|string|
|**secondaryKey**  <br>*opcional*|Chave secundária.|string|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
Configurações do autoscaler.


|Nome|Descrição|Esquema|
|---|---|---|
|**autoscaleEnabled**  <br>*opcional*|Habilitar ou desabilitar o autoscaler.|Booliano|
|**maxReplicas**  <br>*opcional*|O número máximo de réplicas de pod até o qual aumentar.  <br>**Valor mínimo**: `1`|inteiro|
|**minReplicas**  <br>*opcional*|O número mínimo de réplicas de pod até o qual reduzir verticalmente.  <br>**Valor mínimo**: `0`|inteiro|
|**refreshPeriodInSeconds**  <br>*opcional*|Atualizar o tempo para o gatilho de dimensionamento automático.  <br>**Valor mínimo**: `1`|inteiro|
|**targetUtilization**  <br>*opcional*|Percentual de utilização que dispara o dimensionamento automático.  <br>**Valor mínimo**: `0`  <br>**Valor máximo**: `100`|inteiro|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
O recurso de computação do Machine Learning.


|Nome|Descrição|Esquema|
|---|---|---|
|**ID**  <br>*opcional*|ID do recurso.|string|
|**tipo**  <br>*opcional*|Tipo de recurso.|enum (cluster)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
Configuração para reservar recursos para o contêiner no cluster.


|Nome|Descrição|Esquema|
|---|---|---|
|**cpu**  <br>*opcional*|Especifica a reserva de CPU. Formato para Kubernetes: consulte [Meaning of CPU](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu) (Significado de CPU).|string|
|**memory**  <br>*opcional*|Especifica a reserva de memória. Formato para Kubernetes: consulte [Meaning of memory](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory) (Significado de memória).|string|


<a name="deployment"></a>
### <a name="deployment"></a>Implantação
Uma instância de uma implantação do Azure Machine Learning.


|Nome|Descrição|Esquema|
|---|---|---|
|**createdAt**  <br>*opcional*  <br>*read-only*|Hora de criação de implantação (UTC).|cadeia de caracteres (data e hora)|
|**expiredAt**  <br>*opcional*  <br>*read-only*|Hora de expiração da implantação (UTC).|cadeia de caracteres (data e hora)|
|**ID**  <br>*opcional*|ID de implantação.|string|
|**imageId**  <br>*opcional*|ID da imagem associada a essa implantação.|string|
|**serviceName**  <br>*opcional*|Nome do serviço.|string|
|**status**  <br>*opcional*|Status de implantação atual.|string|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
Uma matriz de objetos de implantação.

*Tipo*: matriz <[Deployment](#deployment)>


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Detalhe do erro do serviço de Gerenciamento de Modelos.


|Nome|Descrição|Esquema|
|---|---|---|
|**code**  <br>*obrigatório*|Código do erro.|string|
|**message**  <br>*obrigatório*|Mensagem de erro.|string|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
Um objeto de erro do serviço de Gerenciamento de Modelos.


|Nome|Descrição|Esquema|
|---|---|---|
|**code**  <br>*obrigatório*|Código do erro.|string|
|**details**  <br>*opcional*|Matriz de objetos de detalhe do erro.|Matriz <[ErrorDetail](#errordetail)>|
|**message**  <br>*obrigatório*|Mensagem de erro.|string|
|**statusCode**  <br>*opcional*|Código de status HTTP.|inteiro|


<a name="image"></a>
### <a name="image"></a>Imagem
A imagem do Azure Machine Learning.


|Nome|Descrição|Esquema|
|---|---|---|
|**computeResourceId**  <br>*opcional*|ID do ambiente criado no recurso de computação de Machine Learning.|string|
|**createdTime**  <br>*opcional*|Hora de criação da imagem (UTC).|cadeia de caracteres (data e hora)|
|**creationState**  <br>*opcional*||[AsyncOperationState](#asyncoperationstate)|
|**description**  <br>*opcional*|Texto de descrição da imagem.|string|
|**error**  <br>*opcional*||[ErrorResponse](#errorresponse)|
|**ID**  <br>*opcional*|ID da imagem.|string|
|**imageBuildLogUri**  <br>*opcional*|URI dos logs carregados do build da imagem.|string|
|**imageLocation**  <br>*opcional*|Cadeia de caracteres da localização do Registro de Contêiner do Azure da imagem criada.|string|
|**imageType**  <br>*opcional*||[ImageType](#imagetype)|
|**manifest**  <br>*opcional*||[Manifesto](#manifest)|
|**name**  <br>*opcional*|Nome da imagem.|string|
|**version**  <br>*opcional*|Versão da imagem definida pelo serviço de Gerenciamento de Modelos.|inteiro|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>ImageRequest
Uma solicitação para criar uma imagem do Azure Machine Learning.


|Nome|Descrição|Esquema|
|---|---|---|
|**computeResourceId**  <br>*obrigatório*|ID do ambiente criado no recurso de computação de Machine Learning.|string|
|**description**  <br>*opcional*|Texto de descrição da imagem.|string|
|**imageType**  <br>*obrigatório*||[ImageType](#imagetype)|
|**manifestId**  <br>*obrigatório*|ID do manifesto do qual a imagem será criada.|string|
|**name**  <br>*obrigatório*|Nome da imagem.|string|


<a name="imagetype"></a>
### <a name="imagetype"></a>ImageType
Especifica o tipo da imagem.

*Tipo*: enum (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Manifesto
O manifesto do Azure Machine Learning.


|Nome|Descrição|Esquema|
|---|---|---|
|**assets**  <br>*obrigatório*|Lista de ativos.|Matriz <[Asset](#asset)>|
|**createdTime**  <br>*opcional*  <br>*read-only*|Hora de criação do manifesto (UTC).|cadeia de caracteres (data e hora)|
|**description**  <br>*opcional*|Texto de descrição do manifesto.|string|
|**driverProgram**  <br>*obrigatório*|Programa de driver do manifesto.|string|
|**ID**  <br>*opcional*|ID do manifesto.|string|
|**modelIds**  <br>*opcional*|Lista de IDs dos modelos registrados. A solicitação falhará se um dos modelos incluídos não estiver registrado.|Matriz <string>|
|**modelType**  <br>*opcional*|Especifica que os modelos já estão registrados com o serviço de Gerenciamento de Modelos.|Enum (registrado)|
|**name**  <br>*obrigatório*|Nome do manifesto.|string|
|**targetRuntime**  <br>*obrigatório*||[TargetRuntime](#targetruntime)|
|**version**  <br>*opcional*  <br>*read-only*|Versão do manifesto atribuída pelo serviço de Gerenciamento de Modelos.|inteiro|
|**webserviceType**  <br>*opcional*|Especifica o tipo desejado de serviço Web que será criado usando o manifesto.|Enum (em tempo real)|


<a name="model"></a>
### <a name="model"></a>Modelo
Uma instância de uma modelo do Azure Machine Learning.


|Nome|Descrição|Esquema|
|---|---|---|
|**createdAt**  <br>*opcional*  <br>*read-only*|Hora de criação do modelo (UTC).|cadeia de caracteres (data e hora)|
|**description**  <br>*opcional*|Texto de descrição do modelo.|string|
|**ID**  <br>*opcional*  <br>*read-only*|ID do modelo.|string|
|**mimeType**  <br>*obrigatório*|Tipo MIME do conteúdo do modelo. Para obter mais informações sobre o tipo MIME, consulte a [lista de tipos de mídia IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|string|
|**name**  <br>*obrigatório*|Nome do modelo.|string|
|**marcas**  <br>*opcional*|Lista de marcas do modelo.|Matriz <string>|
|**unpack**  <br>*opcional*|Indica se é necessário descompactar o modelo durante a criação da imagem do Docker.|Booliano|
|**url**  <br>*obrigatório*|URL do modelo. Geralmente, colocamos uma URL de assinatura de acesso compartilhado aqui.|string|
|**version**  <br>*opcional*  <br>*read-only*|Versão do modelo atribuída pelo serviço de Gerenciamento de Modelos.|inteiro|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
As informações de coleta de dados do modelo.


|Nome|Descrição|Esquema|
|---|---|---|
|**eventHubEnabled**  <br>*opcional*|Habilitar o hub de eventos para um serviço.|Booliano|
|**storageEnabled**  <br>*opcional*|Habilitar o armazenamento para um serviço.|Booliano|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
Uma lista paginada de imagens.


|Nome|Descrição|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Link de continuação (URI absoluto) para a próxima página de resultados na lista.|string|
|**valor**  <br>*opcional*|Matriz de objetos de modelo.|Matriz <[Image](#image)>|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
Uma lista paginada de manifestos.


|Nome|Descrição|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Link de continuação (URI absoluto) para a próxima página de resultados na lista.|string|
|**valor**  <br>*opcional*|Matriz de objetos de manifesto.|Matriz <[Manifest](#manifest)>|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
Uma lista paginada de modelos.


|Nome|Descrição|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Link de continuação (URI absoluto) para a próxima página de resultados na lista.|string|
|**valor**  <br>*opcional*|Matriz de objetos de modelo.|Matriz <[Modelo](#model)>|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
Uma lista paginada de serviços.


|Nome|Descrição|Esquema|
|---|---|---|
|**nextLink**  <br>*opcional*|Link de continuação (URI absoluto) para a próxima página de resultados na lista.|string|
|**valor**  <br>*opcional*|Matriz de objetos de serviço.|Matriz <[ServiceResponse](#serviceresponse)>|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
Uma solicitação para criar um serviço.


|Nome|Descrição|Esquema|
|---|---|---|
|**appInsightsEnabled**  <br>*opcional*|Habilitar o Application Insights para um serviço.|Booliano|
|**autoScaler**  <br>*opcional*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*obrigatório*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*opcional*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*opcional*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*obrigatório*|Imagem para criar o serviço.|string|
|**maxConcurrentRequestsPerContainer**  <br>*opcional*|Número máximo de solicitações simultâneas.  <br>**Valor mínimo**: `1`|inteiro|
|**name**  <br>*obrigatório*|Nome do serviço.|string|
|**numReplicas**  <br>*opcional*|Número de réplicas de pod em execução a qualquer momento. Não é possível especificar quando Autoscaler está habilitado.  <br>**Valor mínimo**: `0`|inteiro|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
Uma solicitação para gerar novamente a chave de um serviço.


|Nome|Descrição|Esquema|
|---|---|---|
|**keyType**  <br>*opcional*|Especifica qual chave deve ser regenerada.|enum (primária, secundária)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
O status detalhado do serviço.


|Nome|Descrição|Esquema|
|---|---|---|
|**createdAt**  <br>*opcional*|Hora de criação do serviço (UTC).|cadeia de caracteres (data e hora)|
|**ID**  <br>*opcional*|ID do serviço.|string|
|**image**  <br>*opcional*||[Imagem](#image)|
|**manifest**  <br>*opcional*||[Manifesto](#manifest)|
|**models**  <br>*opcional*|Lista de modelos.|Matriz <[Modelo](#model)>|
|**name**  <br>*opcional*|Nome do serviço.|string|
|**scoringUri**  <br>*opcional*|URI para pontuação do serviço.|string|
|**state**  <br>*opcional*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*opcional*|Hora da última atualização (UTC).|cadeia de caracteres (data e hora)|
|**appInsightsEnabled**  <br>*opcional*|Habilitar o Application Insights para um serviço.|Booliano|
|**autoScaler**  <br>*opcional*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*obrigatório*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*opcional*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*opcional*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*opcional*|Número máximo de solicitações simultâneas.  <br>**Valor mínimo**: `1`|inteiro|
|**numReplicas**  <br>*opcional*|Número de réplicas de pod em execução a qualquer momento. Não é possível especificar quando Autoscaler está habilitado.  <br>**Valor mínimo**: `0`|inteiro|
|**error**  <br>*opcional*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
Uma solicitação para atualizar um serviço.


|Nome|Descrição|Esquema|
|---|---|---|
|**appInsightsEnabled**  <br>*opcional*|Habilitar o Application Insights para um serviço.|Booliano|
|**autoScaler**  <br>*opcional*||[AutoScaler](#autoscaler)|
|**containerResourceReservation**  <br>*opcional*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*opcional*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*opcional*|Imagem para criar o serviço.|string|
|**maxConcurrentRequestsPerContainer**  <br>*opcional*|Número máximo de solicitações simultâneas.  <br>**Valor mínimo**: `1`|inteiro|
|**numReplicas**  <br>*opcional*|Número de réplicas de pod em execução a qualquer momento. Não é possível especificar quando Autoscaler está habilitado.  <br>**Valor mínimo**: `0`|inteiro|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
O tipo do tempo de execução de destino.


|Nome|Descrição|Esquema|
|---|---|---|
|**properties**  <br>*obrigatório*||mapa <string, string>|
|**runtimeType**  <br>*obrigatório*|Especifica o tempo de execução.|enum (SparkPython, Python)|

