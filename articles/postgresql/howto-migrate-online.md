---
title: "Migração com tempo de inatividade mínimo para o Banco de Dados do Azure para PostgreSQL | Microsoft Docs"
description: "Este artigo descreve como executar uma migração com tempo de inatividade mínimo extraindo um banco de dados PostgreSQL para um arquivo de despejo, restaurando o banco de dados PostgreSQL de um arquivo criado por pg_dump no Banco de Dados do Azure para PostgreSQL e configurando a carga inicial e a sincronização de dados contínua do banco de dados de origem para o banco de dados de destino usando o Attunity Replicate para Microsoft Migrations."
services: postgresql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: 8c98c58042e7f1d1726eaad6ee03d1531b6c910e
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migração com tempo de inatividade mínimo para o Banco de Dados do Azure para PostgreSQL
Você pode migrar seu banco de dados PostgreSQL existente para o Banco de Dados do Azure para PostgreSQL usando o Attunity Replicate para Microsoft Migrations, uma oferta patrocinada conjunta da Attunity e da Microsoft que é fornecida com o Serviço de Migração de Banco de Dados do Azure sem nenhum custo adicional para os clientes da Microsoft. O Attunity Replicate para Microsoft Migrations também possibilita que haja tempo de inatividade mínimo na migração de banco de dados, e o banco de dados de origem continua operacional durante o processo de migração.

O Attunity Replicate é uma ferramenta de replicação de dados que permite sincronizar dados entre uma variedade de fontes e destinos, propagando o script de criação de esquema e os dados associados a cada tabela de banco de dados. O Attunity Replicate não propaga nenhum outro artefato (como SP, gatilhos, funções etc.), nem converte, por exemplo, código PL/SQL hospedado nesses artefatos, em T-SQL.

> [!NOTE]
> Enquanto o Attunity Replicate é compatível com uma ampla gama de cenários de migração, o Attunity Replicate para Microsoft Migrations tem como foco o suporte a um subconjunto específico de pares de origem/destino.

Uma visão geral do processo para executar uma migração com tempo de inatividade mínimo inclui:

1. **Migrar o esquema de origem do PostgreSQL** para um Banco de Dados do Azure para PostgreSQL usando o comando [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) com o parâmetro -n e, em seguida, usando o comando [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html).

2. **Configurar a carga inicial e a sincronização de dados contínua do banco de dados de origem para o banco de dados de destino** usando o Attunity Replicate para Microsoft Migrations. Fazer isso minimiza o tempo que o banco de dados de origem precisa estar definido como somente leitura quando você se prepara para passar seus aplicativos para o banco de dados PostgreSQL de destino no Azure.

Para obter mais informações sobre a oferta do Attunity Replicate para Microsoft Migrations, consulte os seguintes recursos:
 - A [página da Web](https://aka.ms/attunity-replicate) do Attunity Replicate para Microsoft Migrations.
 - [Baixar](http://discover.attunity.com/download-replicate-microsoft-lp6657.html) o Attunity Replicate para Microsoft Migrations.
 - A [Comunidade](https://microsoft.attunity.com/) do Attunity Replicate, com um guia de início rápido, tutoriais e suporte.
 - Para obter orientações passo a passo sobre como usar o Attunity para migrar do PostgreSQL para o Banco de Dados do Azure para PostgreSQL, consulte o [Guia de migração de banco de dados](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql).