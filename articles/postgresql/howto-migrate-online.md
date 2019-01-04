---
title: Migração com tempo de inatividade mínimo para o Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve como realizar uma migração de tempo de inatividade mínimo de um banco de dados PostgreSQL para o Banco de Dados do Azure para PostgreSQL usando o Serviço de Migração de Banco de Dados do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: ceb64781dc7e5243f785ad239c24e5f21b0481ce
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543641"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migração com tempo de inatividade mínimo para o Banco de Dados do Azure para PostgreSQL
É possível realizar migrações do PostgreSQL para o Banco de Dados do Azure para PostgreSQL com tempo de inatividade mínimo usando o **recurso de sincronização contínua** introduzido recentemente para [DMS](https://aka.ms/get-dms) (Serviço de Migração de Banco de Dados do Azure). Essa funcionalidade limita o tempo de inatividade incorrido pelo aplicativo.

## <a name="overview"></a>Visão geral
O DMS do Azure executa uma carga inicial do local no Banco de Dados do Azure para PostgreSQL e, em seguida, sincroniza continuamente quaisquer novas transações no Azure enquanto o aplicativo permanece em execução. Depois que os dados são atualizados no lado do Azure de destino, você interrompe o aplicativo por um breve momento (tempo de inatividade mínimo), aguarde o último lote de dados (desde o momento em que você para o aplicativo até que o aplicativo esteja efetivamente indisponível para receber qualquer novo tráfego) para atualizar no destino e, em seguida, atualize a cadeia de conexão para apontar para o Azure. Quando você terminar, o aplicativo estará ativo no Azure!

![Sincronização contínua com o Serviço de Migração de Banco de Dados do Azure](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Próximas etapas
- Exibir o vídeo [Modernização de aplicativos com o Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), que contém uma demonstração mostrando como migrar aplicativos do PostgreSQL para o Banco de Dados do Azure para PostgreSQL.
- Consulte o tutorial [Migrar o PostgreSQL para o Banco de Dados do Azure para PostgreSQL on-line usando o DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
