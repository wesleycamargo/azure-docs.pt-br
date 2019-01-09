---
title: Otimizar o tempo de consulta no Banco de Dados do Azure para PostgreSQL usando a estratégia de armazenamento da tabela de toast
description: Este artigo descreve como otimizar o tempo de consulta com a estratégia de armazenamento da tabela de toast em um banco de dados do Azure para PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 1fb818a65e26f969f72131b0f5265f3efdd36bb6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542183"
---
# <a name="optimizing-query-time-with-toast-table-storage-strategy"></a>Otimizando o tempo de consulta com a estratégia de armazenamento de tabela TOAST 
Este artigo descreve como otimizar os tempos de consulta com a estratégia de armazenamento de tabelas TOAST.

## <a name="toast-table-storage-strategies"></a>Estratégias de armazenamento de tabelas TOAST
Existem quatro estratégias diferentes para armazenar colunas que podem ser torradas no disco, representando várias combinações entre a compactação e o armazenamento fora de linha. A estratégia pode ser definida no nível do tipo de dados e no nível da coluna.
- **Sem Formatação** impede a compactação ou o armazenamento fora de linha; além disso, desabilita o uso de cabeçalhos de byte único para tipos de varlena. **Sem Formatação** é a única estratégia possível para colunas de tipos de dados que não podem ser torrados.
- **Estendido** permite compactação e armazenamento fora de linha. **Estendido** é o padrão para a maioria dos tipos de dados capaz de notificação do sistema. A compactação será tentada primeiro e, em seguida, o armazenamento fora de linha se a linha ainda for muito grande.
- **External** permite armazenamento fora de linha, mas não compactação. Uso de **Externo** fará as operações de sub cadeia de caracteres no texto grande e bytea colunas mais rápidas, em que a penalidade de aumento de espaço de armazenamento, porque essas operações são otimizadas para buscar apenas o necessário partes de fora da linha valor quando ele não é compactado.
- **Principal** permite a compactação, mas não o armazenamento fora de linha. O armazenamento fora de linha ainda será executado para essas colunas, mas apenas como último recurso, quando não houver outra maneira de tornar a linha pequena o suficiente para caber em uma página.

## <a name="using-toast-table-storage-strategies"></a>Usando estratégias de armazenamento de tabelas TOAST
Se suas consultas acessarem tipos de dados compatíveis, considere usar **Principal** em vez da opção padrão **Estendido** para reduzir os tempos de consulta. **Principal** não evita que o armazenamento de fora de linha. Por outro lado, se suas consultas não acessarem tipos de dados compatíveis, poderá ser benéfico manter a opção **Estendido**. Portanto, uma parte maior das linhas da tabela principal caberá no cache de buffer compartilhado, ajudando no desempenho.

Se você tiver uma carga de trabalho usando um esquema com tabelas largas e altas contagens de caracteres, considere o uso de tabelas TOAST do PostgreSQL. Um exemplo de tabela de clientes tinha mais de 350 colunas com várias colunas abrangendo 255 caracteres. Seu tempo de consulta de benchmark reduziu de 4203 segundos para 467 segundos, uma melhoria de 89%, após a conversão da estratégia TOAST de **Principal**.

## <a name="next-steps"></a>Próximas etapas
Analise sua carga de trabalho para as características acima. 

Examine a seguinte documentação do PostgreSQL: [Capítulo 68, Armazenamento físico de banco de dados](https://www.postgresql.org/docs/current/storage-toast.html) 