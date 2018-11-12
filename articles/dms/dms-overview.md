---
title: Visão Geral do Serviço de Migração de Banco de Dados do Azure | Microsoft Docs
description: Visão geral do Serviço de Migração de Banco de Dados do Azure, que fornece migrações perfeitas de várias fontes de banco de dados para plataformas de dados do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: douglasl
ms.service: database-migration
ms.workload: data-services
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 053e571b6285cd405ea17f43fec1d3ea99732070
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235574"
---
# <a name="what-is-the-azure-database-migration-service"></a>O que é o Serviço de Migração de Banco de Dados do Azure?
O Serviço de Migração de Banco de Dados do Azure é um serviço totalmente gerenciado projetado para permitir migrações perfeitas de várias fontes de banco de dados para plataformas de dados do Azure com um tempo de inatividade mínimo (migrações online).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Migrar bancos de dados para Azure com ferramentas clássicas
O Serviço de Migração de Banco de Dados do Azure integra algumas funcionalidades dos nossos serviço e ferramentas existentes. Ele fornece aos clientes uma solução abrangente e altamente disponível. O serviço usa o [Data Migration Assistant](https://aka.ms/dma) para gerar relatórios de avaliação que fornecem recomendações para orientar você durante as alterações necessárias antes de executar uma migração. Cabe a você executar qualquer correção necessária. Quando você estiver pronto para iniciar o processo de migração, o Serviço de Migração de Banco de Dados do Azure executará todas as etapas necessárias. Você pode acioná-lo e ficar tranquilo quanto aos seus projetos de migração, com a certeza de que o processo aproveita as melhores práticas determinadas pela Microsoft.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online requer a criação de uma instância com base no tipo de preço do Comercialmente Crítico (Versão Prévia).

## <a name="regional-availability"></a>Disponibilidade regional
Atualmente, o Serviço de Migração de Banco de Dados do Azure está disponível nas seguintes regiões:

![Disponibilidade regional do Serviço de Migração do Banco de Dados do Azure](media\overview\dms-regional-availability1.png)

Para obter as informações mais atualizadas sobre a disponibilidade regional do Serviço de Migração de Banco de Dados do Azure, no site da infraestrutura global do Azure, consulte [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="next-steps"></a>Próximas etapas
- [Crie uma instância do Serviço de Migração de Banco de Dados do Azure usando o Portal do Azure](quickstart-create-data-migration-service-portal.md).
- [Migre do SQL Server para o Banco de Dados SQL do Azure](tutorial-sql-server-to-azure-sql.md).
- [Visão geral de pré-requisitos para usar o Serviço de Migração de Banco de Dados do Azure](pre-reqs.md).
- [Perguntas Frequentes sobre como usar o Serviço de Migração de Banco de Dados do Azure](faq.md).
