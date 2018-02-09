---
title: "Início Rápido: Pausar e retomar computação no SQL Data Warehouse do Azure (portal do Azure) | Microsoft Docs"
description: "Tarefas do portal do Azure que faça uma pausa de computação para um Azure SQL Data Warehouse economizar custos. Retomar computação quando você estiver pronto para usar o data warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/23/2018
ms.author: barbkess
ms.openlocfilehash: f8b4e29595c6a71696cf2c4939ad4c6c096a28b5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Início Rápido: Pausar e retomar computação para um SQL Data Warehouse do Azure no portal do Azure
Pausa de computação para um Azure SQL Data Warehouse economizar custos. Retomar computação quando você estiver pronto para usar o Data Warehouse.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entrar no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Use [Criar e conectar - portal](create-data-warehouse-portal.md) para criar um Data Warehouse chamado **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pausar computação
Para economizar custos, é possível pausar e retomar os recursos de computação sob demanda. Por exemplo, se você não usar banco de dados durante a noite e nos finais de semana, você poderá pausá-lo durante esses períodos e retomá-lo durante o dia. Você não será cobrado por recursos de computação enquanto o banco de dados estiver em pausa. No entanto, você continuará sendo cobrado pelo armazenamento. 

Siga estas etapas para pausar um SQL Data Warehouse.

1. Clique em **Bancos de dados SQL** na página esquerda do portal do Azure.
2. Selecione **mySampleDataWarehouse** da página **bancos de dados SQL**. Isso abre o Data Warehouse. 
3. Na página **mySampleDataWarehouse**, observe se **Status** indica **Online**.

    ![Computação on-line](media/pause-and-resume-compute-portal/compute-online.png)

4. Para pausar o Data Warehouse, clique no botão **Pausar**. 
5. Uma pergunta de confirmação será exibida perguntando se deseja continuar. Clique em **Sim**.
6. Aguarde alguns minutos e, em seguida, observe se **Status** indica **Pausando**.

    ![Pausando](media/pause-and-resume-compute-portal/pausing.png)

7. Quando a operação de pausa for concluída, o status indica **Pausado** e o botão de opção é **Iniciar**.
8. Os recursos de computação para o Data Warehouse agora estão offline. Você não será cobrado por computação até você reiniciar o serviço.

    ![Computação offline](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Retomar a computação
Siga estas etapas para retomar um SQL Data Warehouse.

1. Clique em **Bancos de dados SQL** na página esquerda do portal do Azure.
2. Selecione **mySampleDataWarehouse** da página **bancos de dados SQL**. Isso abre o Data Warehouse. 
3. Na página **mySampleDataWarehouse**, observe se **Status** indica **Pausado**.

    ![Computação offline](media/pause-and-resume-compute-portal/compute-offline.png)

4. Para retomar o Data Warehouse, clique em **Iniciar**. 
5. Uma pergunta de confirmação será exibida perguntando se deseja continuar. Clique em **Sim**.
6. Observe se o **Status** indica **Continuando**.

    ![Continuando](media/pause-and-resume-compute-portal/resuming.png)

7. Quando o Data Warehouse está novamente online, o status indica **Online** e o botão de opção é **Pausar**.
8. Os recursos de computação para o Data Warehouse agora estão online e você pode usar o serviço. Encargos de computação foram retomados.

    ![Computação on-line](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Limpar recursos

Você está sendo cobrado por unidades de Data Warehouse e pelos dados armazenados em seu Data Warehouse. Esses recursos de computação e armazenamento são cobrados separadamente. 

- Se você quiser manter os dados no armazenamento, pause a computação.
- Se desejar remover encargos futuros, será possível excluir o data warehouse. 

Siga estas etapas para limpar os recursos conforme desejado.

1. Faça logon no [portal do Azure](https://portal.azure.com) e clique no seu Data Warehouse.

    ![Limpar recursos](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Para pausar a computação, clique no botão **Pausar**. Quando o data warehouse for pausado, você verá um botão **Iniciar**.  Para retomar a computação, clique **Iniciar**.

2. Para remover o data warehouse para você não ser cobrado pela computação ou pelo armazenamento, clique em **Excluir**.

3. Para remover o SQL Server criado, clique em **meunovoservidor-20171113.database.windows.net** e, em seguida, clique em **Excluir**.  Tenha cuidado com essa exclusão, uma vez que a exclusão do servidor também exclui todos os bancos de dados atribuídos ao servidor.

4. Para remover o grupo de recursos, clique em **meuGrupoDeRecursos** e, em seguida, clique em **Excluir grupo de recursos**.


## <a name="next-steps"></a>Próximas etapas
Você agora pausou e retomou a computação para o Data Warehouse. Para saber mais sobre o SQL Data Warehouse do Azure, prossiga para o tutorial de carregamento de dados.

> [!div class="nextstepaction"]
>[Carregar dados no SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
