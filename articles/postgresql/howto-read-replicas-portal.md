---
title: Gerenciar réplicas de leitura para o Banco de Dados do Azure para PostgreSQL usando o portal do Azure
description: Saiba como gerenciar réplicas de leitura do Banco de Dados do Azure para PostgreSQL do portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: b34b103d3b710b90fd7b396f2c8d0e7adc27aaca
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56454660"
---
# <a name="create-and-manage-read-replicas-from-the-azure-portal"></a>Criar e gerenciar réplicas de leitura no portal do Azure

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no Banco de Dados do Azure para PostgreSQL no portal do Azure. Para saber mais sobre réplicas de leitura, confira [Visão Geral](concepts-read-replicas.md).

> [!IMPORTANT]
> O recurso de réplica de leitura está em versão prévia pública.

## <a name="prerequisites"></a>Pré-requisitos
Um [servidor do Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md) que será o servidor mestre.

## <a name="prepare-the-master-server"></a>Preparar o servidor mestre
Essas etapas devem ser usadas para preparar um servidor mestre nas camadas de uso geral ou otimizada para memória.

O parâmetro `azure.replication_support` deve ser definido como **REPLICA** no servidor mestre. Quando esse parâmetro é alterado, uma reinicialização do servidor é necessária para que a alteração entre em vigor.

1. No portal do Azure, selecione o servidor existente do Banco de Dados do Azure para PostgreSQL a ser usado como mestre.

2. No menu à esquerda, selecione **Parâmetros de Servidor**.

