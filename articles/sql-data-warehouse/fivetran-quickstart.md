---
title: Início rápido do Fivetran com o Azure SQL Data Warehouse | Microsoft Docs
description: Comece rapidamente com Fivetran e Azure SQL Data Warehouse.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 8e738becfe356908af5baffc0ebf225916b2616e
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49355135"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Comece rapidamente com Fivetran e Azure SQL Data Warehouse

Este início rápido pressupõe que você já tem uma instância já existente do SQL Data Warehouse.

## <a name="setup-connection"></a>Configurar conexão

1. Encontre seu nome totalmente qualificado do servidor e o nome do banco de dados para se conectar ao Azure SQL Data Warehouse.

   [Como localizar o nome do servidor e o nome do banco de dados no portal?](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connect-overview)

2. No Assistente de instalação, decida se deseja se conectar a seu banco de dados diretamente ou por meio de um túnel SSH.

   Se você decidir se conectar diretamente ao seu banco de dados, você precisará criar uma regra de firewall para permitir o acesso. Esse método é o método mais simples e seguro.

   Se você decidir se conectar por meio de um túnel SSH, Fivetran se conectará a um servidor separado em sua rede que fornece um túnel SSH para seu banco de dados. Esse método é necessário se seu banco de dados estiver em uma sub-rede inacessível em uma rede virtual.

3. Adicione o endereço IP "52.0.2.4" em seu firewall de nível de servidor para permitir conexões de entrada para o Azure SQL Data Warehouse de Fivetran.

   [Como adicionar um firewall de nível de servidor?](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal#create-a-server-level-firewall-rule)

## <a name="setup-user-credentials"></a>Configuração de credenciais do usuário

Conectar ao seu SQL Data Warehouse do Azure usando o SQL Server Management Studio ou a ferramenta de escolha como usuário administrador do servidor e executar os seguintes comandos SQL para criar um usuário para Fivetran:

No banco de dados mestre: ` CREATE LOGIN fivetran WITH PASSWORD = '<password>'; `

No banco de dados do SQL Data Warehouse:

```
CREATE USER fivetran_user_without_login without login;
CREATE USER fivetran FOR LOGIN fivetran;
GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
```

Depois de criar usuário fivetran, conceda as seguintes permissões para o warehouse:

```
GRANT CONTROL to fivetran;
```

Adicione uma classe de recurso adequado para o usuário criado, dependendo do requisito de memória para criação de índice columnstore. Por exemplo, integrações, como Marketo e o Salesforce precisam de uma a classe de recurso maior devido ao número grande colunas / volume maior de dados, que requer mais memória para criar índice columnstore.

É recomendável usar classes de recursos estáticos. Você pode iniciar com a classe de recurso `staticrc20`, que aloca a 200 MB para o usuário, independentemente de você usar o nível de desempenho. Se a indexação de columnstore falhar com a classe de recurso atual, temos que aumentar a classe de recurso.

```
EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
```

Para obter mais informações, confira os documentos quanto a [limites de memória e simultaneidade](https://docs.microsoft.com/azure/sql-data-warehouse/memory-and-concurrency-limits#data-warehouse-limits) e [classes de recursos](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression#ways-to-allocate-more-memory)

Permissão CONTROL é necessária para criar as credenciais no escopo do banco de dados que serão usadas durante o carregamento de arquivos do armazenamento de BLOBs usando o PolyBase.

Insira as credenciais para acessar o Azure SQL Data Warehouse

1. Host (o nome do servidor)
2. Porta
3. Banco de dados
4. Usuário (nome de usuário deve ser `fivetran@<server_name>` onde `<server_name>` faz parte de seu uri de host do azure: `<server_name>.database.windows.net`)
5. Senha