---
title: Controle de acesso baseado em função no Azure Cosmos DB com a integração do Active Directory do Azure
description: Saiba como o Azure Cosmos DB fornece proteção de banco de dados com a integração do Active directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: e43341e59e807fdc20033d909beadb9d582149b5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078934"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Controle de acesso baseado em função no Azure Cosmos DB

O Azure Cosmos DB fornece controle de acesso interno baseado em função (RBAC) para cenários comuns de gerenciamento no Azure Cosmos DB. Um indivíduo que tem um perfil no Azure Active Directory pode atribuir essas funções RBAC a usuários, grupos, entidades de serviço ou gerenciados identidades para conceder ou negar acesso a recursos e operações em recursos do Azure Cosmos DB. As atribuições de função estão no escopo para o plano de controle somente acesso, que inclui o acesso a contas do Cosmos do Azure, bancos de dados, contêineres e oferece (produtividade).

## <a name="built-in-roles"></a>Funções internas

Estas são as funções internas com suporte do Azure Cosmos DB:

|**Função interna**  |**Descrição**  |
|---------|---------|
|[Colaborador de contas do DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)   | Pode gerenciar contas do Azure Cosmos DB.  |
|[Leitor de conta do cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)  | Pode ler dados de contas do Azure Cosmos DB.        |
|[Operador de Backup do cosmos](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)     |  Pode enviar a solicitação de restauração para um banco de dados Cosmos do Azure ou um contêiner.       |
|[Operador do cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)  | Pode provisionar contas do Cosmos do Azure, bancos de dados e contêineres, mas não é possível acessar as chaves que são necessárias para acessar os dados.         |

> [!IMPORTANT]
> Suporte RBAC no Azure Cosmos DB se aplica a operações do plano de controle apenas. Operações do plano de dados são protegidas usando as chaves mestras ou tokens de recurso. Para obter mais informações, consulte [proteger o acesso aos dados no Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>IAM (Gerenciamento de identidade e de acesso)

O **controle de acesso (IAM)** painel no portal do Azure é usado para configurar o controle de acesso baseado em função em recursos do Azure Cosmos. As funções são aplicadas aos usuários, grupos, entidades de serviço e identidades gerenciadas no Active Directory. Você pode usar funções internas ou funções personalizadas para indivíduos e grupos. Captura de tela a seguir mostra a integração do Active Directory (RBAC) usando o controle de acesso (IAM) no portal do Azure:

![Controle de acesso (IAM) no portal do Azure – demonstrando a segurança de banco de dados](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Funções personalizadas

Além das funções internas, os usuários também podem criar [funções personalizadas](../role-based-access-control/custom-roles.md) no Azure e aplicar essas funções para entidades de serviço em todas as assinaturas em seu locatário do Active Directory. Funções personalizadas fornecem aos usuários uma maneira de criar definições de função RBAC com um conjunto personalizado de recurso de operações do provedor. Para saber quais operações estão disponíveis para a criação de funções personalizadas para o Azure Cosmos DB, consulte [operações do provedor de recursos do Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="next-steps"></a>Próximas etapas

- [O que é o controle de acesso baseado em função (RBAC) para recursos do Azure](../role-based-access-control/overview.md)
- [Funções personalizadas para recursos do Azure](../role-based-access-control/custom-roles.md)
- [Operações do provedor de recursos do Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
