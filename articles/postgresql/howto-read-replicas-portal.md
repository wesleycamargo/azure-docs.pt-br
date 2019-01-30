---
title: Gerenciar réplicas de leitura no portal do Azure para o Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve como gerenciar réplicas de leitura do Banco de Dados do Azure para PostgreSQL no portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/17/2019
ms.openlocfilehash: 6c1a0a4a13a70daec157ede98f850f87150f8d93
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54383859"
---
# <a name="how-to-create-and-manage-read-replicas-in-the-azure-portal"></a>Como criar e gerenciar réplicas de leitura no portal do Azure
Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no serviço Banco de Dados do Azure para PostgreSQL usando o portal do Azure. Para saber mais sobre réplicas de leitura, [leia a documentação de conceitos](concepts-read-replicas.md).

## <a name="prerequisites"></a>Pré-requisitos
- Um [servidor do Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md) que será o servidor mestre.

## <a name="prepare-the-master-server"></a>Preparar o servidor mestre
Essa etapa de preparação do mestre aplica-se somente aos servidores de Uso Geral e otimizados para memória.

O parâmetro **azure.replication_support** precisa ser definido como REPLICA no servidor mestre. A alteração desse parâmetro exige uma reinicialização do servidor para que entre em vigor.

1. No portal do Azure, selecione o servidor existente do Banco de Dados do Azure para PostgreSQL que você deseja usar como mestre.

2. Selecione **Parâmetros de Servidor** no menu à esquerda.

