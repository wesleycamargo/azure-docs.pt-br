---
title: Saiba como proteger o acesso aos dados no DocumentDB | Microsoft Docs
description: "Saiba mais sobre conceitos de controle de acesso no Banco de Dados de Documentos, incluindo chaves mestras, chaves somente leitura, usuários e permissões."
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 8641225d-e839-4ba6-a6fd-d6314ae3a51c
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: e4cbc9b0c9532d56376c4fabebcde8c64cb0474b
ms.lasthandoff: 03/24/2017


---
# <a name="securing-access-to-documentdb-data"></a>Protegendo o acesso a dados no Banco de Dados de Documentos
Este artigo fornece uma visão geral de como proteger o acesso a dados armazenados no [Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/).

O DocumentDB usa dois tipos de chaves para autenticar usuários e fornecer acesso aos dados e recursos. 

|Tipo de chave|Recursos|
|---|---|
|[Chaves mestras](#master-keys) |Usadas para os recursos administrativos: contas de bancos de dados, bancos de dados, usuários e permissões|
|[Tokens de recurso](#resource-tokens)|Usados para recursos do aplicativo: coleções, documentos, anexos, procedimentos armazenados, gatilhos e UDFs|

<a id="master-keys"></a>

## <a name="master-keys"></a>Chaves mestras 

As chaves mestras fornecem acesso a todos os recursos administrativos para a conta de banco de dados. Chaves mestras:  
- Fornecem acesso a contas, a bancos de dados, a usuários e a permissões. 
- Não podem ser usadas para fornecer acesso granular a documentos e coleções.
- São criadas durante a criação de uma conta.
- Podem ser geradas novamente a qualquer momento.

Cada conta é formada por duas Chaves mestras: uma chave primária e uma chave secundária. A finalidade das chaves duplas é para que você possa gerar novamente, ou reverter as chaves, fornecendo acesso contínuo à sua conta e dados. 

Além das duas chaves mestras para a conta do DocumentDB, há duas chaves somente leitura. Essas chaves somente leitura só permitem operações de leitura na conta. As chaves somente leitura não fornecem acesso a recursos com permissões de leitura.

As chaves mestras primária, secundária, somente leitura e de leitura-gravação podem ser recuperadas e geradas novamente usando o Portal do Azure. Para obter instruções, veja [Exibir, copiar e gerar novamente as chaves de acesso](documentdb-manage-account.md#a-idkeysaview-copy-and-regenerate-access-keys).

![Controle de acesso (IAM) no Portal do Azure - demonstrando a segurança do banco de dados NoSQL](./media/documentdb-secure-access-to-data/nosql-database-security-master-key-portal.png)

O processo de girar a chave mestra é simples. Navegue até o Portal do Azure para recuperar a chave secundária, depois substitua a chave primária pela chave secundária em seu aplicativo e gire a chave primária no Portal do Azure.

![Rotação de chave mestra no Portal do Azure – demonstrando a segurança do banco de dados NoSQL](./media/documentdb-secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Exemplo de código para uso de uma chave mestra

O exemplo de código a seguir ilustra como usar o ponto de extremidade e a chave mestra de uma conta do DocumentDB para criar uma instância de DocumentClient e criar um novo banco de dados. 

```csharp
//Read the DocumentDB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the NOSQL (DocumentDB) account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>Tokens de recurso

Os tokens de recurso fornecem acesso aos recursos do aplicativo em um banco de dados. Tokens de recurso:
- Fornecem acesso a coleções, chaves de partição, documentos, anexos, procedimentos armazenados, gatilhos e UDFs específicos.
- São criados quando um [usuário](#users) recebe [permissões](#permissions) para um recurso específico.
- São recriados quando um recurso de permissão recebe uma ação de uma chamada POST, GET ou PUT.
- Use um token de recurso de hash construído especificamente para o usuário, o recurso e a permissão.
- São associados a um período de validade personalizável. O intervalo de tempo válido padrão é de uma hora. O tempo de vida do token, no entanto, pode ser especificado explicitamente, até o máximo de cinco horas.
- Fornecem uma alternativa segura para o fornecimento da chave mestra. 
- Permitem que os cliente leiam, gravem e excluam recursos na conta do DocumentDB de acordo com as permissões que receberam.

Você pode usar um token de recurso (criando usuários e permissões do Banco de Dados de Documentos) quando quiser fornecer acesso a recursos de sua conta do Banco de Dados de Documentos a um cliente que não é confiável para receber a chave mestra.  

Os tokens de recurso do DocumentDB fornecem uma alternativa segura que permite que os clientes leiam, gravem e excluam recursos da sua conta de acordo com as permissões que você conceder, e sem a necessidade de uma chave mestra ou de somente leitura.

Este é um padrão de design típico no qual tokens de recurso podem ser solicitados, gerados e fornecidos aos clientes:

1. Um serviço de camada intermediária é configurado para atender a um aplicativo móvel de compartilhamento de fotos do usuário. 
2. O serviço de camada intermediária tem a chave mestra da conta do Banco de Dados de Documentos.
3. O aplicativo de fotos é instalado em dispositivos móveis de usuários finais. 
4. No logon, o aplicativo de fotos estabelece a identidade do usuário com o serviço de camada intermediária. Esse mecanismo de estabelecimento de identidade depende apenas do aplicativo.
5. Depois que a identidade é estabelecida, o serviço de camada intermediária solicita permissões com base na identidade.
6. O serviço de camada intermediária envia um token de recurso de volta para o aplicativo móvel.
7. O aplicativo móvel pode continuar usando o token de recurso para acessar diretamente recursos do Banco de Dados de Documentos com as permissões definidas pelo token e no intervalo permitido por ele. 
8. Quando o token de recurso expira, as solicitações seguintes recebem uma exceção 401 de não autorizado.  Nesse ponto, o aplicativo móvel restabelece a identidade e solicita um novo token de recurso.

    ![Fluxo de trabalho dos tokens de recurso do Banco de Dados de Documentos](./media/documentdb-secure-access-to-data/resourcekeyworkflow.png)

A geração e gerenciamento do token de recurso é realizada pelas bibliotecas de cliente nativas do DocumentDB; no entanto, se você usar REST deverá construir os cabeçalhos de solicitação/autenticação. Para saber mais sobre como criar cabeçalhos de autenticação para REST, veja [Controle de acesso em recursos do DocumentDB](https://docs.microsoft.com/en-us/rest/api/documentdb/access-control-on-documentdb-resources) ou o [código-fonte para nossos SDKs](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Para obter um exemplo de um serviço de camada intermediária usado para gerar, ou tokens de recurso do agente, confira o [aplicativo ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Usuários
Os usuários do DocumentDB são associados a um banco de dados do DocumentDB.  Cada banco de dados pode conter zero ou mais usuários do DocumentDB.  O exemplo de código a seguir mostra como criar um recurso de usuário do DocumentDB.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Cada usuário do DocumentDB tem uma propriedade PermissionsLink que pode ser usada para recuperar a lista de [permissões](#permissions) associadas ao usuário.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Permissões
Um recurso de permissão do Banco de Dados de Documentos é associado a um usuário do Banco de Dados de Documentos.  Cada usuário pode conter nenhuma ou mais permissões do Banco de Dados de Documentos.  Um recurso de permissão fornece acesso a um token de segurança de que o usuário precisa ao tentar acessar um recurso de aplicativo específico.
Há dois níveis de acesso disponíveis que podem ser fornecidos por um recurso de permissão:

* Tudo: o usuário tem permissão total com relação ao recurso.
* Leitura: O usuário pode apenas ler o conteúdo do recurso, mas não pode executar operações de gravação, atualização ou exclusão no recurso.

> [!NOTE]
> Para executar procedimentos armazenados no Banco de Dados de Documentos, o usuário precisa ter a permissão Tudo na coleção na qual o procedimento armazenado será executado.
> 
> 

### <a name="code-sample-to-create-permission"></a>Exemplo de código para criar permissão

O exemplo de código a seguir mostra como criar um recurso de permissão, ler o token de recurso do recurso de permissão e associar as permissões ao [usuário](#users) criado anteriormente.

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = documentCollection.SelfLink,
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Se você tiver especificado uma chave de partição para sua coleção, depois a permissão para coleta, os recursos de documento e anexo também deverão incluir o ResourcePartitionKey além do ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Exemplo de código para permissões de leitura para usuário

Para obter facilmente todos os recursos de permissão associados a um determinado usuário, o DocumentDB disponibiliza um feed de permissões para cada objeto de usuário.  O trecho de código a seguir mostra como recuperar a permissão associada ao usuário criado acima, construir uma lista de permissões e instanciar um novo DocumentClient em nome do usuário.

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a segurança de banco de dados do DocumentDB, veja [DocumentDB: segurança de banco de dados NoSQL](documentdb-nosql-database-security.md).
* Para saber mais sobre o gerenciamento de chaves mestras e chaves somente leitura, veja [Como gerenciar uma conta do DocumentDB](documentdb-manage-account.md#a-idkeysaview-copy-and-regenerate-access-keys).
* Para saber como criar tokens de autorização do DocumentDB, veja [Controle de acesso em recursos do DocumentDB](https://docs.microsoft.com/en-us/rest/api/documentdb/access-control-on-documentdb-resources).

