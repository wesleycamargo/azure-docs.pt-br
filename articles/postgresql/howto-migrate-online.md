---
title: "Migração com tempo de inatividade mínimo para o Banco de Dados do Azure para PostgreSQL"
description: "Este artigo descreve como executar uma migração com tempo de inatividade mínimo extraindo um banco de dados PostgreSQL para um arquivo de despejo, restaurando o banco de dados PostgreSQL de um arquivo criado por pg_dump no Banco de Dados do Azure para PostgreSQL e configurando a carga inicial e a sincronização de dados contínua do banco de dados de origem para o banco de dados de destino usando o Attunity Replicate para Microsoft Migrations."
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 48cf460405ae3985553f9bff29f4fd7abb008196
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migração com tempo de inatividade mínimo para o Banco de Dados do Azure para PostgreSQL
Migre seu banco de dados PostgreSQL existente para o Banco de Dados do Azure para PostgreSQL usando o Attunity Replicate para Microsoft Migrations. O Attunity Replicate é uma oferta conjunta da Attunity e da Microsoft. Juntamente com o Serviço de Migração de Banco de Dados do Azure, ele é incluído sem custo adicional para os clientes da Microsoft. 

O Attunity Replicate ajuda a minimizar o tempo de inatividade durante as migrações de banco de dados e mantém o banco de dados de origem em funcionamento durante todo o processo.

O Attunity Replicate é uma ferramenta de replicação de dados que permite a sincronização de dados entre uma variedade de origens e destinos. Ele propaga o script de criação de esquema e os dados associados a cada tabela de banco de dados. O Attunity Replicate não propaga nenhum outro artefato (como SP, gatilhos, funções e assim por diante) nem converte, por exemplo, o código PL/SQL hospedado nesses artefatos em T-SQL.

> [!NOTE]
> Embora o Attunity Replicate seja compatível com um amplo conjunto de cenários de migração, ele enfoca o suporte para um subconjunto específico de pares de origem/destino.

Uma visão geral do processo para executar uma migração com tempo de inatividade mínimo inclui:

* **Migrar o esquema de origem do PostgreSQL** para um Banco de Dados do Azure para PostgreSQL usando o comando [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) com o parâmetro -n e, em seguida, usando o comando [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html).

* **Configurar a carga inicial e a sincronização de dados contínua do banco de dados de origem para o banco de dados de destino** usando o Attunity Replicate para Microsoft Migrations. Fazer isso minimiza o tempo que o banco de dados de origem precisa estar definido como somente leitura quando você se prepara para passar seus aplicativos para o banco de dados PostgreSQL de destino no Azure.

Para obter mais informações sobre a oferta Attunity Replicate para Microsoft Migrations, consulte os seguintes recursos:
 - Acesse a página da Web do [Attunity Replicate para Microsoft Migrations](https://aka.ms/attunity-replicate).
 - Baixe o [Attunity Replicate para Microsoft Migrations](http://discover.attunity.com/download-replicate-microsoft-lp6657.html).
 - Acesse a [Comunidade do Attunity Replicate](https://aka.ms/attunity-community/) par obter um Guia de Início Rápido, tutoriais e suporte.
 - Para obter diretrizes passo a passo sobre como usar o Attunity Replicate para migrar do PostgreSQL para o Banco de Dados do Azure para PostgreSQL, consulte o [Guia de Migração de Banco de Dados](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql).