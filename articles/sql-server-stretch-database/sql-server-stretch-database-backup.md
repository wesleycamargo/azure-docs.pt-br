---
title: Fazer backup de bancos de dados habilitados para Stretch | Microsoft Docs
description: '\-Saiba como fazer backup de bancos de dados habilitados para Stretch.'
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: a196f858-ef8f-47b5-b9db-bb7db98d48bd
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 68aeee33dbbba67e2a24300db62bf6bcdc627ea5


---
# <a name="backup-stretch-enabled-databases"></a>Fazer backup de bancos de dados habilitados para o Stretch
Backups de bancos de dados ajudam você a se recuperar de muitos tipos de desastres, erros e falhas.  

* Você precisa fazer backup de seus bancos de dados do SQL Server habilitados para Stretch.  
* O Microsoft Azure faz backup automaticamente dos dados remotos que o Stretch Database migrou do SQL Server para o Azure.  

> [!NOTE]
> O backup é apenas uma parte de uma solução de continuidade de negócios de alta disponibilidade. Para obter mais informações sobre a alta disponibilidade, consulte [Soluções de alta disponibilidade](https://msdn.microsoft.com/library/ms190202.aspx).
> 
> 

## <a name="back-up-your-sql-server-data"></a>Fazer backup dos dados do SQL Server
Para fazer backup de seus bancos de dados do SQL Server habilitados para Stretch, você pode continuar usando os métodos de backup do SQL Server utilizados atualmente. Para obter mais informações, consulte [Backup e restauração de bancos de dados do SQL Server](https://msdn.microsoft.com/library/ms187048.aspx).

Backups de um banco de dados do SQL Server habilitado para Stretch contêm apenas dados locais e dados qualificados para migração no momento em que o backup é executado. \(Dados qualificados são dados que ainda não foram migrados, mas serão migrados para o Azure com base nas configurações de migração das tabelas.\) Isso é conhecido como backup **superficial** e não inclui dados já migrados para o Azure.  

## <a name="back-up-your-remote-azure-data"></a>Fazer backup de dados remotos do Azure
O Microsoft Azure faz backup automaticamente dos dados remotos que o Stretch Database migrou do SQL Server para o Azure.  

### <a name="azure-reduces-the-risk-of-data-loss-with-automatic-backup"></a>O Azure reduz o risco de perda de dados com o backup automático
O serviço SQL Server Stretch Database no Azure protege seus bancos de dados remotos com instantâneos automáticos de armazenamento pelo menos a cada 8 horas. Ele mantém cada instantâneo por 7 dias para fornecer a você uma variedade de possíveis pontos de restauração.  

### <a name="azure-reduces-the-risk-of-data-loss-with-geo-redundancy"></a>O Azure reduz o risco de perda de dados com a redundância geográfica
Os backups de bancos de dados do Azure são armazenados no Armazenamento do Azure com redundância geográfica (RA\-GRS) e, portanto, têm redundância geográfica por padrão. O armazenamento com redundância geográfica replica seus dados para uma região secundária a centenas de quilômetros da região primária. Em ambas as regiões, primária e secundária, seus dados são replicados três vezes cada um, entre domínios de falha e domínios de atualização separados. Isso garante a durabilidade dos dados, mesmo no caso de uma interrupção regional completa ou um desastre que cause indisponibilidade das regiões do Azure.

### <a name="a-namestretchrpoastretch-database-reduces-the-risk-of-data-loss-for-your-azure-data-by-retaining-migrated-rows-temporarily"></a><a name="stretchRPO"></a>O Stretch Database reduz o risco de perda de dados para seus dados do Azure mantendo temporariamente linhas migradas
Após migrar linhas qualificadas do SQL Server para o Azure, o Stretch Database retém as linhas na tabela de preparo por um mínimo de 8 horas. Se você restaurar um backup do banco de dados do Azure, o Stretch Database usará as linhas salvas na tabela de preparo para reconciliar os bancos de dados do SQL Server e do Azure.

Depois de restaurar um backup de seus dados do Azure, você precisa executar o procedimento armazenado [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) para reconectar o banco de dados do SQL Server habilitado para o Stretch e o banco de dados remoto do Azure. Quando você executa **sys.sp_rda_reauthorize_db**, o Stretch Database sincroniza automaticamente os bancos de dados do SQL Server e do Azure.

Para aumentar o número de horas de dados migrados que o Stretch Database retém temporariamente na tabela de preparo, execute o procedimento armazenado [sys.sp_rda_set_rpo_duration](https://msdn.microsoft.com/library/mt707766.aspx) e especifique um número maior que 8 horas. Para decidir a quantidade de dados a serem retidos, considere os seguintes fatores:

* A frequência dos backups automáticos do Azure (pelo menos a cada 8 horas).
* O tempo necessário após um problema para reconhecer o problema e decidir restaurar um backup.
* A duração da operação de restauração do Azure.

> [!NOTE]
> Aumentar a quantidade de dados que o Stretch Database retém temporariamente na tabela de preparo aumenta a quantidade de espaço necessária no SQL Server.
> 
> 

Para verificar o número de horas de dados que o Stretch Database retém temporariamente na tabela de preparo, execute o procedimento armazenado [sys.sp_rda_get_rpo_duration](https://msdn.microsoft.com/library/mt707767.aspx).

## <a name="see-also"></a>Consulte também
[Gerenciar e solucionar problemas do Banco de Dados de Stretch](sql-server-stretch-database-manage.md)

[sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Backup e restauração de bancos de dados do SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)




<!--HONumber=Nov16_HO3-->


