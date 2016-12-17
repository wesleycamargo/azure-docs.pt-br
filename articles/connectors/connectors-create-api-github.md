---
title: GitHub | Microsoft Docs
description: "Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O GitHub é um serviço de hospedagem do repositório Git baseado na Web. Ele oferece toda a funcionalidade de controle de revisão distribuída e de SCM (gerenciamento do código-fonte) do Git, além de adicionar seus próprios recursos."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e6468b7e9cde47ca4caf9be14d0ad780a431e6f2


---
# <a name="get-started-with-the-github-connector"></a>Introdução ao conector do GitHub
O GitHub é um serviço de hospedagem do repositório Git baseado na Web. Ele oferece toda a funcionalidade de controle de revisão distribuída e de SCM (gerenciamento do código-fonte) do Git, além de adicionar seus próprios recursos.

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. 
> 
> 

É possível começar criando um aplicativo lógico agora; consulte [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Gatilhos e ações
O conector do GitHub pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML. 

 O conector do GitHub contém as seguintes ações e/ou gatilhos disponíveis:

### <a name="github-actions"></a>Ações do GitHub
Você pode executar as seguintes ações:

| Ação | Descrição |
| --- | --- |
| [CreateIssue](connectors-create-api-github.md#createissue) |Cria um problema |

### <a name="github-triggers"></a>Gatilhos do GitHub
Você pode escutar estes eventos:

| Gatilho | Descrição |
| --- | --- |
| Quando um problema é aberto |Um problema é aberto |
| Quando um problema é fechado |Um problema é fechado |
| Quando um problema é atribuído |Um problema é atribuído |

## <a name="create-a-connection-to-github"></a>Criar uma conexão com o GitHub
Para criar Aplicativos lógicos com o GitHub, você deve primeiro criar uma **conexão**, em seguida, fornecer os detalhes para as seguintes propriedades: 

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| A criptografia do token |Sim |Fornecer as credenciais do GitHub |

Depois de criar a conexão, é possível usá-la para executar as ações e ouvir os gatilhos descritos neste artigo. 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 
> [!TIP]
> Você pode usar essa conexão em outros aplicativos lógicos.
> 
> 

## <a name="reference-for-github"></a>Referência do GitHub
Aplica-se à versão: 1.0

## <a name="createissue"></a>CreateIssue
Criar um problema: cria um problema 

```POST: /repos/{repositoryOwner}/{repositoryName}/issues``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| repositoryOwner |string |Sim |path |nenhum |Proprietário do repositório |
| repositoryName |string |Sim |path |nenhum |Nome do repositório |
| issueBasicDetails | |sim |corpo |nenhum |Detalhes do problema |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="issueopened"></a>IssueOpened
Quando um problema é aberto: um problema é aberto 

```GET: /trigger/issueOpened``` 

Não há parâmetros para essa chamada

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="issueclosed"></a>IssueClosed
Quando um problema é fechado: um problema é fechado 

```GET: /trigger/issueClosed``` 

Não há parâmetros para essa chamada

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="issueassigned"></a>IssueAssigned
Quando um problema é atribuído: um problema é atribuído 

```GET: /trigger/issueAssigned``` 

Não há parâmetros para essa chamada

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na operação. |

## <a name="object-definitions"></a>Definições de objeto
### <a name="issuebasicdetailsmodel"></a>IssueBasicDetailsModel
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| título |string |Sim |
| corpo |string |Sim |
| assignee |string |Sim |

### <a name="issuedetailsmodel"></a>IssueDetailsModel
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| título |string |Sim |
| corpo |string |Sim |
| assignee |string |Sim |
| número |string |Não |
| state |string |Não |
| created_at |string |Não |
| repository_url |string |Não |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


