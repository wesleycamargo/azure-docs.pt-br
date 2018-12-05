---
title: Reiniciar o Banco de Dados do Azure para PostgreSQL usando o portal do Azure
description: Este artigo descreve como restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o portal do Azure.
services: postgresql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: postgresql
ms.topic: article
ms.date: 11/16/2018
ms.openlocfilehash: 9a60f46f71a3d8e6f6d218c9a0ebd3194b6ab39f
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52166656"
---
# <a name="restart-azure-database-for-postgresql-server-using-azure-portal"></a>Reiniciar o Banco de Dados do Azure para PostgreSQL usando o portal do Azure
Este tópico descreve como você pode reiniciar um servidor do Banco de Dados do Azure para PostgreSQL. Você talvez precise reiniciar o servidor por razões de manutenção, o que causa uma breve interrupção, conforme o servidor executa a operação.

A reinicialização do servidor será bloqueada se o serviço estiver ocupado. Por exemplo, o serviço pode estar processando uma operação solicitada anteriormente como o dimensionamento vCores.
 
O tempo necessário para concluir uma reinicialização depende do processo de recuperação do PostgreSQL. Para diminuir o tempo de reinicialização, é recomendável que você minimize a quantidade de atividade que ocorre no servidor antes da reinicialização.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa:
- Um [Banco de Dados do Azure para servidor e banco de dados PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Realizar a reinicialização do servidor

As etapas a seguir reiniciam o servidor PostgreSQL:

1. No Portal do Azure, selecione o servidor do Banco de Dados do Azure para PostgreSQL.

2. Na barra de ferramentas da página de **Visão Geral** do servidor, clique em **Restaurar**.

   ![Banco de Dados do Azure para PostgreSQL - Visão geral - botão Reiniciar](./media/howto-restart-server-portal/2-server.png)

3. Clique em **Sim** para confirmar a reinicialização do servidor.

   ![Banco de Dados do Azure para PostgreSQL - Restaurar confirmação ](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observe que o status do servidor muda para "Reiniciando".

   ![Banco de Dados do Azure para PostgreSQL - Reiniciar status ](./media/howto-restart-server-portal/4-restarting-status.png)

5. Confirme se a reinicialização do servidor foi bem-sucedida.

   ![Banco de Dados do Azure para PostgreSQL - Reiniciar com êxito ](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Próximas etapas

[Guia de Início Rápido – Criar um servidor do Banco de Dados do Azure para PostgreSQL usando o portal do Azure](./quickstart-create-server-database-portal.md)