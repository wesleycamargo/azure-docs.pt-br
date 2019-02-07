---
title: O SQL Data Warehouse Gen2 do Azure oferece suporte a camadas inferiores de computação | Microsoft Docs
description: O SQL Data Warehouse Gen2 do Azure agora oferece suporte a camadas inferiores de computação
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 12/03/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 22993395d60df23533b67ec48d0ea7f979d73e22
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692455"
---
# <a name="azure-sql-data-warehouse-gen2-support-for-lower-compute-tiers"></a>O SQL Data Warehouse Gen2 do Azure oferece suporte a camadas inferiores de computação

A Microsoft está ajudando a reduzir o custo inicial da execução de um data warehouse capaz de processar consultas mais exigentes, adicionando as camadas de computação inferiores ao ultrarrápido SQL Data Warehouse Gen2 do Azure. Os clientes podem experimentar os recursos de segurança, flexibilidade e desempenho líderes do SQL Data Warehouse do Azure, começando com 100 cDWU (Unidades de Data Warehouse) e aumentá-las para 30.000 cDWU em poucos minutos. Os clientes podem se beneficiar do desempenho e da flexibilidade do Gen2 com camadas de computação inferiores. 

Ao remover o ponto de entrada para o data warehouse da próxima geração, a Microsoft abre as portas para clientes orientados pelo valor que desejam avaliar todos os benefícios de um data warehouse seguro e de alto desempenho, sem ter que adivinhar qual ambiente de avaliação é melhor para eles.  Os clientes podem começar com apenas 100 cDWU, abaixo do ponto de entrada atual de 500 cDWU.  O SQL Data Warehouse Gen2 continua oferecendo suporte a operações de pausa e retomada e vai além de apenas flexibilidade na computação.  O Gen2 também oferece suporte à capacidade ilimitada de armazenamento no repositório de coluna, juntamente com 2,5x mais memória por consulta, até 128 consultas simultâneas e recursos de cache adaptável, gerando experiências com desempenho, em média, cinco vezes melhor quando comparado à mesma Unidade de Data Warehouse no Gen1 pelo mesmo preço.  Backups com redundância geográfica são um padrão no Gen2 com a proteção de dados incorporada garantida. O SQL Data Warehouse Gen2 do Azure está pronto para ser dimensionado de acordo com as suas necessidades.

Atualmente, o portal não oferece suporte à implantação ou ao dimensionamento para instâncias de camadas inferiores do Gen2. Estamos trabalhando para habilitar este recurso, enquanto isso, [envie um tíquete](sql-data-warehouse-get-started-create-support-ticket.md) caso queira utilizar essa nova camada.

## <a name="getting-started-with-azure-sql-data-warehouse-gen2"></a>Introdução ao SQL Data Warehouse Gen2 do Azure 

Os clientes podem optar por implantar uma nova instância do Gen2 ou atualizar uma instância de data warehouse Gen1 existente para ter acesso à flexibilidade e ao desempenho da próxima geração de data warehouse. 

Experimente a [Camada com computação otimizada do SQL Data Warehouse Gen2 do Azure.](https://azure.microsoft.com/services/sql-data-warehouse/?v=17.44)
Atualize da [Computação otimizada do SQL Data Warehouse Gen1 do Azure para o Gen2](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation). Veja o SQL Data Warehouse Gen2 do Azure em ação neste [vídeo do Microsoft Mechanics.](https://www.youtube.com/watch?v=Ap8I3UZonzI&feature=youtu.be)


## <a name="supported-regions-for-lower-compute-tiers"></a>Regiões com suporte para níveis inferiores de computação

- Leste dos EUA 1 
- Leste dos EUA 2
- Europa Ocidental
- Norte da Europa
- Oeste dos EUA 2
- Sudeste Asiático
- Centro-Sul dos EUA
- Centro dos EUA 
- Ásia Oriental
- Leste do Japão
- Centro da Índia
- Leste da Austrália
- Sudeste da Austrália
- Canadá Central
- Oeste do Japão 
- Canadá Central

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](upgrade-to-latest-generation.md) sobre como otimizar o desempenho da computação otimizada ao atualizar o SQL Data Warehouse. 
