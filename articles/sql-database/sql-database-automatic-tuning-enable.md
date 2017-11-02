---
title: "Habilitar o ajuste automático para o Banco de Dados SQL do Azure | Microsoft Docs"
description: "Habilite o ajuste automático do Banco de Dados SQL do Azure com facilidade."
services: sql-database
documentationcenter: 
author: veljko-msft
manager: drasumic
editor: vvasic
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/19/2016
ms.author: veljko-msft
ms.openlocfilehash: 82db5996c1ba1f224593e4eaa5b3b0067755db49
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="enable-automatic-tuning"></a>Habilitar o ajuste automático

O Banco de Dados SQL do Azure é um serviço de dados gerenciados automaticamente que monitora as consultas constantemente e identifica a ação que pode ser executada para melhorar o desempenho da carga de trabalho. Examine as recomendações e aplique-as manualmente ou permita que o Banco de Dados SQL do Azure aplique as ações corretivas automaticamente – isso é conhecido como **modo de ajuste automático**. O ajuste automático pode ser habilitado no nível do servidor ou do banco de dados.

## <a name="enable-automatic-tuning-on-server"></a>Habilitar o ajuste automático no servidor

Para habilitar o ajuste automático no servidor de Banco de Dados SQL do Azure, navegue para o servidor no portal do Azure e, em seguida, selecione **Ajuste automático** no menu. Selecione as opções de ajuste automático que você deseja habilitar e selecione **Aplicar**:

![Servidor](./media/sql-database-automatic-tuning-enable/server.png)

As opções de ajuste automático no servidor são aplicadas a todos os bancos de dados do servidor. Por padrão, todos os bancos de dados herdam a configuração de seu servidor pai, mas isso pode ser substituído e especificado para cada banco de dados individualmente.

## <a name="configure-automatic-tuning-on-database"></a>Configurar o ajuste automático no banco de dados

O portal do Azure permite especificar a configuração de ajuste automático individualmente em cada banco de dados.

> [!NOTE]
> A recomendação geral é gerenciar a configuração de ajuste automático no nível de servidor, de forma que as mesmas definições de configuração possam ser aplicadas em cada banco de dados automaticamente. Configure o ajuste automático em um banco de dados individual caso ele seja diferente dos outros no mesmo servidor.
>

Para habilitar o ajuste automático em um banco de dados individual, navegue para o banco de dados no portal do Azure e, em seguida, selecione **Ajuste automático**. Configure um banco de dados individual para herdar as configurações do servidor selecionando a opção ou especifique a configuração de um banco de dados individualmente.

![Banco de dados](./media/sql-database-automatic-tuning-enable/database.png)

Depois de selecionar a configuração apropriada, clique em **Aplicar**.

## <a name="next-steps"></a>Próximas etapas
* Leia o [artigo Ajuste automático](sql-database-automatic-tuning.md) para saber mais sobre o ajuste automático e como ele pode ajudar você a melhorar o desempenho.
* Consulte [Recomendações de desempenho](sql-database-advisor.md) para obter uma visão geral das recomendações de desempenho do Banco de Dados SQL do Azure.
* Consulte [Análise de desempenho de consultas](sql-database-query-performance.md) para saber mais sobre como visualizar o impacto no desempenho de suas principais consultas.
