---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 37d7b1d44c2a4b2f3cd2fd3ac881b106d5056279
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
Agora, você pode usar a ferramenta Data Explorer no Portal do Azure para criar um banco de dados e uma tabela. 

1. Clique em **Data Explorer** > **Nova Tabela**. 
    
    A área **Adicionar Tabela** é exibida à direita, talvez seja necessário rolar para a direita para vê-la.

    ![Data Explorer no Portal do Azure](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. Na página **Adicionar Tabela**, insira as configurações da nova tabela.

    Configuração|Valor sugerido|DESCRIÇÃO
    ---|---|---
    Id da Tabela|tabela de exemplo|A ID da nova tabela. Os nomes de tabela possuem os mesmos requisitos de caractere que os ids de banco de dados. Os nomes de banco de dados devem ter entre um e 255 caracteres e não podem conter `/ \ # ?` nem espaços à direita.
    Capacidade de armazenamento| Fixo (10 GB)|Use o valor padrão de **Fixo (10 GB)**. Esse valor é a capacidade de armazenamento do banco de dados.
    Throughput|400 RUs|Altere a taxa de transferência para 400 unidades de solicitação por segundo (RU/s). Se quiser reduzir a latência, você poderá escalar verticalmente a taxa de transferência mais tarde.

    Clique em **OK**.

    O Data Explorer exibe o novo banco de dados e a tabela.

    ![O Data Explorer do Portal do Azure, mostrando o novo banco de dados e a coleção](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)