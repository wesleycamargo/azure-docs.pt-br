---
title: "Armazenando backups do Banco de Dados SQL do Azure por um período de até 10 anos | Microsoft Docs"
description: "Saiba como o Banco de Dados SQL do Azure dá suporte ao armazenamento de backups por um período de até 10 anos."
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/21/2016
ms.author: sashan
translationtype: Human Translation
ms.sourcegitcommit: 10b40214ad4c7d7bb7999a5abce1c22100b617d8
ms.openlocfilehash: e976ac44bd6d8b2df2cddcab6c068b0f67ba9c3c


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>Armazenando backups do Banco de Dados SQL do Azure por um período de até 10 anos
Muitos aplicativos têm fins regulamentares, de conformidade ou outros fins comerciais que exigem a retenção dos backups automáticos de banco de dados completo, além dos 7-35 dias fornecidos pelos [backups automáticos](sql-database-automated-backups.md) do Banco de Dados SQL.

O recurso **Retenção de Backup de Longo Prazo** permite armazenar seus backups do Banco de Dados SQL do Azure em um cofre dos Serviços de Recuperação do Azure por um período de até 10 anos. Armazene até mil bancos de dados por cofre. Selecione qualquer backup no cofre para restaurá-lo como um novo banco de dados.

> [!NOTE]
> Habilite até 200 bancos de dados por cofre durante um período de 24 horas. Portanto, recomendamos o uso de um cofre separado para cada servidor, a fim de minimizar o impacto desse limite. 
> 
> 

## <a name="how-does-sql-database-long-term-retention-work"></a>Como funciona a Retenção de Longo Prazo do Banco de Dados SQL?

A retenção de longo prazo de backups permite que você associe um servidor de Banco de Dados SQL do Azure a um cofre dos Serviços de Recuperação do Azure. 

* O cofre deve ser criado na mesma assinatura do Azure que criou o SQL Server e na mesma região geográfica e grupo de recursos. 
* Em seguida, você configura uma política de retenção para qualquer banco de dados. A política faz com que os backups semanais de banco de dados completo sejam copiados no cofre dos Serviços de Recuperação e retidos durante o período de retenção especificado (até 10 anos). 
* Em seguida, é possível fazer a restauração de qualquer um destes backups para um novo banco de dados em qualquer servidor na assinatura. A cópia é executada pelo armazenamento do Azure com base em backups existentes e não afeta o desempenho do banco de dados existente.


> [!TIP]
> Para ver um tutorial, confira [Introdução ao Backup e Restauração para Proteção e Recuperação dos Dados usando o Portal do Azure](sql-database-get-started-backup-recovery.md) ou [Introdução ao Backup e Restauração para Proteção e Recuperação dos Dados usando o PowerShell](sql-database-get-started-backup-recovery-powershell.md)

## <a name="how-do-i-enable-long-term-retention"></a>Como faço para habilitar a Retenção de Longo Prazo?

Para configurar a retenção de backup de longo prazo em um banco de dados:

1. Crie um cofre dos Serviços de Recuperação do Azure na mesma região, assinatura e grupo de recursos que o servidor do Banco de Dados SQL. 
2. Registre o servidor no cofre
3. Crie uma Política de Proteção dos Serviços de Recuperação do Azure
4. Aplique a política de proteção aos bancos de dados que exigem a retenção de backup de longo prazo

Para configurar a retenção de longo prazo, confira [configurar retenção de backup de longo prazo](sql-database-configure-long-term-retention.md).

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-retention-feature"></a>Como faço para restaurar um banco de dados armazenado com o recurso Retenção de Longo Prazo?

Para fazer a recuperação com base em um backup de retenção de longo prazo:

1. Liste o cofre em que o backup está armazenado
2. Liste o contêiner que está mapeado para o servidor lógico
3. Liste a fonte de dados no cofre que está mapeada para o banco de dados
4. Liste os pontos de recuperação disponíveis para restauração
5. Restaure do ponto de recuperação para o servidor de destino em sua assinatura

Para recuperar um banco de dados de um backup na retenção de longo prazo, confira [recuperar de um backup em retenção de longo prazo](sql-database-restore-from-long-term-retention.md).

## <a name="how-much-does-long-term-retention-cost"></a>Quanto custa a Retenção de Longo Prazo?

