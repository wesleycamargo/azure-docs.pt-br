---
title: Armazenar backups do Banco de Dados SQL do Azure por um período de até 10 anos | Microsoft Docs
description: Saiba como o Banco de Dados SQL do Azure dá suporte para o armazenamento de backups completos do banco de dados por até 10 anos.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 3d6010df64c4e3c75bd05e2eb9828c07cf3fb342
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471378"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Armazenar backups do Banco de Dados SQL do Azure por um período de até 10 anos

Muitos aplicativos têm fins regulamentares, de conformidade ou outros fins comerciais que exigem a retenção dos backups de banco de dados além dos 7 a 35 dias fornecidos pelos [backups automáticos](sql-database-automated-backups.md) do Banco de Dados SQL do Microsoft Azure. Usando o recurso LTR (retenção de longo prazo), é possível armazenar backups completos especificados do Banco de Dados SQL no armazenamento de blobs [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) por até 10 anos. É possível restaurar qualquer backup como um novo banco de dados.

> [!NOTE]
> O LTR pode ser habilitado para bancos de dados autônomos e em pool. Ainda não está disponível para bancos de dados em instâncias em hospedados em Instâncias Gerenciadas. Você pode usar trabalhos do SQL Agent para agendar [backups somente cópia de banco de dados](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) como uma alternativa ao LTR alem de 35 dias.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Como funciona a retenção de longo prazo do Banco de Dados SQL

A retenção de backup de longo prazo (LTR) aproveita os backups completos do banco de dados [criados automaticamente](sql-database-automated-backups.md) para habilitar a restauração pontual (PITR). Esses backups são copiados para diferentes blobs de armazenamento se a política de LTR estiver configurada.
Você pode configurar uma política LTR para cada banco de dados SQL e especificar com que frequência você precisa copiar os backups para os blobs de armazenamento de longo prazo. Para permitir essa flexibilidade, você pode definir a política usando uma combinação de quatro parâmetros: retenção de backup semanal (W), retenção de backup mensal (M), retenção de backup anual (Y) e semana do ano (WeekOfYear). Se você especificar W, um backup por semana será copiado para o armazenamento de longo prazo. Se você especificar M, um backup durante a primeira semana de cada mês será copiado para o armazenamento de longo prazo. Se você especificar Y, um backup durante a semana especificada por WeekOfYear será copiado para o armazenamento de longo prazo. Cada backup será mantido no armazenamento de longo prazo pelo período especificado por esses parâmetros. 

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


 
Se você modificasse a política acima e definisse W=0 (sem backups semanais), a cadência das cópias de backup mudaria, conforme mostrado na tabela acima pelas datas destacadas. O valor de armazenamento necessário para manter esses backups reduziria adequadamente. 

> [!NOTE]
1. As cópias LTR são criadas pelo serviço de armazenamento do Azure, de modo que o processo de cópia não cause nenhum impacto de desempenho no banco de dados existente.
2. A política se aplica aos backups futuros. Por exemplo se a WeekOfYear especificada estiver no passado quando a política for configurada, o primeiro backup LTR será criado no próximo ano. 
3. Para restaurar um banco de dados do armazenamento LTR, você pode selecionar um backup específico com base no carimbo de data/hora.   O banco de dados pode ser restaurado para qualquer servidor existente sob a mesma assinatura do banco de dados original. 
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Replicação geográfica e retenção de backup de longo prazo

Se estiver usando grupos de failover ou de replicação geográfica ativos como a solução de continuidade de negócios, prepare-se para eventuais failovers e configure a mesma política de LTR no banco de dados geográfico secundário. Isso não aumentará o custo de armazenamento de LTR, pois os backups não serão gerados a partir dos secundários. Somente quando o secundário tornar-se primário, os backups serão criados. Dessa forma, você garantirá a geração não interrompida dos backups de LTR quando o failover for acionado e o primário for movido para a região secundária. 

> [!NOTE]
Quando o banco de dados primário original recuperar-se da indisponibilidade que causa o failover, ele se tornará um novo secundário. Portanto, a criação de backup não será retomada e a política de LTR existente não terá efeito até que torne-se primário novamente. 
> 

## <a name="configure-long-term-backup-retention"></a>Configurar retenção de backup de longo prazo

Para saber como configurar a retenção de longo prazo usando o Portal do Azure ou usando o PowerShell, consulte [Configurar a retenção de backup de longo prazo ](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Próximas etapas

Como os backups de banco de dados protegem os dados de danos ou exclusão acidental, eles são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios. Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
