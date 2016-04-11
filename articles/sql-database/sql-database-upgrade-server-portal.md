<properties
	pageTitle="Atualização para o Banco de Dados SQL V12 do Azure usando o portal do Azure | Microsoft Azure"
	description="Explica como atualizar para o Banco de Dados SQL V12 do Azure incluindo como atualizar bancos de dados da Web e Empresarial e como atualizar um servidor V11 migrando seus bancos de dados diretamente para um pool de banco de dados elástico usando o portal do Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="02/23/2016"
	ms.author="sstein"/>


# Atualização para o Banco de Dados SQL V12 do Azure usando o portal do Azure


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


O V12 de banco de dados SQL é a versão mais recente para atualizar para o V12 do banco de dados do SQL que é recomendado. O V12 do banco de dados SQL tem muitas [vantagens em relação à versão anterior](sql-database-v12-whats-new.md) incluindo:

- Maior compatibilidade com o SQL Server.
- Desempenho premium melhorado e novos níveis de desempenho.
- [Pools de banco de dados elásticos](sql-database-elastic-pool.md).

Esse artigo fornece instruções para atualizar os servidores existentes V11 de banco de dados SQL e bancos de dados para V12 de banco de dados SQL.

Durante o processo de atualização para V12 você vai atualizar qualquer banco de dados da Web e Business para uma nova camada de serviço para que instruções atualizem o bancos de dados da Web e Business que estão incluídos.

Além disso, migrar para um [pool de banco de dados elástico](sql-database-elastic-pool.md) pode ser mais econômico do que a atualização para níveis de desempenho individual (faixas de preço) para bancos de dados únicos. Os pools também simplificam o gerenciamento de banco de dados porque você só precisa gerenciar as configurações de desempenho para o pool em vez de gerenciar separadamente os níveis de desempenho de bancos de dados individuais. Se você tiver bancos de dados em vários servidores, considere movê-los para o mesmo servidor, aproveitando para colocá-los em um pool. Você pode facilmente [migrar automaticamente dos servidores V11 diretamente para pools de banco de dados elásticos usando o PowerShell](sql-database-upgrade-server-powershell.md). Você também pode usar o portal para migrar os bancos de dados V11 para um pool, mas no portal, você já deve ter um servidor V12 para criar um pool. Instruções são fornecidas mais tarde neste artigo para criar o pool após a atualização do servidor se você tiver [bancos de dados que podem se beneficiar de um pool](sql-database-elastic-pool-guidance.md).

Observe que os bancos de dados permanecerão online e continuarão a trabalhar em toda a operação de atualização. No momento da transição real para o novo nível de desempenho temporário podem ocorrer quedas das conexões ao banco de dados com duração muito curtas que é normalmente em torno de 90 segundos, mas pode ser até 5 minutos. Se seu aplicativo [tiver um tratamento de falha transitória para encerramentos de conexão](sql-database-connect-central-recommendations.md), isso será suficiente para proteger contra a queda de conexões ao final da atualização.

A atualização para o V12 do banco de dados SQL não poderá ser desfeita. Após uma atualização, o servidor não pode ser revertido para V11.

Após a atualização para V12, as [recomendações de camada de serviço](sql-database-service-tier-advisor.md) e as [considerações de desempenho do pool elástico](sql-database-elastic-pool-guidance.md) não estarão imediatamente disponíveis até que o serviço tenha tempo para avaliar suas cargas de trabalho no novo servidor. O histórico de recomendação do servidor V11 não se aplica ao servidores V12, portanto não é mantido.

## Prepare-se para atualizar