3. Pesquise **azure.replication_support**.

   ![Banco de Dados do Azure para PostgreSQL – azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. Defina **azure.replication_support** como REPLICA. **Salve** a alteração.

   ![Banco de Dados do Azure para PostgreSQL – REPLICA e Salvar](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. Depois que o salvamento for concluído, você receberá uma notificação.

   ![Banco de Dados do Azure para PostgreSQL – Notificação de salvamento](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. Reinicie o servidor para aplicar a alteração depois que ela for salva. Confira [a documentação sobre reinicialização](howto-restart-server-portal.md) para saber como reiniciar um servidor.

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura
As réplicas de leitura podem ser criadas usando as seguintes etapas:
1.  Selecione o servidor existente do Banco de Dados do Azure para PostgreSQL que deseja usar como mestre. 

2.  Selecione Replicação no menu, em CONFIGURAÇÕES.

   Se você ainda não configurou **azure.replication_support** como REPLICA no mestre de Uso Geral ou otimizado para memória nem reiniciou o servidor, você verá uma mensagem com instruções de como fazer isso. Faça isso antes de continuar com a criação.

3.  Selecione Adicionar Réplica.

   ![Banco de Dados do Azure para PostgreSQL – Adicionar réplica](./media/howto-read-replicas-portal/add-replica.png)

4.  Insira um nome para o servidor de réplica e selecione OK para confirmar a criação da réplica.

   ![Banco de Dados do Azure para PostgreSQL – Nomear réplica](./media/howto-read-replicas-portal/name-replica.png) 

> [!IMPORTANT]
> Réplicas de leitura são criadas com a mesma configuração de servidor que o mestre. Após a criação de uma réplica, o tipo de preço (exceto ao alterar do tipo Básico e para ele), a geração da computação, os vCores, o armazenamento e o período de retenção de backup podem ser alterados independentemente do servidor mestre.

> [!IMPORTANT]
> Antes da atualização da configuração do servidor de um mestre para novos valores, a configuração das réplicas deve ser atualizada para valores iguais ou superiores. A tentativa de fazer de outro modo causará um erro. Isso garante que as réplicas possam permanecer atualizadas com as alterações feitas no mestre. 


Depois que o servidor de réplica for criado, ele poderá ser exibido na janela Replicação.

![Banco de Dados do Azure para PostgreSQL – Nova réplica](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Parar replicação

> [!IMPORTANT]
> Parar a replicação para um servidor é irreversível. Depois que a replicação tiver parado entre um mestre e uma réplica, ela não poderá ser desfeita. O servidor de réplica então se torna um servidor autônomo e agora suporta tanto leitura quanto gravação. Este servidor não pode ser transformado em uma réplica novamente.

Para interromper a replicação entre um mestre e uma réplica no portal do Azure, use as seguintes etapas:
1.  No portal do Azure, selecione o servidor mestre do Banco de Dados do Azure para PostgreSQL.

2.  Selecione Replicação no menu, em CONFIGURAÇÕES.

3.  Selecione o servidor de réplica para o qual você deseja interromper a replicação.

   ![Banco de Dados do Azure para PostgreSQL – Selecionar réplica](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Selecione Parar replicação.

   ![Banco de Dados do Azure para PostgreSQL – Selecionar Parar replicação](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5.  Confirme que deseja interromper a replicação clicando em OK.

   ![Banco de Dados do Azure para PostgreSQL – Confirmar a interrupção da replicação](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master"></a>Excluir um mestre

> [!IMPORTANT]
> A exclusão de um servidor mestre interrompe a replicação para todos os servidores de réplica. Os servidores de réplica tornam-se servidores independentes que agora suportam leitura e gravação.
A exclusão de um mestre segue as mesmas etapas de um servidor autônomo do Banco de Dados do Azure para PostgreSQL. Para excluir um servidor do portal do Azure, faça o seguinte:

1.  No portal do Azure, selecione o servidor mestre do Banco de Dados do Azure para PostgreSQL.

2.  Na Visão Geral, selecione Excluir.

   ![Banco de Dados do Azure para PostgreSQL – Excluir servidor](./media/howto-read-replicas-portal/delete-server.png)
 
3.  Digite o nome do servidor mestre e selecione Excluir para confirmar a exclusão do servidor mestre.

   ![Banco de Dados do Azure para PostgreSQL – Confirmar exclusão](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Excluir uma réplica
Para excluir uma réplica de leitura, siga as mesmas etapas da exclusão de um servidor mestre acima. Primeiro, abra a página Visão Geral da réplica e, em seguida, selecione Excluir.

   ![Banco de Dados do Azure para PostgreSQL – Excluir réplica](./media/howto-read-replicas-portal/delete-replica.png)
 
Como alternativa, você pode excluí-la na janela Replicação.
1.  No portal do Azure, selecione o servidor mestre do Banco de Dados do Azure para PostgreSQL.

2.  Selecione Replicação no menu, em CONFIGURAÇÕES.

3.  Selecione o servidor de réplica que você deseja excluir. 

   ![Banco de Dados do Azure para PostgreSQL – Selecionar réplica](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Selecione Excluir réplica.

   ![Banco de Dados do Azure para PostgreSQL – Selecionar Excluir réplica](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5.  Digite o nome da réplica e selecione Excluir para confirmar a exclusão da réplica.

   ![Banco de Dados do Azure para PostgreSQL – Confirmar a exclusão da réplica](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Monitorar uma réplica
### <a name="max-lag-across-replicas"></a>Retardo Máximo entre Réplicas
A métrica **Retardo máximo entre réplicas** mostra o tempo de retardo entre o mestre e a réplica com o maior retardo. 

1.  No portal do Azure, selecione o servidor **mestre** do Banco de Dados do Azure para PostgreSQL.

2.  Selecione Métricas. Na janela de métricas, selecione **Retardo Máximo entre Réplicas**.

    ![Banco de Dados do Azure para PostgreSQL – Monitorar o retardo máximo entre réplicas](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Selecione **Máx.** como a Agregação. 

### <a name="replica-lag"></a>Retardo da Réplica
A métrica **Retardo da Réplica** mostra o tempo decorrido desde a última transação reproduzida nessa réplica. Se não houver nenhuma transação ocorrendo no mestre, a métrica refletirá esse retardo.

1.  No portal do Azure, selecione um servidor de **réplica** do Banco de Dados do Azure para PostgreSQL.

2.  Selecione Métricas. Na janela de métricas, selecione **Retardo de Réplica**.

   ![Banco de Dados do Azure para PostgreSQL – Monitorar o retardo da réplica](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3.  Selecione **Máx.** como a Agregação. 
 
## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [réplicas de leitura no Banco de Dados do Azure para PostgreSQL](concepts-read-replicas.md).