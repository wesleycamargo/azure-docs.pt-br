---
title: Armazenar backups do Banco de Dados SQL do Azure por um período de até 10 anos | Microsoft Docs
description: Saiba como o Banco de Dados SQL do Azure dá suporte para o armazenamento de backups completos do banco de dados por até 10 anos.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 51f00984a8f0d750bdb478ae4bc8093adad8108e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Armazenar backups do Banco de Dados SQL do Azure por um período de até 10 anos

Muitos aplicativos têm fins regulamentares, de conformidade ou outros fins comerciais que exigem a retenção dos backups de banco de dados além dos 7 a 35 dias fornecidos pelos [backups automáticos](sql-database-automated-backups.md) do Banco de Dados SQL do Microsoft Azure. Usando o recurso LTR (retenção de longo prazo), é possível armazenar backups completos especificados do Banco de Dados SQL no armazenamento de blobs [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) por até 10 anos. É possível restaurar qualquer backup como um novo banco de dados.

> [!IMPORTANT]
> A retenção de longo prazo atualmente está em versão prévia. Os backups existentes armazenados no cofre de Serviço de Recuperação de Serviços do Azure como parte da versão prévia anterior desse recurso são migrados para o armazenamento do SQL Azure.<!-- and available in the following regions: Australia East, Australia Southeast, Brazil South, Central US, East Asia, East US, East US 2, India Central, India South, Japan East, Japan West, North Central US, North Europe, South Central US, Southeast Asia, West Europe, and West US.-->
>

## <a name="how-sql-database-long-term-retention-works"></a>Como funciona a retenção de longo prazo do Banco de Dados SQL

A retenção de backup de longo prazo aproveita os [backups automáticos do Banco de Dados SQL](sql-database-automated-backups.md) criados para PITR (Recuperação Pontual). É possível configurar uma política de retenção de longo prazo para cada banco de dados SQL e especificar com que frequência você precisa copiar os backups para o armazenamento de longo prazo. Para permitir essa flexibilidade, você pode definir a política usando uma combinação de quatro parâmetros: retenção de backup semanal (W), retenção de backup mensal (M), retenção de backup anual (Y) e semana do ano (WeekOfYear). Se você especificar W, um backup por semana será copiado para o armazenamento de longo prazo. Se você especificar M, um backup durante a primeira semana de cada mês será copiado para o armazenamento de longo prazo. Se você especificar Y, um backup durante a semana especificada por WeekOfYear será copiado para o armazenamento de longo prazo. Cada backup será mantido no armazenamento de longo prazo pelo período especificado por esses parâmetros. 

Exemplos:

-  W=0, M=0, Y=5, WeekOfYear=3

   O 3º backup completo de cada ano será mantido por 5 anos.

- W=0, M=3, Y=0

   O 1º backup completo de cada mês será mantido por 3 meses.

- W=12, M=0, Y=0

   Cada backup completo semanal será mantido por 12 semanas.

- W=6, M=12, Y=10, WeekOfYear=16

   Cada backup completo semanal será mantido por 6 semanas. Exceto o 1º backup completo de cada mês, que será mantido por 12 meses. Exceto o backup completo realizado na 16ª semana do ano, que será mantido por 10 anos. 

A tabela a seguir ilustra a cadência e a expiração dos backups de longo prazo para a seguinte política:

W=12 semanas (84 dias), M=12 meses (365 dias), Y=10 anos (3650 dias), WeekOfYear=15 (semana após 15 de abril)

   ![Exemplo de LTR](./media/sql-database-long-term-retention/ltr-example.png)


 
Se você modificasse a política acima e definisse W=0 (sem backups semanais), a cadência das cópias de backup mudaria, conforme mostrado na tabela acima pelas datas destacadas. O valor de armazenamento necessário para manter esses backups reduziria adequadamente. Observação: as cópias de LTR são criadas pelo serviço de armazenamento do Azure, de modo que o processo de cópia não tenha impacto no desempenho do banco de dados existente.
Para restaurar um banco de dados do armazenamento LTR, você pode selecionar um backup específico com base no carimbo de data/hora.   O banco de dados pode ser restaurado para qualquer servidor existente sob a mesma assinatura do banco de dados original. 

## <a name="configure-long-term-backup-retention"></a>Configurar retenção de backup de longo prazo

Para saber como configurar a retenção de longo prazo usando o Portal do Azure ou usando o PowerShell, consulte [Configurar a retenção de backup de longo prazo ](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Próximas etapas

Como os backups de banco de dados protegem os dados de danos ou exclusão acidental, eles são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios. Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