A retenção de longo prazo de um Banco de Dados SQL do Azure é cobrada de acordo com as [taxas de preços dos serviços de backup do Azure](https://azure.microsoft.com/pricing/details/backup/).

Depois que o servidor do Banco de Dados SQL do Azure é registrado no cofre, você é cobrado pelo armazenamento total usado pelos backups semanais armazenados no cofre.

## <a name="view-available-backups-stored-in-long-term-backup-retention"></a>Exibir backups disponíveis armazenados na retenção de backup de longo prazo

Para exibir os backups no cofre dos Serviços de Recuperação do Azure, confira [exibir backups em retenção de longo prazo](sql-database-view-backups-in-vault.md).



## <a name="disabling-long-term-retention"></a>Desabilitando a Retenção de Longo Prazo

O Serviço de Recuperação manipula automaticamente a limpeza dos backups com base na política de retenção fornecida. 

* Para parar de enviar os backups de um banco de dados específico para o cofre, remova a política de retenção do banco de dados.
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> Os backups que já estão no cofre não são afetados. Eles são excluídos automaticamente pelo Serviço de Recuperação quando seu período de retenção expirar.


## <a name="removing-long-term-retention-backups-from-the-azure-recovery-services-vault"></a>Removendo backups de retenção de longo prazo do cofre dos Serviços de Recuperação do Azure

Para remover backups de retenção de longo prazo do cofre, confira [Excluir backups de retenção de longo prazo](sql-database-long-term-retention-delete.md)

## <a name="long-term-retention-faq"></a>Perguntas frequentes sobre a Retenção de Longo Prazo:

1. P: Posso excluir manualmente os backups específicos no cofre?

    R: Não neste momento. O cofre limpa os backups automaticamente quando o período de retenção expirar.
2. P: Posso registrar meu servidor para armazenar backups em mais de um cofre?

    R: Não. Atualmente só possível é armazenar backups em um cofre por vez.
3. P: Posso ter um cofre e o servidor em assinaturas diferentes?

    R: Não, atualmente o cofre e o servidor devem estar na mesma assinatura e no mesmo grupo de recursos.
4. P: Posso usar um cofre que criei em uma região diferente da região do servidor?

    R: Não, o cofre e o servidor devem estar na mesma região para minimizar o tempo de cópia e evitar os encargos de tráfego.
5. P: Quantos bancos de dados posso armazenar em um cofre?

    R: Atualmente, só há suporte para até mil bancos de dados por cofre. 
6. P. Quantos cofres posso criar por assinatura?

    R. Você pode criar até 25 cofres por assinatura.
7. P. Quantos bancos de dados posso configurar por dia e por cofre?

    R. Só é possível configurar até 200 bancos de dados por dia e por cofre.
8. P: A retenção de longo prazo funciona com pools elásticos?

    R: Sim. Qualquer banco de dados no pool pode ser configurado com a política de retenção.
9. P: Posso escolher a hora em que o backup é criado?

    R: Não, o Banco de Dados SQL controla o agendamento de backups para minimizar o impacto no desempenho de seus bancos de dados.
10. P: Tenho a TDE habilitada para meu banco de dados. Pode usar a TDE com o cofre? 

    R. Sim, há suporte para a TDE. Você poderá restaurar o banco de dados do cofre mesmo se o banco de dados original não existir mais.
11. P. O que acontecerá com os backups no cofre se minha assinatura for suspensa? 

    R. Se sua assinatura for suspensa, reteremos os bancos de dados e backups existentes, mas os novos backups não serão copiados no cofre. Depois que você reativar a assinatura, o serviço continua a copiar os backups no cofre. Seu cofre fica acessível para as operações de restauração usando os backups que foram copiados nele antes da suspensão da assinatura. 
12. P: Posso obter acesso aos arquivos de backup do Banco de Dados SQL para baixá-los ou restaurá-los no SQL Server?

    R: Não, não atualmente.
13. P: É possível ter vários Agendamentos (Diário, Semanal, Mensal, Anual) em uma Política de Retenção do SQL?

    R: Não, no momento isto está disponível apenas para backups de Máquina Virtual.
14. P. E se configurarmos o backup de retenção de longo prazo em um banco de dados que é um secundário de replicação geográfica ativa?

    R: Atualmente, não fazemos backups em réplicas, portanto, não há nenhuma opção para backup de retenção de longo prazo em secundários. No entanto, é importante que um cliente configure o backup de retenção de longo prazo em um secundário de replicação geográfica ativa por esses motivos:
    - Quando ocorrer um failover e o banco de dados se tornar primário, faremos um backup completo e esse backup completo será carregado no cofre.
    - Não há custo adicional para o cliente para a configuração de backup de retenção de longo prazo em um secundário.



## <a name="next-steps"></a>Próximas etapas
Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL do Azure, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).




<!--HONumber=Jan17_HO2-->


