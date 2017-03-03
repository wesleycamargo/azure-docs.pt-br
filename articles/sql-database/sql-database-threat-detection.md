---
title: "Detecção de ameaças – Banco de Dados SQL do Azure | Microsoft Docs"
description: "A Detecção de Ameaças detecta as atividades anormais do banco de dados que indicam possíveis ameaças de segurança ao banco de dados."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: v-romcal
ms.assetid: b50d232a-4225-46ed-91e7-75288f55ee84
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 07/10/2016
ms.author: ronmat; ronitr
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: eadaa3e7a279b6b92da1d0c026c3002297dfd298
ms.lasthandoff: 02/17/2017


---
# <a name="sql-database-threat-detection"></a>Detecção de Ameaças do Banco de Dados SQL

A Detecção de Ameaças detecta as atividades anormais do banco de dados que indicam possíveis ameaças de segurança ao banco de dados.  A detecção de ameaças está atualmente em visualização.

## <a name="overview"></a>Visão geral

A Detecção de Ameaças fornece uma nova camada de segurança, que permite que os clientes detectem e respondam às ameaças potenciais conforme elas ocorrem, fornecendo alertas de segurança nas atividades anormais.  Os usuários podem explorar os eventos suspeitos usando a [Auditoria do Banco de Dados SQL](sql-database-auditing.md) para determinar se eles resultam de uma tentativa de acesso, violação ou exploração dos dados no banco de dados.
A Detecção de Ameaças torna simples tratar as possíveis ameaças no banco de dados sem a necessidade de ser um especialista em segurança ou gerenciar os sistemas de monitoramento de segurança avançados.

Por exemplo, a Detecção de Ameaças detecta determinadas atividades anormais do banco de dados que indicam possíveis tentativas de injeção de SQL. A injeção de SQL é um dos problemas comuns de segurança do aplicativo da Web na Internet, usada para atacar os aplicativos controlados por dados. Os invasores aproveitam as vulnerabilidades do aplicativo para inserir instruções SQL mal-intencionadas nos campos de entrada do aplicativo, para violar ou modificar os dados no banco de dados.

## <a name="next-steps"></a>Próximas etapas

* Para configurar e gerenciar a detecção de ameaças, consulte [Configurar e gerenciar a detecção de ameaças no Portal do Azure](sql-database-threat-detection-portal.md).