- **Atualizar todos os bancos de dados da Web e Business**: veja a seção [Atualize todos os bancos de dados da Web e Empresarial](sql-database-upgrade-server-portal.md#upgrade-all-web-and-business-databases) abaixo ou veja [Monitorar e gerenciar um pool de banco de dados elástico (PowerShell)](sql-database-elastic-pool-manage-powershell.md).
- **Examinar e suspender a replicação geográfica:** se o seu Banco de Dados SQL do Azure estiver configurado para replicação geográfica, você deverá documentar sua configuração atual e [parar a replicação geográfica](sql-database-geo-replication-portal.md#remove-secondary-database). Após a conclusão da atualização, você deve reconfigurar seu banco de dados para replicação geográfica.
- **Abra estas portas se você tiver clientes em uma VM do Azure**: se o seu programa cliente se conectar ao Banco de Dados SQL V12 enquanto seu cliente for executado em uma máquina virtual do Azure (VM), abra os seguintes intervalos de porta 11000-11999 e 14000-14999 na VM. Para obter detalhes, confira [Portas para Banco de dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).



## Inicie a atualização

1. No [Portal do Azure](https://portal.azure.com/), navegue até o servidor que deseja atualizar selecionando **PROCURAR TUDO** > **SQL Servers** e selecionando o servidor desejado.
2. Selecione **Atualização mais recente do banco de dados SQL** e, em seguida, selecione **Atualizar este servidor**.

      ![atualizar servidor][1]

## Atualize todos os bancos de dados da Web e Empresarial

Se o seu servidor tiver qualquer banco de dados da Web ou Empresarial, é necessário atualizá-los. Durante o processo de atualização para o Banco de Dados SQL V12, você também deverá atualizar todos os bancos de dados da Web e Empresarial para uma nova camada de serviço.

Para ajudá-lo com a atualização, o serviço do Banco de Dados SQL recomenda uma camada de serviço e nível de desempenho (tipo de preço) adequados para cada banco de dados. O serviço recomenda uma camada que é mais adequada para a execução da carga de trabalho existente do banco de dados analisando o uso do histórico do banco de dados.

3. Na folha **Atualizar este servidor**, selecione cada banco de dados para examinar e selecionar para qual camada de preços recomendada ele será atualizado. Também é possível procurar vários tipos de preço e selecionar aquele que se adapte melhor ao seu ambiente.


     ![databases][2]


7. Depois de clicar na camada sugerida, será exibida a folha **Escolha sua camada de preços**, em que é possível clicar em uma camada e clicar no botão **Selecionar** para alterar para a camada em questão. Selecione uma nova camada para cada banco de dados da Web ou Empresarial.

    O que é importante observar, é que o seu banco de dados SQL não está bloqueado em nenhuma camada de serviço ou nível de desempenho específicos, de forma que os requisitos de sua alteração de banco de dados podem ser alterados facilmente entre as camadas de serviço e níveis de desempenho disponíveis. Na realidade, os Bancos de Dados SQL Basic, Standard e Premium do Azure são cobrados por hora, e você tem a capacidade de dimensionar cada banco de dados para cima ou para baixo quatro vezes dentro de um período de 24 horas.

    ![filmes][6]


Depois que todos os bancos de dados no servidor forem qualificados, você estará pronto para iniciar a atualização

## Confirmar a atualização

3. Quando todos os bancos de dados no servidor forem qualificados para a atualização, você precisará **DIGITAR O NOME DO SERVIDOR** para verificar que deseja executar a atualização e, em seguida, clicar em **OK**.

    ![verificar atualização][3]


4. A atualização é iniciada e exibe a notificação em andamento. O processo de atualização é iniciado. Dependendo dos detalhes de seus bancos de dados específicos, a atualização para o V12 pode levar algum tempo. Durante esse tempo, todos os bancos de dados no servidor permanecerão online, mas as ações de gerenciamento de servidor e de banco de dados serão restringidas.

    ![atualização em andamento][4]

    No momento da transição real para o novo nível de desempenho temporário podem ocorrer quedas das conexões com duração muito curtas (em segundos) com o banco de dados. Se um aplicativo tiver um tratamento de falha transitória (lógica de repetição) para encerramentos de conexão, isto será suficiente para proteger contra a queda de conexões ao final da atualização.

5. Após a conclusão da operação de atualização da **Atualização mais recente**, a folha exibirá **Habilitado**.

    ![V12 habilitado][5]

## Mover os bancos de dados em um pool de banco de dados elástico

No [Portal do Azure](https://portal.azure.com/), procure o servidor V12 e clique em **Adicionar pool**.

-ou-

Se você receber uma mensagem dizendo **Clique aqui para ver os pools de banco de dados elásticos recomendados para este servidor**, clique nela para criar facilmente um pool que é otimizado para os bancos de dados do seu servidor. Para obter detalhes, veja [Considerações de preço e desempenho para um pool de banco de dados elástico](sql-database-elastic-pool-guidance.md).

![Adicionar pool a um servidor][7]

Siga as instruções no artigo [Criar um pool de banco de dados elástico](sql-database-elastic-pool.md) para terminar de criar o pool.

## Monitorar bancos de dados após a atualização para V12 do banco de dados SQL

>[AZURE.IMPORTANT] Atualize para a versão mais recente do SSMS (SQL Server Management Studio) para tirar proveito dos novos recursos v12. [Baixe o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

Após a atualização, monitore o banco de dados ativamente para garantir que os aplicativos estão sendo executados no desempenho esperado e otimize as configurações conforme necessário.

Além de monitorar os bancos de dados individuais, você pode monitorar os pools de banco de dados elástico [Monitorar, gerenciar e dimensionar um pool de banco de dados elástico com o Portal do Azure](sql-database-elastic-pool-manage-portal.md) ou com o [PowerShell](sql-database-elastic-pool-powershell.md#monitoring-elastic-databases-and-elastic-database-pools).


**Dados de consumo de recursos:** para recursos de banco de dados Básico, Standard e Premium, os dados de consumo estão disponíveis através do DMV [sys.dm\_ db\_ resource\_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) no banco de dados do usuário. Essa DMV oferece, quase em tempo real, informações sobre consumo de recurso na granularidade a 15 segundos para a hora da operação anterior. O consumo de percentual de DTU para um intervalo é calculado como o consumo de percentual máxima das dimensões de CPU, E/S e log. Aqui está uma consulta para calcular o consumo médio de porcentagem de DTU na última hora:

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Informações adicionais de monitoramento:

- [Diretrizes de desempenho do Banco de Dados SQL do Azure para bancos de dados individuais](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Considerações de preço e desempenho para um pool de banco de dados elástico](sql-database-elastic-pool-guidance.md).
- [Monitoramento de Banco de Dados SQL usando exibições de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md)




**Alertas:** configurar “Alertas” no Portal do Azure para ser notificado quando o consumo de DTU de um banco de dados atualizado aproximar-se de certo nível alto. Alertas de banco de dados podem ser configurados no Portal do Azure para várias métricas de desempenho como Log, CPU, E/S e DTU. Navegue até o banco de dados e selecione **Regras de alerta** na folha **Configurações**.

Por exemplo, você pode configurar um alerta por email sobre “Porcentagem DTU” se o valor da porcentagem média de DTU exceder 75% nos últimos 5 minutos. Confira [Receber notificações de alerta](../azure-portal/insights-receive-alert-notifications.md) para saber mais sobre como configurar notificações de alerta.





## Próximas etapas

- [Verificar as recomendações de pool e criar um pool](sql-database-elastic-pool-create-portal.md).
- [Alterar a camada de serviço e o nível de desempenho do banco de dados](sql-database-scale-up.md).



## Links relacionados

- [Novidades no Banco de Dados SQL V12](sql-database-v12-whats-new.md)
- [Planejar e se preparar para atualizar para o Banco de Dados SQL V12](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-upgrade-server-portal/latest-sql-database-update.png
[2]: ./media/sql-database-upgrade-server-portal/upgrade-server2.png
[3]: ./media/sql-database-upgrade-server-portal/upgrade-server3.png
[4]: ./media/sql-database-upgrade-server-portal/online-during-upgrade.png
[5]: ./media/sql-database-upgrade-server-portal/enabled.png
[6]: ./media/sql-database-upgrade-server-portal/recommendations.png
[7]: ./media/sql-database-upgrade-server-portal/new-elastic-pool.png

<!---HONumber=AcomDC_0330_2016-->