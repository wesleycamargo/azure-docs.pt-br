---
title: "Migração com tempo de inatividade mínimo para o Banco de Dados do Azure para MySQL | Microsoft Docs"
description: "Este artigo descreve como executar uma migração com tempo de inatividade mínimo de um banco de dados MySQL para o Banco de Dados do Azure para MySQL e como configurar o carregamento inicial e a sincronização de dados contínua do banco de dados de origem para o banco de dados de destino usando o Attunity Replicate para Microsoft Migrations."
services: mysql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: d23628fd8446f6e7e0e5ed14b98da13c09b2d592
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migração com tempo de inatividade mínimo para o Banco de Dados do Azure para MySQL
Migre seu banco de dados MySQL existente para o Banco de Dados do Azure para MySQL usando o Attunity Replicate para Microsoft Migrations. O Attunity Replicate é uma oferta conjunta da Attunity e da Microsoft. Juntamente com o Serviço de Migração de Banco de Dados do Azure, ele é incluído sem custo adicional para os clientes da Microsoft. 

O Attunity Replicate ajuda a minimizar o tempo de inatividade durante as migrações de banco de dados e mantém o banco de dados de origem em funcionamento durante todo o processo.

O Attunity Replicate é uma ferramenta de replicação de dados que permite a sincronização de dados entre uma variedade de origens e destinos. Ele propaga o script de criação de esquema e os dados associados a cada tabela de banco de dados. O Attunity Replicate não propaga nenhum outro artefato (como SP, gatilhos, funções e assim por diante) nem converte, por exemplo, o código PL/SQL hospedado nesses artefatos em T-SQL.

> [!NOTE]
> Embora o Attunity Replicate seja compatível com um amplo conjunto de cenários de migração, ele enfoca o suporte para um subconjunto específico de pares de origem/destino.

Uma visão geral do processo para executar uma migração com tempo de inatividade mínimo inclui:

* **Migrar o esquema de origem do MySQL** para um serviço de banco de dados gerenciado do Banco de Dados do Azure para MySQL usando o [MySQL Workbench](https://www.mysql.com/products/workbench/).

* **Configurar a carga inicial e a sincronização de dados contínua do banco de dados de origem para o banco de dados de destino** usando o Attunity Replicate para Microsoft Migrations. Fazer isso minimiza o tempo que o banco de dados de origem precisa estar definido como somente leitura quando você se prepara para passar seus aplicativos para o banco de dados MySQL de destino no Azure.

Para obter mais informações sobre a oferta Attunity Replicate para Microsoft Migrations, consulte os seguintes recursos:
 - Acesse a página da Web do [Attunity Replicate para Microsoft Migrations](https://aka.ms/attunity-replicate).
 - Baixe o [Attunity Replicate para Microsoft Migrations](http://discover.attunity.com/download-replicate-microsoft-lp6657.html).
 - Acesse a [Comunidade do Attunity Replicate](https://aka.ms/attunity-community) par obter um Guia de Início Rápido, tutoriais e suporte.
 - Para obter diretrizes passo a passo sobre como usar o Attunity Replicate para migrar o banco de dados MySQL para o Banco de Dados do Azure para MySQL, consulte o [Guia de Migração de Banco de Dados](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql).