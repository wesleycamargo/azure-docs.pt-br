---
title: "Migração com tempo de inatividade mínimo para o Banco de Dados do Azure para MySQL | Microsoft Docs"
description: "Este artigo descreve como executar uma migração com tempo de inatividade mínimo de um banco de dados MySQL para o Banco de Dados do Azure para MySQL e como configurar o carregamento inicial e a sincronização de dados contínua do banco de dados de origem para o banco de dados de destino usando o Attunity Replicate para o Microsoft Migrations."
services: mysql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: 429f5c9f939a802184a6513a63fb9115abf4b235
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migração com tempo de inatividade mínimo para o Banco de Dados do Azure para MySQL
Você pode migrar seu banco de dados MySQL existente para o Banco de Dados do Azure para MySQL usando o Attunity Replicate para Microsoft Migrations, uma oferta patrocinada conjunta da Attunity e da Microsoft que é fornecida com o Serviço de Migração de Banco de Dados do Azure sem nenhum custo adicional para os clientes da Microsoft. O Attunity Replicate para Microsoft Migrations também possibilita que haja tempo de inatividade mínimo na migração de banco de dados, e o banco de dados de origem continua operacional durante o processo de migração.

O Attunity Replicate é uma ferramenta de replicação de dados que permite sincronizar dados entre uma variedade de fontes e destinos, propagando o script de criação de esquema e os dados associados a cada tabela de banco de dados. O Attunity Replicate não propaga nenhum outro artefato (como SP, gatilhos, funções etc.), nem converte, por exemplo, código PL/SQL hospedado nesses artefatos, em T-SQL.

> [!NOTE]
> Enquanto o Attunity Replicate é compatível com uma ampla gama de cenários de migração, o Attunity Replicate para Microsoft Migrations tem como foco o suporte a um subconjunto específico de pares de origem/destino.

Uma visão geral do processo para executar uma migração com tempo de inatividade mínimo inclui:

1. **Migrar o esquema de origem do MySQL** para um Banco de Dados do Azure para MySQL usando o [MySQL Workbench](https://www.mysql.com/products/workbench/).

2. **Configurar a carga inicial e a sincronização de dados contínua do banco de dados de origem para o banco de dados de destino** usando o Attunity Replicate para Microsoft Migrations. Fazer isso minimiza o tempo que o banco de dados de origem precisa estar definido como somente leitura quando você se prepara para passar seus aplicativos para o banco de dados MySQL de destino no Azure.

Para obter mais informações sobre a oferta do Attunity Replicate para Microsoft Migrations, consulte os seguintes recursos:
 - A [página da Web](https://aka.ms/attunity-replicate) do Attunity Replicate para Microsoft Migrations.
 - [Baixar](http://discover.attunity.com/download-replicate-microsoft-lp6657.html) o Attunity Replicate para Microsoft Migrations.
 - A [Comunidade](https://microsoft.attunity.com/) do Attunity Replicate, com um guia de início rápido, tutoriais e suporte.
 - Para obter orientações passo a passo sobre como usar o Attunity para migrar do MySQL para o Banco de Dados do Azure para MySQL, consulte o [Guia de migração de banco de dados](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql).