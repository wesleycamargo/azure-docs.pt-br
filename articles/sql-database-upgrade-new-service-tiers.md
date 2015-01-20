<properties title="Upgrade SQL Database Web/Business Databases to New Service Tiers" pageTitle="Atualizar bancos de dados do SQL banco de dados Web/Business para novas camadas de serviço" description="Atualize banco de dados da Web do SQL Azure ou banco de dados de negócios para os novos níveis de camadas/desempenho do serviço do banco de dados do SQL do Azure." metaKeywords="Azure, SQL. DB, Service, Tier, web, business" services="Azure SQL DB" solutions="upgrade, migrate, move, monitor, t-sql, powershell" documentationCenter="" authors="Srini Acharya, Umachandar Jayachandran" manager="jeffreyg, jhubbard" videoId="" scriptId="" />

<tags ms.service="sql-database" ms.date="12/3/2014" ms.author="jhubbard" />


#Atualizar bancos de dados do SQL banco de dados Web/Business para novas camadas de serviço

<p> Bancos de dados da Web do Azure e SQL de negócios executados em um ambiente compartilhado multilocatário sem qualquer capacidade de recursos reservada para o banco de dados. A atividade de outros bancos de dados em seu cluster pode afetar o desempenho. Disponibilidade do recurso a qualquer momento determinado depende muito de outras cargas de trabalho simultâneas em execução no sistema. Isso pode resultar em desempenho de aplicativos de banco de dados imprevisíveis e altamente variável. Comentários dos clientes são que esse desempenho imprevisível é difícil de gerenciar e preferem um desempenho mais previsível. 

Para resolver esses comentários, o serviço de banco de dados SQL do Azure introduziu novas camadas de serviço de banco de dados [(Basic, Standard e Premium)](http://azure.microsoft.com/blog/2014/08/26/new-azure-sql-database-service-tiers-generally-available-in-september-with-reduced-pricing-and-enhanced-sla/ ), que oferecem um desempenho previsível e uma infinidade de novos recursos de segurança e continuidade de negócios. Essas novas camadas de serviço são projetadas para fornecer um nível especificado de recursos para uma carga de trabalho de banco de dados, independentemente de outras cargas de trabalho do cliente em execução no ambiente. Isso resulta em um desempenho altamente previsível. Como parte desse lançamento, as camadas de serviço Web e Business estão sendo substituídas e desativadas em setembro de 2015. 

Com essas alterações surgem perguntas sobre como avaliar e decidir qual nova camada de serviço é a melhor adequada para seus bancos de dados Web e Business (W/B) atuais e sobre o próprio processo de atualização real. Este documento fornece uma metodologia orientada para atualizar o banco de dados Web/Business para os novos níveis de camadas e desempenho do serviço.

A atualização de um banco de dados para a nova camada de serviço Web/Business envolve as seguintes etapas:

1.	Determinar a camada de serviço que fornece o nível mínimo de capacidade de recursos necessários para seus requisitos de aplicativo ou de negócios
2.	Determinar o nível de desempenho com base no histórico do uso de recursos
3.	Atualizar para o nível de desempenho/camada de serviço escolhida
4.	Monitorar o banco de dados após a atualização e ajustar o nível de desempenho/camada de serviço conforme necessário

## 1. Determinar a camada de serviço com base na capacidade de recurso

Determinar a camada de serviço que fornece o nível mínimo de capacidade de recursos necessários para o requisitos do seu aplicativo ou negócios. Por exemplo, considere seus requisitos como quanto tempo os backups são mantidos OU se os recursos de replicação geográfica ativos Standard são necessários OU tamanho total máximo necessário do banco de dados, etc. Esses requisitos terão impacto sobre a sua escolha de camada de serviço mínima (consulte o [blog do comunicado de camada de serviço). ](http://azure.microsoft.com/blog/2014/08/26/new-azure-sql-database-service-tiers-generally-available-in-september-with-reduced-pricing-and-enhanced-sla/ ) 

A camada 'Básica' é usada principalmente para bancos de dados muito pequenos e de baixa atividade. Portanto, para uma atualização você deve geralmente começar com a camada "Standard" ou "Premium" com base em seus recursos ao escolher a camada de serviço. Consulte a [documentação do Azure](http://msdn.microsoft.com/pt-br/library/azure/dn741336.aspx) para os limites específicos de nível de desempenho/camada de serviço para solicitações/sessões simultâneas.

##2. Determinar o nível de desempenho com base no uso de recursos 
O serviço de banco de dados SQL do Azure fornece o consumo de recursos de banco de dados Web/Business na exibição sys.resource_stats [(consulte a documentação do MSDN)](http://msdn.microsoft.com/library/azure/dn269979.aspx) no banco de dados mestre do servidor lógico onde se encontra o banco de dados atual. Ele exibe dados de consumo de recursos em porcentagem do limite do nível de desempenho. Essa exibição fornece dados para até os últimos 14 dias, em intervalos de 5 minutos. Como bancos de dados Web/Business não possuem os limites de DTU e recursos garantidos associados a eles, podemos normalizar os valores de porcentagem em termos da quantidade de recursos disponíveis para um banco de dados em nível de desempenho S2. O consumo médio de porcentagem DTU de um banco de dados em qualquer intervalo específico pode ser calculado como o valor mais alto de porcentagem entre o uso da CPU, e/s e Log nesse intervalo. Execute a seguinte consulta no banco de dados mestre para recuperar o consumo médio de DTU de um banco de dados:

 
                   
     SELECT start_time, end_time
	 ,(SELECT Max(v)
         FROM (VALUES (avg_cpu_percent)
                    , (avg_physical_data_read_percent)
                    , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.resource_stats
    WHERE database_name = '<your db name>'
    ORDER BY end_time DESC;

Informações sobre consumo de DTU em termos de um nível de banco de dados S2 permite normalizar o consumo atual dos bancos de dados Web/Business em termos de novos bancos de dados de camada e ver onde eles se ajustam melhor. Por exemplo, se o consumo médio de porcentagem DTU mostra um valor de 80%, isso indica que o banco de dados está consumindo DTU na taxa de 80% do limite de um banco de dados no nível de desempenho S2. Se você vir valores superiores a 100% na exibição sys.resource_stats, isso significa que você precisa de um nível de desempenho maior que o S2. Por exemplo, digamos que você vê um valor de porcentagem DTU de pico de 300%.  Isso indica que você está usando três vezes mais recursos que estariam disponíveis em um S2.  Para determinar um tamanho inicial razoável, compare os DTU disponíveis em um S2 (50 DTU) com os tamanhos maiores próximos (P1 = 100 DTU, ou 200% de S2, P2 = 200 DTU ou 400% do S2). Porque você está em 300% do S2 convém começar com um P2 e testar novamente.

Com base no percentual de uso DTU e a edição maior do que era necessário de acordo com sua carga de trabalho, você pode determinar qual o nível de desempenho e da camada de serviço mais adequada para sua carga de trabalho do banco de dados (conforme indicado por meio de porcentagem DTU e potência DTU relativa de vários [níveis de desempenho).](http://msdn.microsoft.com/library/azure/dn741336.aspx) Aqui está uma tabela que fornece um mapeamento da porcentagem de consumo de recursos Web/Business para os níveis de desempenho equivalente da nova camada: 

![resource consumption](http://i.imgur.com/nPodQZH.png)

> **Observação**: Números DTU relativos entre vários níveis de desempenho são baseados no [Padrão de referência de cargas de trabalho de banco de dados SQL do Azure](http://msdn.microsoft.com/library/azure/dn741327.aspx). Como a carga de trabalho do seu banco de dados provavelmente será diferente do padrão de referência, você deve usar os cálculos acima como orientação para um ajuste inicial do banco de dados Web/Business nas novas camadas. Depois de mover o banco de dados para a nova camada, use o processo descrito na seção anterior para validar e ajustar a camada de serviço correta que atenda as suas necessidades de carga de trabalho.
> 
> **Observação**: Embora o nível de desempenho/camada da nova edição sugerida leva em consideração a atividade de banco de dados ao longo dos últimos 14 dias, esses dados baseiam-se em amostras de consumo de recursos com a média superior a 5 minutos. Como tal, ele poderá perder intermitências de curto prazo de atividade menores do que 5 minutos de duração. Assim, este guia deve ser usado como um ponto de partida para atualizar seu banco de dados. Depois de atualizar o banco de dados para a camada sugerida, mais monitoramento, testes e validações serão necessários e o banco de dados pode ser movido para cima/para baixo até um nível diferente de camada e desempenho conforme necessário.

Aqui está uma consulta no banco de dados mestre que executa o cálculo do banco de dados da camada Web/Business e sugere qual edição provavelmente é a mais adequada de acordo com sua carga de trabalho para cada um desses intervalos de amostra de dados de 5 minutos.

> **Observação:** esta consulta é útil somente para bancos de dados Web/Business e não **fornecerá** resultados corretos para bancos de dados nas novas camadas.

    WITH DTU_mapping AS
    ( SELECT *
        FROM ( VALUES (1, 10,'Basic'), (2, 20,'S0'), (3, 40,'S1'), (4, 100, 'S2')
                    , (5, 200, 'P1'), (6, 400,'P2'), (7, 1600, 'P3')
           ) AS t(id, percent_of_S2, target_edition)
    ), rc as
    ( SELECT start_time, end_time
           , (SELECT Max(v)
                FROM (VALUES (avg_cpu_percent)
                       		, (avg_physical_data_read_percent)
                       		, (avg_log_write_percent)
                   ) AS value(v)) as [avg_DTU_percent]
        FROM sys.resource_stats	
       WHERE database_name = 'WebDB'
    )
    SELECT rc.*
         , (SELECT TOP(1) t.target_edition
              FROM DTU_mapping AS t
             WHERE t.percent_of_S2 > CAST(1.2*rc.avg_DTU_percent as int)
             ORDER BY t.percent_of_S2) as target_edition
    FROM rc;

**Resultado de exemplo:**

![Sample Result](http://i.imgur.com/CTnjv26.png)

No gráfico, você pode ver a tendência de consumo do percentual médio de DTU ao longo do tempo. Aqui está um exemplo de gráfico para um banco de dados de um nível S2 na maioria do tempo, com alguma atividade de pico atingindo um nível de banco de dados P1.  O consumo de DTU ao longo do tempo varia dos limites do "Basic" até os limites do "P1". Para ajustar completamente esse banco de dados na nova camada, será necessário um banco de dados com camada de serviço Premium com nível de desempenho "P1". Por outro lado, um banco de dados de nível S2 pode funcionar se esses picos ocasionais para o nível P1 são raros.

![DTU Usage](http://i.imgur.com/e4N4ay5.png)

##3. Atualizar para uma nova camada de serviço/nível de desempenho
Após determinar sua camada de serviço e nível de desempenho para o banco de dados Web/Business, há várias maneiras de atualizar o banco de dados para a nova camada:


- Portal de Gerenciamento do Azure
- PowerShell do Azure 
- [API REST de gerenciamento de serviço](http://msdn.microsoft.com/pt-br/library/azure/dn505719.aspx)
- Transact-SQL 

Para a migração ad hoc de bancos de dados Web/Business, use o [Portal de gerenciamento do Azure](https://manage.windowsazure.com/). Consulte a seção "Atualizar para uma camada de serviço superior" do artigo [Alterando camadas de serviço e níveis de desempenho de banco de dados](http://msdn.microsoft.com/pt-br/library/azure/dn369872.aspx) para conhecer as etapas para atualizar usando o Portal de gerenciamento do Azure.

Para executar a operação em vários bancos de dados em um servidor/assinatura ou para automatizar o processo, use os comandos do PowerShell ou Transact-SQL.

##Atualizar usando o PowerShell do Azure
Você pode usar o exemplo de ponta a ponta para atualizar um banco de dados Web/Business usando o PowerShell do Azure abaixo. As etapas para atualizar um banco de dados são as seguintes:

1. Definir o contexto do servidor com o cmdlet **New-AzureSqlDatabaseServerContext** 
2. Obter o banco de dados a ser atualizado com o cmdlet **Get-AzureSqlDatabase**
3. Obter o nível de desempenho desejado com base no nome (exemplo: S1, S2, P1) usando **Set-AzureSqlDatabase - ServiceObjective**
4. Use o **Set-AzureSqlDatabase** para especificar a nova camada de serviço e nível de desempenho do banco de dados

Um exemplo de script para atualizar um servidor e um banco de dados para o nível de desempenho/camada de serviço desejada é mostrado abaixo:

    # Get the credential for the server. Provide login that is in dbmanager role of server
    $credential = Get-Credential
    # Provide server name in the cmdlet below:
    $serverContext = New-AzureSqlDatabaseServerContext -ServerName "<server_name>" -Credential $credential
    # Provide database name in the cmdlet below:
    $db = Get-AzureSqlDatabase $serverContext -DatabaseName "<database_name>"
    $service_tier = "<desired_service_tier>"
    $performance_level = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "<desired_performance_level>"
    Set-AzureSqlDatabase $serverContext -Database $db -ServiceObjective $performance_level -Edition $service_tier


## Atualizar usando Transact-SQL

Para atualizar um banco de dados Web/Business existente usando o Transact-SQL, você pode usar a 
[Instrução ALTER DATABASE](http://msdn.microsoft.com/library/azure/ms174269.aspx). O comando ALTER DATABASE para atualizar um banco de dados para o novo nível de desempenho e camada de serviço é mostrado abaixo:

    ALTER DATABASE <db_name> MODIFY (EDITION = '<service_tier>', SERVICE_OBJECTIVE = '<performance_level>');
      	 

##4.	Monitoramento da atualização da nova camada de serviço e nível de desempenho
O banco de dados SQL do Azure fornece informações sobre o andamento em operações de gerenciamento (como CREATE, ALTER, DROP) executadas em um banco de dados na exibição de gerenciamento dinâmico sys.dm_operation_status no banco de dados mestre do servidor lógico onde seu banco de dados atual está localizado [Consulte a documentação do sys.dm_operation_status.](http://msdn.microsoft.com/library/azure/dn270022.aspx) Use o status da operação DMV para determinar o progresso da operação de atualização de um banco de dados. Este exemplo de consulta mostra todas as operações de gerenciamento executadas em um banco de dados:

    SELECT o.operation, o.state_desc, o.percent_complete
    , o.error_code, o.error_desc, o.error_severity, o.error_state
    , o.start_time, o.last_modify_time
    FROM sys.dm_operation_status AS o
    WHERE o.resource_type_desc = 'DATABASE'
    and o.major_resource_id = '<database_name>'
    ORDER BY o.last_modify_time DESC;

Se você usou o portal de gerenciamento para a atualização, uma notificação também está disponível a partir do portal para a operação.

## Impacto da atualização
A atualização de bancos de dados Web/Business para as novas camadas de serviço e níveis de desempenho é uma operação online. O banco de dados continuará a trabalhar durante a operação de atualização. No momento da transição real para o novo nível de desempenho temporário podem ocorrer quedas das conexões com duração muito curtas (em segundos) com o banco de dados. Se um aplicativo tiver um tratamento de falha transitória para encerramentos de conexão, então isto é suficiente para proteger contra a queda de conexões no final da atualização. Para obter as práticas recomendadas sobre como tratar encerramentos de conexão, consulte [Práticas recomendadas do banco de dados SQL do Azure para evitar negações de solicitação ou encerramento de conexão.](http://msdn.microsoft.com/library/azure/dn338082.aspx)

Para obter mais detalhes sobre como estimar a latência da operação de atualização, consulte a seção "Compreendendo e estimando a latência em alterações de banco de dados" de [Alteração das camadas de serviço e níveis de desempenho de banco de dados.](http://msdn.microsoft.com/library/azure/dn369872.aspx)

##O que acontece quando a carga de trabalho de banco de dados atinge os limites de recursos após a atualização?
Os níveis de desempenho são calibrados e controlados para fornecer os recursos necessários para executar sua carga de trabalho de banco de dados até os limites máximos permitidos para a sua camada de serviço e nível de desempenho selecionados (ou seja, o consumo de recursos é de 100%). Se a sua carga de trabalho está atingindo os limites em um dos limites de CPU/E/S de dados/E/S de Log, você continuará a receber os recursos no nível máximo permitido, mas provavelmente você observará latências maiores para suas consultas. O alcance de um desses limites não resultará em nenhum erro, mas apenas uma lentidão na carga de trabalho, a menos que a lentidão se torne tão grave que as consultas comecem a não serem executadas a tempo. Se você está atingindo os limites máximos permitidos de sessões/solicitações de usuário simultâneas (threads de trabalho), você verá erros explícitos (consulte a documentação do MSDN) para todas as conexões/solicitações adicionais.

##Monitoramento do banco de dados após a atualização
Após a atualização do banco de dados Web/Business para a nova camada, é recomendável monitorar o banco de dados ativamente para garantir que aplicativos estão sendo executados no desempenho esperado e uso ideal conforme necessário. As seguintes etapas adicionais são recomendáveis para monitorar o banco de dados.


**Dados de consumo de recursos:** para bancos de dados Basic, Standard e Premium recursos mais granulares de consumo de dados estão disponíveis por meio de um novo DMV chamado [sys.dm_db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) no banco de dados do usuário. Essa DMV oferece informações quase em tempo real com frequência de 15 segundos durante a hora anterior de operação. O consumo de porcentagem DTU de um intervalo é calculado como o consumo percentual máximo das dimensões de CPU, E/S e log. Aqui está uma consulta para calcular o consumo médio de porcentagem DTU durante a última hora:

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

 
A [documentação](http://msdn.microsoft.com/pt-br/library/dn800981.aspx) adicional contém detalhes de como usar essa DMV.  [As Diretrizes de desempenho de banco de dados SQL do Azure](http://msdn.microsoft.com/pt-br/library/azure/dn369873.aspx) abordam como monitorar e ajustar seu aplicativo.

**Impacto no desempenho de memória:** embora a memória é uma das dimensões de recurso que contribui para a taxa de DTU, o banco de dados SQL foi projetado para usar toda a memória disponível para operações de banco de dados. Por esse motivo o consumo de memória não está incluído no consumo de DTU médio na consulta acima. Por outro lado, se você reduzir para um nível de desempenho inferior, então a memória disponível para o banco de dados será reduzida. Isso pode resultar em maior consumo de e/s que afetam o DTU consumido. Portanto, ao reduzir para um nível de desempenho inferior, certifique-se de que você tem espaço suficiente na porcentagem de e/s. Use a DMV [sys.dm_db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) mencionada acima para monitorá-lo.



- **Alertas:** Configurar 'Alertas' no Portal de gerenciamento do Azure para ser notificado quando o consumo de DTU para um banco de dados atualizado se aproximar de um nível determinado mais alto. Alertas de banco de dados podem ser configurados no Portal de gerenciamento do Azure para várias métricas de desempenho como DTU, CPU, E/S e Log.

	Por exemplo, você pode configurar um alerta por email sobre "Porcentagem DTU" se o valor da porcentagem média de DTU exceder 75% nos últimos 5 minutos. Consulte [como: Receber notificações de alerta e gerenciar regras de alerta no Azure](http://msdn.microsoft.com/pt-br/library/azure/dn306638.aspx) para saber mais sobre como configurar notificações de alerta.


- **Atualização/downgrade de nível de desempenho programada:** se seu aplicativo tiver cenários específicos que exigem mais desempenho somente em determinados momentos do dia/semana, você poderá usar a [Automação do Azure](http://msdn.microsoft.com/library/azure/dn643629.aspx) para ampliar/reduzir seu banco de dados para um nível de desempenho superior/inferior de acordo com uma operação planejada.

	Por exemplo, atualizar o banco de dados para um nível mais alto de desempenho para a duração de um trabalho em lotes/manutenção semanal e reduzi-lo depois que o trabalho for concluído. Esse tipo de agendamento também é útil para qualquer operação de uso intensivo de recursos grandes como carregamento de dados, recompilação  de índices, etc.  Observe que o modelo de cobrança do banco de dados SQL do Azure baseia-se em uso por hora de uma camada de serviço/nível de desempenho. Essa flexibilidade permite que você planeje atualizações agendadas ou planejadas com mais eficiência de custos.

##Resumo
O serviço de banco de dados SQL do Azure fornece os dados e ferramentas de telemetria para avaliar suas cargas de trabalho de banco de dados Web/Business e determinar a melhor camada de serviço adequada para atualização. O processo de atualização é bem simples e pode ser feito sem colocar o banco de dados offline e sem perda de dados. Bancos de dados atualizados se beneficiam do desempenho previsível e recursos adicionais fornecidos pelo novas camadas de serviço.

<!--HONumber=35.2-->
