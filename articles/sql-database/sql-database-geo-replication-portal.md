<properties 
    pageTitle="Configurar a replicação geográfica para o Banco de Dados SQL do Azure com o portal do Azure | Microsoft Azure" 
    description="Configurar a replicação geográfica para o Banco de Dados SQL do Azure usando o portal do Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="12/01/2015"
    ms.author="sstein"/>

# Configurar a replicação geográfica para o Banco de Dados SQL do Azure com o portal do Azure


> [AZURE.SELECTOR]
- [Azure portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


Este artigo mostra como configurar a replicação geográfica para o Banco de Dados SQL usando o [portal do Azure](https://portal.azure.com).

A replicação geográfica permite criar até quatro bancos de dados de réplica (secundários) em diferentes locais do datacenter (regiões). Os bancos de dados secundários estão disponíveis no caso de uma paralisação do data center ou da incapacidade de conectar ao banco de dados primário.

A replicação geográfica só está disponível para os bancos de dados Standard e Premium.

Os bancos de dados Standard podem ter um secundário não legível e devem usar a região recomendada. Os bancos de dados Premium podem ter até quatro secundários legíveis em qualquer uma das regiões disponíveis.


Para configurar a replicação geográfica, você precisará do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um Banco de Dados SQL do Azure - O banco de dados primário que você deseja replicar em uma região geográfica diferente.



## Adicionar banco de dados secundário

As etapas a seguir criam um novo banco de dados secundário em uma parceria de replicação geográfica.

Para adicionar um secundário, você deverá ser o proprietário ou o co-proprietário da assinatura.

O banco de dados secundário terá o mesmo nome do banco de dados primário e, por padrão, terá o mesmo nível de serviço. O banco de dados secundário pode ser legível (somente a camada Premium) ou não legível, e pode ser um banco de dados individual ou um banco de dados elástico. Para saber mais, confira [Camadas de Serviço](sql-database-service-tiers.md). Depois do banco de dados secundário ser criado e propagado, os dados começarão a ser replicados desde o banco de dados primário até o novo banco de dados secundário.

> [AZURE.NOTE] O comando falhará se o banco de dados parceiro já existir (como, por exemplo, como resultado do encerramento de um relacionamento de replicação geográfica anterior).




### Adicionar secundário

1. No [Portal do Azure](https://portal.azure.com), procure o banco de dados que você deseja configurar para a replicação geográfica.
2. Na folha Banco de Dados SQL, selecione **Todas as configurações** > **Replicação Geográfica**.
3. Selecione a região para criar o banco de dados secundário. Os bancos de dados Premium podem usar qualquer região para um secundário; os bancos de dados Standard devem usar a região recomendada:


    ![Adicionar secundário][1]


4. Configure o **Tipo secundário** (**Legível**, ou **Não Legível**); somente os bancos de dados Premium podem ter secundários legíveis, os bancos de dados secundários do Standard só podem ser definidos como **Não Legíveis**.
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

1. No [Portal do Azure](https://portal.azure.com), procure o banco de dados primário na parceria de replicação geográfica.
2. Na folha Banco de Dados SQL, selecione **Todas as configurações** > **Replicação Geográfica**.
3. Na lista **SECUNDÁRIOS**, selecione o banco de dados que você deseja remover da parceria de replicação geográfica.
4. Clique em **Parar Replicação**.

    ![remover secundário][7]


5. Clicar em **Parar Replicação** abre uma janela de confirmação, portanto clique em **Sim** para remover o banco de dados da parceria de replicação geográfica (defini-lo como um banco de dados de leitura/gravação não faz parte de qualquer replicação).


    ![confirmar remoção][8]



## Iniciar um failover

O banco de dados secundário pode ser alternado para se tornar primário.

1. No [Portal do Azure](https://portal.azure.com), procure o banco de dados primário na parceria de replicação geográfica.
2. Na folha Banco de Dados SQL, selecione **Todas as configurações** > **Replicação Geográfica**.
3. Na lista **SECUNDÁRIOS**, selecione o banco de dados que deverá se tornar o novo primário.
4. Clique em **Failover**.

    ![failover][10]

O comando executa o seguinte fluxo de trabalho:

1. Alterne temporariamente a replicação para o modo síncrono. Isso fará com que todas as transações pendentes sejam liberadas para o secundário. 

2. Alterne as funções primária e secundária dos dois bancos de dados na parceria de replicação geográfica.

Para o failover planejado, esta sequência garante que não ocorrerá nenhuma perda de dados. Há um breve período durante o qual os bancos de dados não estão disponíveis (na ordem de 0 a 25 segundos) enquanto as funções são alternadas. A operação inteira deve levar menos de um minuto para ser concluída em circunstâncias normais.

   

## Próximas etapas

- [Projetando aplicativos de nuvem para a continuidade de negócios usando a replicação geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Executar análise de recuperação de desastres](sql-database-disaster-recovery-drills.md)


## Recursos adicionais

- [Destacar os novos recursos de replicação geográfica](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [Projetando aplicativos de nuvem para a continuidade de negócios usando a replicação geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)


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

<!---HONumber=AcomDC_0128_2016-->