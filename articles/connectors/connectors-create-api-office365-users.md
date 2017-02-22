---
title: "Adicionar o conector Usuários do Office 365 aos Aplicativos Lógicos | Microsoft Docs"
description: "Visão geral do conector dos Usuários do Office 365 com os parâmetros da API REST"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: b2146481-9105-4f56-b4c2-7ae340cb922f
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 2e4af1c9369a97d518d027dc4679b9f01ca53d4c


---
# <a name="get-started-with-the-office-365-users-connector"></a>Introdução ao conector dos Usuários do Office 365
Conecte-se aos Usuários do Office 365 para obter perfis, pesquisar usuários e muito mais. 

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.
> 
> 

Com os Usuários do Office 365, você pode:

* Criar seu fluxo de negócios baseado nos dados obtidos dos Usuários do Office 365. 
* Usar ações que obtêm relatórios diretos, o perfil de usuário de um gerenciador e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, obtenha relatórios diretos de uma pessoa e, em seguida, utilize essas informações e atualize um banco de dados SQL Azure. 

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Gatilhos e ações
O conector dos Usuários do Office 365 tem as ações a seguir disponíveis. Não há nenhum gatilho.

| Gatilhos | Ações |
| --- | --- |
| Nenhum |<ul><li>Obter o gerenciador</li><li>Obter meu perfil</li><li>Obter relatórios diretos</li><li>Obter o perfil do usuário</li><li>Pesquisar por usuários</li></ul> |

Todos os conectores dão suporte a dados nos formatos JSON e XML. 

## <a name="create-a-connection-to-office-365-users"></a>Criar uma conexão com os Usuários do Office 365
Ao adicionar esse conector aos seus aplicativos lógicos, é necessário entrar em sua conta dos Usuários do Office 365 e permitir que os aplicativos lógicos se conectem à sua conta.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Depois de criar a conexão, insira as propriedades dos Usuários do Office 365, como a ID de usuário. A **Referência da API REST** neste tópico descreve essas propriedades.

> [!TIP]
> É possível usar essa mesma conexão dos Usuários do Office 365 em outros aplicativos lógicos.
> 
> 

## <a name="office-365-users-rest-api-reference"></a>Referência da API REST dos Usuários do Office 365
Aplica-se à versão: 1.0.

### <a name="get-my-profile"></a>Obter meu perfil
Recupera o perfil do usuário atual.  
```GET: /users/me``` 

Não existem parâmetros para esta chamada.

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 202 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 403 |Proibido |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

### <a name="get-user-profile"></a>Obter o perfil do usuário
Recupera um perfil do usuário específico.  
```GET: /users/{userId}``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| coluna |string |Sim |path |Nenhum |Nome UPN ou ID do email |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 202 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 403 |Proibido |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

### <a name="get-manager"></a>Obter o gerenciador
Recupera o perfil do usuário para o gerenciador do usuário especificado.  
```GET: /users/{userId}/manager``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| coluna |string |Sim |path |Nenhum |Nome UPN ou ID do email |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 202 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 403 |Proibido |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

### <a name="get-direct-reports"></a>Obter relatórios diretos
Obter relatórios diretos.  
```GET: /users/{userId}/directReports``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| coluna |string |Sim |path |Nenhum |Nome UPN ou ID do email |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 202 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 403 |Proibido |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

### <a name="search-for-users"></a>Pesquisar por usuários
Recupera os resultados da pesquisa dos perfis do usuário.  
```GET: /users``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| searchTerm |string |não |query |Nenhum |Pesquisar cadeia de caracteres (aplica-se a: nome de exibição, nome, sobrenome, email, apelido de email e nome UPN) |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 202 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 403 |Proibido |
| 500 |Erro interno do servidor |
| padrão |Falha na operação. |

## <a name="object-definitions"></a>Definições de objeto
#### <a name="user-user-model-class"></a>Usuário: classe de modelo de usuário
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| DisplayName |string |não |
| GivenName |string |não |
| Sobrenome |string |não |
| Email |string |não |
| MailNickname |string |não |
| TelephoneNumber |string |não |
| AccountEnabled |booleano |não |
| ID |string |sim |
| UserPrincipalName |string |não |
| Departamento |string |não |
| JobTitle |string |não |
| mobilePhone |string |não |

## <a name="next-steps"></a>Próximas etapas
[Crie um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

Volte para a [Lista de APIs](apis-list.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG



<!--HONumber=Jan17_HO3-->