3. Pesquise o parâmetro `azure.replication_support`.

   ![Pesquise o parâmetro azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. Defina o valor do parâmetro `azure.replication_support` como **REPLICA**. Selecione **Salvar** para manter suas alterações.

   ![Defina o parâmetro para REPLICA e salve suas alterações](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. Depois de salvar suas alterações, você receberá uma notificação:

   ![Notificação de salvar](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. Reinicie o servidor para aplicar suas alterações. Para saber como reiniciar um servidor, confira [Reiniciar um servidor de Banco de Dados do Azure para PostgreSQL](howto-restart-server-portal.md).


## <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Para criar uma réplica de leitura, siga estas etapas:

1.  Selecione um servidor do Banco de Dados do Azure para PostgreSQL existente a ser usado como servidor mestre. 

2.  No menu de servidor, em **CONFIGURAÇÕES**, selecione **Replicação**.

   Se você não tiver definido o parâmetro `azure.replication_support` como **REPLICA** em um servidor mestre Otimizado para Memória ou de Uso Geral e reiniciado o servidor, você receberá uma notificação. Conclua essas etapas antes de criar a réplica.

3.  Selecione **para adicionar réplica**.

   ![Adicionar uma réplica](./media/howto-read-replicas-portal/add-replica.png)

4.  Insira um nome para a réplica de leitura. Selecione **OK** para confirmar a criação da réplica.

   ![Nome da réplica](./media/howto-read-replicas-portal/name-replica.png) 

Uma réplica é criada usando a mesma configuração de servidor que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: período de retenção de backup, armazenamento, vCores e geração da computação. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.

> [!IMPORTANT]
> Antes de uma configuração de servidor mestre ser atualizada com novos valores, atualize a configuração de réplica para valores iguais ou maiores. Esta ação garante que a réplica pode acompanhar as alterações feitas ao mestre.

Depois que a réplica de leitura é criada, ela pode ser exibida na janela **Replicação**:

![Exibir a nova réplica na janela Replicação](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Parar replicação
Você pode interromper a replicação entre um servidor mestre e uma réplica de leitura.

> [!IMPORTANT]
> Depois de interromper a replicação para um servidor mestre e uma réplica de leitura, isso não poderá ser desfeito. A réplica de leitura se torna um servidor autônomo que dá suporte a leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

Para interromper a replicação entre um servidor mestre e uma réplica de leitura do portal do Azure, siga estas etapas:

1.  No portal do Azure, selecione o servidor mestre do Banco de Dados do Azure para PostgreSQL.

2.  No menu de servidor, em **CONFIGURAÇÕES**, selecione **Replicação**.

3.  Selecione o servidor de réplica para o qual interromper a replicação.

   ![Selecionar a réplica](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Selecione **Parar replicação**.

   ![Selecionar interromper a replicação](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5.  Selecione **OK** para interromper a replicação.

   ![Confirme para interromper a replicação](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Excluir um servidor mestre
Para excluir um servidor mestre, você deve usar as mesmas etapas para excluir um servidor do Banco de Dados do Azure para PostgreSQL. 

> [!IMPORTANT]
> Ao excluir um servidor mestre, a replicação para todas as réplicas de leitura será interrompida. As réplicas de leitura tornam-se servidores independentes que agora têm suporte para leitura e gravação.

Para excluir um servidor do portal do Azure, siga estas etapas:

1.  No portal do Azure, selecione o servidor mestre do Banco de Dados do Azure para PostgreSQL.

2.  Abra a página **Visão geral** para o servidor. Selecione **Excluir**.

   ![Na página Visão geral do servidor, selecione para excluir o servidor mestre](./media/howto-read-replicas-portal/delete-server.png)
 
3.  Insira o nome do servidor mestre para excluir. Selecione **Excluir** para confirmar a exclusão do servidor mestre.

   ![Confirme para excluir o servidor mestre](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Excluir uma réplica
Você pode excluir uma réplica de leitura semelhante a como exclui um servidor mestre.

- No portal do Azure, abra a página **Visão geral** para a réplica de leitura. Selecione **Excluir**.

   ![Na página de Visão geral de réplica, selecione para excluir a réplica](./media/howto-read-replicas-portal/delete-replica.png)
 
Você também pode excluir a réplica de leitura usando a janela **Replicação** seguindo estas etapas:

1.  No portal do Azure, selecione o servidor mestre do Banco de Dados do Azure para PostgreSQL.

2.  No menu de servidor, em **CONFIGURAÇÕES**, selecione **Replicação**.

3.  Selecione a réplica de leitura a excluir.

   ![Selecione a réplica a excluir](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Selecione **Excluir réplica**.

   ![Selecionar excluir réplica](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5.  Insira o nome da réplica a excluir. Selecione **Excluir** para confirmar a exclusão da réplica.

   ![Confirme a exclusão da réplica](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Monitorar uma réplica
Duas métricas estão disponíveis para monitorar réplicas de leitura.

### <a name="max-lag-across-replicas-metric"></a>Métrica de Retardo Máximo Entre Réplicas
A métrica **Retardo Máximo Entre Réplicas** mostra o retardo em bytes entre o servidor mestre e a réplica com o maior retardo. 

1.  No portal do Azure, selecione o servidor mestre do Banco de Dados do Azure para PostgreSQL.

2.  Selecione **Métricas**. Na janela **Métricas**, selecione **Retardo Máximo entre Réplicas**.

    ![Monitorar o retardo máximo entre réplicas](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Para sua **Agregação**, selecione **Máx**.


### <a name="replica-lag-metric"></a>Métrica de retardo de réplica
A métrica **Retardo da Réplica** mostra o tempo decorrido desde a última transação reproduzida em uma réplica. Se não houver nenhuma transação ocorrendo no mestre, a métrica refletirá esse retardo.

1.  No portal do Azure, selecione a réplica de leitura do Banco de Dados do Azure para PostgreSQL.

2.  Selecione **Métricas**. Na janela **Métricas**, selecione **Retardo de Réplica**.

   ![Monitorar o retardo da réplica](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3.  Para sua **Agregação**, selecione **Máx**. 
 
## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [réplicas de leitura no Banco de Dados do Azure para PostgreSQL](concepts-read-replicas.md).