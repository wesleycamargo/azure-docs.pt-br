---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/19/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: c3cbfda674abaeea1adf35c3ee0d2b5ddf6b2f84
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52853180"
---
Agora, você pode usar a ferramenta Data Explorer no Portal do Azure para criar um banco de dados e uma coleção. 

1. Clique em **Data Explorer** > **Nova Coleção**. 
    
    A área **Adicionar Coleção** é exibida à direita, talvez seja necessário rolar para a direita para vê-la.

    ![O Data Explorer do Portal do Azure, folha Adicionar Coleção](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. Na página **Adicionar coleção**, insira as configurações da nova coleção.

    Configuração|Valor sugerido|DESCRIÇÃO
    ---|---|---
    ID do banco de dados|*Tarefas*|Digite *Tarefas* como o nome do novo banco de dados. Os nomes de banco de dados devem conter de 1 a 255 caracteres e não podem conter /, \\, #, ?, ou um espaço à direita.
    ID da coleção|*Itens*|Insira *Itens* como o nome da nova coleção. As IDs de coleção têm os mesmos requisitos de caracteres que os nomes de banco de dados.
    Aprovisionar a taxa de transferência de bancos de dados|Deixar em branco|O Azure Cosmos DB pode provisionar a taxa de transferência, tanto no nível de banco de dados (todas as coleções em um banco de dados compartilham a mesma taxa de transferência) como no nível de coleção. Deixe em branco para provisionar a taxa de transferência no nível de coleção para esta coleção específica.
    Capacidade de armazenamento|*Ilimitado*|Escolha a capacidade de armazenamento **Ilimitada**. 
    Chave de partição|*/category*|Digite "/category" como a chave de partição. A definição de uma chave de partição permite que o Azure Cosmos DB dimensione sua coleção para atender às necessidades de armazenamento e taxa de transferência do seu aplicativo. Em geral, uma boa opção de chave de partição é uma que tenha uma ampla variedade de valores distintos e resulte em uma distribuição uniforme do armazenamento e do volume de solicitação em sua carga de trabalho. [Saiba mais sobre como particionar.](../articles/cosmos-db/partitioning-overview.md)
    Produtividade|*400 RU/s*|Altere a taxa de transferência para 400 unidades de solicitação por segundo (RU/s). Se quiser reduzir a latência, você poderá escalar verticalmente a taxa de transferência mais tarde. 
    
    Além das configurações anteriores, você pode opcionalmente adicionar **Chaves exclusivas** à coleção. Vamos deixar o campo vazio neste exemplo. As chaves exclusivas oferecem aos desenvolvedores a capacidade de adicionar uma camada de integridade dos dados ao seu banco de dados. Ao criar uma política de chave exclusiva durante a criação de uma coleção, você garante a exclusividade de um ou mais valores por chave de partição. Para obter mais informações, consulte o artigo [Chaves exclusivas no Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Clique em **OK**.

    O Data Explorer exibe o novo banco de dados e a coleção.

    ![O Data Explorer do Portal do Azure, mostrando o novo banco de dados e a coleção](./media/cosmos-db-create-collection/azure-cosmos-db-data-explorer-preview.png)