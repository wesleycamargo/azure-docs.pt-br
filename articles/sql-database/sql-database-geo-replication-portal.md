<properties 
    pageTitle="Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure com o portal do Azure | Microsoft Azure" 
    description="Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure usando o portal do Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="04/27/2016"
    ms.author="sstein"/>

# Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure com o portal do Azure


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


Este artigo mostra como configurar a Replicação Geográfica para o Banco de Dados SQL usando o [portal do Azure](http://portal.azure.com).

Para iniciar o failover, consulte [Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure](sql-database-geo-replication-failover-portal.md).

>[AZURE.NOTE] Replicação Geográfica Ativa (secundários legíveis) agora está disponível para todos os bancos de dados em todas as camadas de serviço. Em abril de 2017 o tipo de secundário não legível será descontinuado e bancos de dados não legíveis existentes serão automaticamente atualizados para secundários legíveis.

Você pode configurar até 4 bancos de dados secundários legíveis, na mesma localização de centro de dados ou em localizações (regiões) diferentes. Os bancos de dados secundários estão disponíveis no caso de uma paralisação do data center ou da incapacidade de conectar ao banco de dados primário.

Para configurar a Replicação Geográfica, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um Banco de Dados SQL do Azure - O banco de dados primário que você deseja replicar em uma região geográfica diferente.



## Adicionar banco de dados secundário

As etapas a seguir criam um novo banco de dados secundário em uma parceria de Replicação Geográfica.

Para adicionar um secundário, você deverá ser o proprietário ou o co-proprietário da assinatura.

O banco de dados secundário terá o mesmo nome do banco de dados primário e, por padrão, terá o mesmo nível de serviço. O banco de dados secundário pode ser legível ou não legível, e pode ser um único banco de dados ou um banco de dados elástico. Para saber mais, confira [Camadas de Serviço](sql-database-service-tiers.md). Depois do banco de dados secundário ser criado e propagado, os dados começarão a ser replicados desde o banco de dados primário até o novo banco de dados secundário.

> [AZURE.NOTE] O comando falhará se o banco de dados parceiro já existir (por exemplo, como resultado do encerramento de um relacionamento de Replicação Geográfica anterior).




### Adicionar secundário

1. No [Portal do Azure](http://portal.azure.com), procure o banco de dados que você deseja configurar para a Replicação Geográfica.
2. Na folha Banco de Dados SQL, selecione **Todas as configurações** > **Replicação Geográfica**.
3. Selecione a região para criar o banco de dados secundário.


    ![Adicionar secundário][1]


4. Configure o **Tipo de secundário** (**Legível** ou **Não legível**).
5. Selecione ou configure o servidor do banco de dados secundário.

    ![Criar Secundário][3]

5. Opcionalmente, você pode adicionar um banco de dados secundário a um pool de banco de dados elástico:

       - Clique em **Pool de banco de dados elástico** e selecione um pool no servidor de destino em que será criado o banco de dados secundário. Um pool já deverá existir no servidor de destino, já que este fluxo de trabalho não cria um novo pool.

6. Clique em **Criar** para adicionar o secundário.
 
6. O banco de dados secundário é criado e começa o processo de propagação.
 
    ![propagação][6]

7. Quando o processo de propagação for concluído, o banco de dados secundário exibirá seu status (não legível).

    ![secundário pronto][9]



## Remover banco de dados secundário

A operação encerra permanentemente a replicação para o banco de dados secundário e altera a função do secundário para um banco de dados de leitura/gravação normal. Se a conectividade com o banco de dados secundário for interrompida, o comando terá êxito, mas o secundário não se tornará de leitura/gravação até a conectividade ser restaurada.

1. No [Portal do Azure](http://portal.azure.com), procure o banco de dados primário na parceria de Replicação Geográfica.
2. Na folha Banco de Dados SQL, selecione **Todas as configurações** > **Replicação Geográfica**.
3. Na lista **SECUNDÁRIOS**, selecione o banco de dados que você deseja remover da parceria de Replicação Geográfica.
4. Clique em **Parar Replicação**.

    ![remover secundário][7]


5. Clicar em **Parar Replicação** abre uma janela de confirmação, portanto clique em **Sim** para remover o banco de dados da parceria de Replicação Geográfica (defina-o como um banco de dados de leitura/gravação que não faz parte de nenhuma replicação).


    ![confirmar remoção][8]



   

## Próximas etapas

- [Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure](sql-database-geo-replication-failover-portal.md)
- [Projetando aplicativos de nuvem para continuidade de negócios usando a Replicação Geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Executar análise de recuperação de desastres](sql-database-disaster-recovery-drills.md)


## Recursos adicionais

- [Configuração de segurança para a Replicação Geográfica](sql-database-geo-replication-security-config.md)
- [Destacar novos recursos de Replicação Geográfica](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [Perguntas frequentes sobre BCDR no Banco de Dados SQL](sql-database-bcdr-faq.md)
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- [Criando aplicativos para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalizar seu Banco de Dados SQL do Azure recuperado](sql-database-recovered-finalize.md)


<!--Image references-->
[1]: ./media/sql-database-geo-replication-portal/configure-geo-replication.png
[2]: ./media/sql-database-geo-replication-portal/add-secondary.png
[3]: ./media/sql-database-geo-replication-portal/create-secondary.png
[4]: ./media/sql-database-geo-replication-portal/secondary-type.png
[5]: ./media/sql-database-geo-replication-portal/create.png
[6]: ./media/sql-database-geo-replication-portal/seeding0.png
[7]: ./media/sql-database-geo-replication-portal/remove-secondary.png
[8]: ./media/sql-database-geo-replication-portal/stop-confirm.png
[9]: ./media/sql-database-geo-replication-portal/seeding-complete.png
[10]: ./media/sql-database-geo-replication-portal/failover.png

<!---HONumber=AcomDC_0608_2016-->