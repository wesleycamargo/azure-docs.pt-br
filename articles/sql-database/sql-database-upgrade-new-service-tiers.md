<properties 
	pageTitle="Atualizar bancos de dados SQL de negócios ou da Web para novas camadas de serviço" 
	description="Atualize o banco de dados da Web ou de negócios do SQL Azure para os novos níveis de desempenho/camadas de serviço de banco de dados SQL Azure. Atualize o Banco de Dados SQL, altere as camadas de serviço, altere o nível de desempenho." 
	services="sql-database" 
	documentationCenter="" 
	authors="jenniehubbard" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.date="2/23/2015" 
	ms.author="jhubbard; v-stste@microsoft.com" 
	ms.workload="data-services" 
	ms.topic="article" 
	ms.tgt_pltfrm=""/>


# Atualizar bancos de dados SQL de negócios ou da Web para novas camadas de serviço

Os bancos de dados SQL Web e Business do Azure estão sendo substituídos e [serão desativados em setembro de 2015](http://msdn.microsoft.com/library/azure/dn741330.aspx) assim, é hora de começar o planejamento para atualizar os bancos de dados Web ou Business existentes para as camadas de serviço Basic, Standard ou Premium.

## Visão geral

<p> Os bancos de dados Web e de Negócios SQL Azure são executados em um ambiente compartilhado multilocatário sem nenhuma capacidade de recursos reservada para o banco de dados. A atividade de outros bancos de dados dentro desse ambiente de recurso compartilhado pode afetar o desempenho. A disponibilidade de recurso em qualquer momento determinado depende muito de outras cargas de trabalho simultâneas em execução no sistema. Isso pode resultar em desempenho de aplicativos de banco de dados imprevisível e altamente variável. Os comentários dos clientes são que esse desempenho imprevisível é difícil de gerenciar, e um desempenho mais previsível é preferencial. 

Para atender a esses comentários, o serviço de banco de dados SQL do Azure introduziu novas camadas de serviço de banco de dados [(Basic, Standard e Premium)](http://msdn.microsoft.com/library/dn741340.aspx), que oferecem um desempenho previsível e uma infinidade de novos recursos de segurança e continuidade de negócios. Essas novas camadas de serviço são projetadas para fornecer um nível especificado de recursos para uma carga de trabalho de banco de dados, independentemente de outras cargas de trabalho do cliente em execução no ambiente. Isso resulta em um desempenho altamente previsível. 

Com essas alterações surgem perguntas sobre como avaliar e decidir qual nova camada de serviço é a melhor adequada para os bancos de dados Web e Business (W/B) atuais e sobre o processo de atualização real.

Por fim, a melhor opção é a combinação de camada de serviço e nível de desempenho que fornece o equilíbrio ideal entre recursos, desempenho e custo, até agora a que satisfaz completamente as necessidades de negócios e requisitos do seu aplicativo.

Este documento fornece uma metodologia orientada para atualizar um banco de dados Web ou Business para uma das novas camadas de serviço e nível de desempenho.

O que é importante observar, é que os bancos de dados do SQL Azure não estão bloqueados em nenhuma camada de serviço ou nível de desempenho específicos, de forma que os requisitos de sua alteração de banco de dados podem ser alterados facilmente entre as camadas de serviço e níveis de desempenho disponíveis. Na verdade, os bancos de dados SQL Basic, Standard e Premium são cobrados por hora, e você tem a capacidade de dimensionar cada banco de dados para cima ou para baixo 4 vezes dentro de um período de 24 horas (usando o [Portal do Azure ou de forma programada](http://msdn.microsoft.com/library/azure/ff394116.aspx)). Isso significa que você pode ajustar a camada de serviço e o nível de desempenho para maximizar as necessidades de desempenho, conjunto de recursos e custos do banco de dados, com base nos requisitos e cargas de trabalho diferentes do seu aplicativo. Isso também significa que avaliar e alterar a camada de serviço e o nível de desempenho do banco de dados (scaling up e down) é um processo contínuo que deve fazer parte de sua manutenção programada e rotina de ajuste de desempenho. 


## Atualizando bancos de dados Business e Web

A atualização do bancos de dados Web ou Business para um novo nível de desempenho/camada de serviço não colocará o banco de dados offline. O banco de dados continuará a trabalhar durante a operação de atualização. No momento da transição real para o novo nível de desempenho temporário podem ocorrer quedas das conexões com duração muito curtas (em segundos) com o banco de dados. Se um aplicativo tiver um tratamento de falha transitória para encerramentos de conexão, então isto é suficiente para proteger contra a queda de conexões no final da atualização. 

A atualização de um banco de dados Web ou Business para uma nova camada de serviço envolve as seguintes etapas:


1. Determinar a camada de serviço com base na capacidade de recurso
2. Determinar um nível aceitável de desempenho com base no histórico de uso de recursos
3. Por que o desempenho existente para meu banco de dados Web ou Business aponta para os níveis Premium mais altos?
4. Ajuste sua carga de trabalho de acordo com um nível de desempenho inferior
5. *Atualizar para uma nova camada de serviço/nível de desempenho*
6. Monitoramento da atualização da nova camada de serviço e nível de desempenho
7. Monitoramento do banco de dados após a atualização



## 1. Determinar a camada de serviço com base na capacidade de recurso

As camadas de serviço Basic, Standard e Premium oferecem diferentes conjuntos de recursos, portanto a primeira etapa na seleção de uma camada apropriada é determinar a camada de serviço que fornece o nível mínimo de recursos necessários para seu aplicativo e negócios. 

Por exemplo, considere que longos backups precisam ser mantidos, ou se recursos de[replicação geográfica Standard ou Ativa](http://msdn.microsoft.com/library/azure/dn783447.aspx) são necessários, ou o tamanho total máximo do banco de dados necessário, etc. Esses requisitos determinam sua escolha de camada de serviço mínima.

A camada 'Basic' é usada principalmente para bancos de dados muito pequenos e de baixa atividade. Portanto, para uma atualização deve geralmente iniciar com a camada 'Standard' ou 'Premium' com base no nível mínimo de recursos necessários.

Os novos recursos da camada de serviço e níveis desempenho são resumidos e comparados na tabela a seguir:

![Service Tier feature comparison][1]


**Recursos adicionais para comparar as camadas de serviço e níveis de desempenho:**

| Artigo | Descrição |
|:--|:--|
|[Faixas de serviço de banco de dados SQL do Azure (edições)](http://msdn.microsoft.com/library/azure/dn741340.aspx)| Visão geral das camadas de serviço Basic, Standard e Premium.|
|[Faixas de serviço de banco de dados SQL do Azure e níveis de desempenho](http://msdn.microsoft.com/library/dn741336.aspx)| Métricas e recursos para cada camada de serviço (e como monitorar a utilização do banco de dados no portal de gerenciamento e usando DMVs). |
|[O que é diferente nas camadas de serviço?](http://msdn.microsoft.com/library/dn369873.aspx#Different)| Mais informações sobre as camadas de serviço diferentes, incluindo alguns motivos por que você deve selecionar uma camada em vez de outra. |
|[Continuidade dos negócios no Banco de dados SQL do Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)|Detalhes dos recursos de continuidade nos negócios e recuperação de desastres (restauração pontual, restauração geográfica, replicação geográfica) disponíveis para as camadas de serviço diferentes.|
|[Preços do Banco de Dados SQL](http://azure.microsoft.com/pricing/details/sql-database/)|Informações detalhadas sobre preços para as diferentes camadas de serviço e níveis de desempenho.|

<br>

Depois de selecionar uma camada de serviço apropriada que atende aos requisitos de seu banco de dados, a próxima etapa é selecionar um nível de desempenho que seja aceitável para suas cargas de trabalho de banco de dados real. 



## 2. Determinar um nível aceitável de desempenho com base no histórico de uso de recursos

O serviço de banco de dados SQL expõe as informações no portal de gerenciamento e nas exibições do sistema, para fornecer para você a comparação sugerida do novo serviço da camada e nível de desempenho para o seu banco de dados Web ou Business existente.

Como bancos de dados Web e Business não possuem os limites de DTU e recursos garantidos associados a eles, podemos normalizar os valores de porcentagem em termos da quantidade de recursos disponíveis para um banco de dados em nível de desempenho S2. O consumo médio de porcentagem DTU de um banco de dados em qualquer intervalo específico pode ser calculado como o valor mais alto de porcentagem entre o uso da CPU, e/s e Log nesse intervalo.


Use o portal de gerenciamento para uma visão geral de alto nível do uso de percentual DTU e, em seguida, analise os detalhes usando exibições do sistema.


### Como exibir o consumo de DTU no Portal de gerenciamento
O portal de gerenciamento fornece informações sobre o consumo de DTU para um banco de dados Web ou Business existente.
As informações sobre DTU estão disponíveis no Portal do Azure atual.


**Portal de Gerenciamento**

1. Faça o logon no [portal de gerenciamento](https://manage.windowsazure.com) e navegue para um banco de dados Web ou Business existente.
2. Clique na guia **MONITORAMENTO**.
3. Clique em **ADICIONAR MÉTRICAS**.
4. Selecione **porcentagem DTU** e clique na marca de seleção na parte inferior para confirmar.

Após a confirmação, você deve ver os dados da **porcentagem DTU** na tabela. Lembre-se de que esta é a porcentagem em comparação com DTUs em um banco de nível Standard (S2), que é de 50 DTUs.

Também vale a pena observar é que esses dados são a média das amostras que são tomadas a cada 5 minutos, portanto, pode haver curtos picos de atividade entre amostras que não está refletida nessas métricas.

![DTU percentage data][2]

Observe que os dados no exemplo acima mostram um uso médio de aproximadamente 10 DTUs (19,23% de 50) e uma porcentagem DTU máxima de DTUs ~ 28 (% 55,83 x 50). 
Supondo que esses dados representavam minha carga de trabalho típica eu provavelmente selecionaria a Standard (S1) para minha atualização inicial. A Standard (S0) fornece 10 DTUs que é meu uso médio, mas isso significaria que meu banco de dados em média seria executado 100% da capacidade, o que nunca é um bom plano. Embora a S1 provavelmente seria uma boa escolha para meu uso médio, e quanto aos horários em que eu estou atingindo o máximo? Talvez eu saiba que os picos de algum processo de manutenção noturno e uso do cliente real não é afetado, posso ficar satisfeito com o desempenho reduzido durante esse período de tempo. Mas talvez não saiba quando o máximo será atingido para que o consumo de porcentagem DTU possa exigir análise mais detalhada.

Para analisar os detalhes de consumo de recursos do banco de dados, você pode usar as exibições do sistema fornecida.


### Exibições do sistema


Os dados de consumo dos banco de dados Web e Business são acessados por meio da exibição [sys.resource_stats](http://msdn.microsoft.com/library/azure/dn269979.aspx) no banco de dados principal do servidor lógico onde se encontra o banco de dados atual. Ela exibe dados de consumo de recursos em porcentagens do limite do nível de desempenho. Essa exibição fornece dados para até os últimos 14 dias, em intervalos de 5 minutos.  Execute a seguinte consulta no banco de dados mestre para recuperar o consumo médio de DTU de um banco de dados:

 
                   
     SELECT start_time, end_time
	 ,(SELECT Max(v)
         FROM (VALUES (avg_cpu_percent)
                    , (avg_physical_data_read_percent)
                    , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.resource_stats
    WHERE database_name = '<your db name>'
    ORDER BY end_time DESC;

Informações sobre consumo de DTU em termos de um nível de banco de dados S2 permite normalizar o consumo atual dos bancos de dados Web/Business em termos da nova camada dos bancos de dados e ver onde elas se ajustam melhor. Por exemplo, se o consumo médio de porcentagem DTU mostra um valor de 80%, isso indica que o banco de dados está consumindo DTU na taxa de 80% do limite de um banco de dados no nível de desempenho S2. Se você vir valores superiores a 100% na exibição **sys.resource_stats**, isso significa que você precisa de um nível de desempenho maior que o S2. Por exemplo, digamos que você vê um valor de porcentagem DTU de pico de 300%.  Isso indica que você está usando três vezes mais recursos que estariam disponíveis em um S2. Para determinar um tamanho inicial razoável, compare as DTUs disponíveis em um S2 (50 DTUs) com os tamanhos mais próximos (S3/P1 = 100 DTUs ou 200% de S2, P2 = 200 DTUs ou 400% de S2). Porque você está em 300% do S2, convém começar com um P2 e testar novamente. 

Com base no percentual de uso DTU e a edição maior do que era necessário de acordo com sua carga de trabalho, você pode determinar qual o nível de desempenho e da camada de serviço mais adequada para sua carga de trabalho do banco de dados (conforme indicado por meio de porcentagem DTU e potência DTU relativa de vários [níveis de desempenho).](http://msdn.microsoft.com/library/azure/dn741336.aspx) Aqui está uma tabela que fornece um mapeamento da porcentagem de consumo de recursos Web/Business para os níveis de desempenho equivalente da nova camada: 

![Resource consumption][4]

> **Observação**:
> Números DTU relativos entre vários níveis de desempenho são baseados no [Padrão de referência de cargas de trabalho de banco de dados SQL do Azure](http://msdn.microsoft.com/library/azure/dn741327.aspx). Como a carga de trabalho do seu banco de dados provavelmente será diferente do padrão de referência, você deve usar os cálculos acima como orientação para um ajuste inicial do banco de dados Web/Business nas novas camadas. Depois de mover o banco de dados para a nova camada, use o processo descrito na seção anterior para validar e ajustar a camada de serviço correta que atenda as suas necessidades de carga de trabalho.
> 
> Embora o nível de desempenho/camada da nova edição sugerida leva em consideração a atividade de banco de dados ao longo dos últimos 14 dias, esses dados baseiam-se em amostras de consumo de recursos com a média superior a 5 minutos. Como tal, ele poderá perder intermitências de curto prazo de atividade menores do que 5 minutos de duração. Assim, este guia deve ser usado como um ponto de partida para atualizar seu banco de dados. Depois de atualizar o banco de dados para a camada sugerida, mais monitoramento, testes e validações serão necessários e o banco de dados pode ser movido para cima/para baixo até uma camada e nível de desempenho diferentes conforme necessário.

Aqui está uma consulta no banco de dados mestre que executa o cálculo do banco de dados da camada Web/Business e sugere qual edição provavelmente é a mais adequada de acordo com sua carga de trabalho para cada um desses intervalos de amostra de dados de 5 minutos.

> **Observação:** esta consulta é útil somente para bancos de dados Web/Business e não **fornecerá** resultados corretos para bancos de dados nas novas camadas.

    WITH DTU_mapping AS
    ( SELECT *
        FROM ( VALUES (1, 10,'Basic'), (2, 20,'S0'), (3, 40,'S1'), (4, 100, 'S2')
                    , (5, 200, 'S3/P1'), (6, 1600,'Premium')
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

![Sample Result](media/sql-database-upgrade-new-service-tiers/CTnjv26.png)

No gráfico, você pode ver a tendência de consumo do percentual médio de DTU ao longo do tempo. Aqui está um exemplo de gráfico para um banco de dados de um nível S2 na maioria do tempo, com alguma atividade de pico atingindo um nível de banco de dados P1.  O consumo de DTU ao longo do tempo varia dos limites do 'Basic' até os limites do 'P1'. Para ajustar completamente esse banco de dados na nova camada, será necessário um banco de dados com camada de serviço Premium com nível de desempenho 'P1'. Por outro lado, um banco de dados de nível S2 pode funcionar se esses picos ocasionais para o nível P1 são raros.

![DTU Usage](media/sql-database-upgrade-new-service-tiers/e4N4ay5.png)

**Impacto no desempenho de memória:** embora a memória é uma das dimensões de recurso que contribui para a taxa de DTU, o banco de dados SQL foi projetado para usar toda a memória disponível para operações de banco de dados. Por esse motivo o consumo de memória não está incluído no consumo de DTU médio na consulta acima. Por outro lado, se você reduzir para um nível de desempenho inferior, então a memória disponível para o banco de dados será reduzida. Isso pode resultar em maior consumo de e/s que afetam o DTU consumido. Portanto, ao reduzir para um nível de desempenho inferior, certifique-se de que você tem espaço suficiente na porcentagem de e/s. Use a DMV [sys.dm_db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) mencionada acima para monitorá-lo.



## 3. Por que o desempenho existente para meu banco de dados Web ou Business aponta para os níveis Premium mais altos?

Os bancos de dados Web e Business não tem nenhum valor específico de capacidade de recursos reservado para qualquer banco de dados individual. Além disso, não há nenhum mecanismo no local para os clientes dimensionarem o desempenho para cima ou para baixo para um banco de dados Web ou Business. Isso resulta na variação de um banco de dados Web e Business em qualquer lugar desde agonizantemente lento até níveis Premium. Esse intervalo variado de desempenho é *unfairly* dependente do nível geral de consumo de recursos em qualquer ponto no tempo por outros bancos de dados dentro do ambiente de vários locatários que compartilham recursos.  

Claro que o objetivo dos serviços de banco de dados SQL Azure é ter todos os bancos de dados Web e Business executados mais próximo de 100% o melhor possível. E que fizeram um ótimo trabalho de manter os níveis de desempenho médio de bancos de dados Web e Business em direção aos níveis Premium. Isso é por que o desempenho do seu banco de dados existente pode ser mapeado para níveis Premium atuais. Infelizmente isso resultou em um pouco de expectativas irreais durante a comparação de bancos de dados Web e Business em relação aos novos níveis de desempenho/camadas de serviço ao avaliar qual camada de serviço para a qual ser atualizada.

Para compreender mais claramente as diferenças entre Web/Business e as camadas de serviço Basic, Standard e Premium, vamos observar a figura a seguir. Considere 9 bancos de dados SQL em execução no modelo Web/Business de recursos compartilhados em comparação aos 9 bancos de dados SQL em execução no modelo de camada de serviço Basic, Standard e Premium. No modelo Web/Business, você pode ver claramente os efeitos que 'noisy neighbors' têm sobre os outros bancos de dados que estão participando do pool de recursos compartilhados. Quando o banco de dados 1 está executando uma carga de trabalho de uso intensivo de recursos, todos os outros bancos de dados no pool são afetados e os recursos disponíveis começam a ser reduzidos. Nas camadas de serviço Basic, Standard e Premium uma quantidade específica de recursos ***são*** alocados para cada banco de dados de forma que os outros bancos de dados dentro do ambiente compartilhado são essencialmente isolados da edição vizinha com ruídos e associados por nível de desempenho selecionado para o banco de dados. 

![Predictable performance of the new service tiers][3]

Se o percentual DTU geral é extremamente alto, você deve começar analisando as métricas detalhadas da qual os DTUs consistem. Especificamente, detalhando pormenorizadamente o Log de E/S e uso de memória do banco de dados. Ele pode descobrir possíveis áreas onde você pode otimizar e reduzir o consumo de DTU.


## 4.	Ajuste sua carga de trabalho de banco de dados de acordo com um nível de desempenho inferior
Se a análise do histórico do uso de recursos do seu banco de dados indica que você deve atualizar para um nível de desempenho que é mais caro do que o desejado, você pode procurar em áreas em que o ajuste de desempenho adicional pode ajudar. 

Considerando seu conhecimento sobre os detalhes do seu aplicativo, se o uso do recurso parece muito alto em comparação com o que você espera que a carga de trabalho típica deve ser, então talvez você tenha algumas oportunidades em que o ajuste de desempenho pode trazer benefícios para seu aplicativo.

Na verdade, todos os bancos de dados podem se beneficiar de uma nova rodada de ajuste de desempenho.

Além da manutenção de ajuste típica, como análise de índices, planos de execução, etc., você deve otimizar suas rotinas de acesso de dados para o banco de dados do SQL Azure de destino. 

| Artigo | Descrição |
|:--|:--|
| [Diretrizes de desempenho de Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/dn369873.aspx) | A seção 'Tuning Your Application' fornece recomendações detalhadas e técnicas para o ajuste de desempenho de um Banco de Dados SQL do Azure.|
|[Monitoramento de desempenho e ferramentas de ajuste](https://msdn.microsoft.com/library/ms179428.aspx)| Links e descrições das ferramentas disponíveis para monitorar eventos no SQL Server e para ajustar o design de banco de dados físico.|
|[Monitorar e ajustar o desempenho](https://msdn.microsoft.com/library/ms189081.aspx)|Seção ajuste de desempenho do SQL Server 2014 no MSDN.|
| [Solucionando problemas de desempenho no SQL Server 2008](https://msdn.microsoft.com/library/dd672789.aspx) | Mais antigo, mas ainda relevante, o whitepaper fornece orientação para solução de problemas de desempenho comuns, incluindo informações importantes sobre solução de problemas afunilamentos de memória e cpu.|
|[Solucionar problemas e otimizar consultas com o Banco de Dados SQL do Azure](http://social.technet.microsoft.com/wiki/contents/articles/1104.troubleshoot-and-optimize-queries-with-azure-sql-database.aspx)|Tópico mais antigo, mas ainda relevante sobre Exibição de Gerenciamento Dinâmico e como podem ser usados para fins de solução de problemas.|



## 5. Atualizar para uma nova camada de serviço/nível de desempenho
Depois de determinar a camada de serviço e nível de desempenho apropriados para seu banco de dados Web ou Business, há várias maneiras de atualizar o banco de dados para a nova camada:

| Ferramenta de gerenciamento | Para alterar o nível de desempenho e da camada de serviço de um banco de dados|
| :---| :---|
| [Portal de Gerenciamento do Azure](https://manage.windowsazure.com) | clique na guia **ESCALA** na página do painel de seu banco de dados. |
| [PowerShell do Azure](http://msdn.microsoft.com/library/azure/dn546726.aspx) | use o cmdlet [Set-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546732.aspx). |
| [API REST de gerenciamento de serviço](http://msdn.microsoft.com/library/azure/dn505719.aspx) | use o comando [Update Database](http://msdn.microsoft.com/library/dn505718.aspx).|
| [Transact-SQL](http://msdn.microsoft.com/library/bb510741.aspx) | use a declaração [ALTER DATABASE (Transact-SQL)](http://msdn.microsoft.com/library/ms174269.aspx). |

Para obter detalhes, consulte [Alterando camadas de serviço e níveis de desempenho de banco de dados](http://msdn.microsoft.com/library/dn369872.aspx)


## 6.	Monitoramento da atualização da nova camada de serviço e nível de desempenho
O banco de dados SQL do Azure fornece informações sobre o andamento em operações de gerenciamento (como CREATE, ALTER, DROP) executadas em um banco de dados na exibição de gerenciamento dinâmico sys.dm_operation_status no banco de dados mestre do servidor lógico onde seu banco de dados atual está localizado [Consulte a documentação do sys.dm_operation_status.](http://msdn.microsoft.com/library/azure/dn270022.aspx) Use o status da operação DMV para determinar o progresso da operação de atualização de um banco de dados. Este exemplo de consulta mostra todas as operações de gerenciamento executadas em um banco de dados:

    SELECT o.operation, o.state_desc, o.percent_complete
    , o.error_code, o.error_desc, o.error_severity, o.error_state
    , o.start_time, o.last_modify_time
    FROM sys.dm_operation_status AS o
    WHERE o.resource_type_desc = 'DATABASE'
    and o.major_resource_id = '<database_name>'
    ORDER BY o.last_modify_time DESC;

Se você usou o portal de gerenciamento para a atualização, uma notificação também está disponível a partir do portal para a operação.

### O que acontece quando a carga de trabalho de banco de dados atinge os limites de recursos após a atualização?
Os níveis de desempenho são calibrados e controlados para fornecer os recursos necessários para executar sua carga de trabalho de banco de dados até os limites máximos permitidos para a sua camada de serviço e nível de desempenho selecionados (ou seja, o consumo de recursos é de 100%). Se a sua carga de trabalho está atingindo os limites em um dos limites de CPU/E/S de dados/E/S de Log, você continuará a receber os recursos no nível máximo permitido, mas provavelmente você observará latências maiores para suas consultas. O alcance de um desses limites não resultará em nenhum erro, mas apenas uma lentidão na carga de trabalho, a menos que a lentidão se torne tão grave que as consultas comecem a não serem executadas a tempo. Se você está atingindo os limites do máximo permitido de sessões/solicitações de usuários simultâneos (threads de trabalho), você verá [erro 10928 ou 10929](http://msdn.microsoft.com/library/azure/dn338078.aspx).


## 7.	Monitoramento do banco de dados após a atualização
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

 
A [documentação](http://msdn.microsoft.com/library/dn800981.aspx) adicional contém detalhes de como usar essa DMV.  [As Diretrizes de desempenho de banco de dados SQL do Azure](http://msdn.microsoft.com/library/azure/dn369873.aspx) abordam como monitorar e ajustar seu aplicativo.


- **Alertas:** Configurar  'Alerts' no Portal de gerenciamento do Azure para ser notificado quando o consumo de DTU para um banco de dados atualizado se aproximar de um nível determinado mais alto. Alertas de banco de dados podem ser configurados no Portal de gerenciamento do Azure para várias métricas de desempenho como DTU, CPU, E/S e Log. 

	Por exemplo, você pode configurar um alerta por email sobre "Porcentagem DTU" se o valor da porcentagem média de DTU exceder 75% nos últimos 5 minutos. Consulte [Como: Receber notificações de alerta e gerenciar regras de alerta no Azure](http://msdn.microsoft.com/library/azure/dn306638.aspx) para saber mais sobre como configurar notificações de alerta.


- **Ampliação/redução de nível de desempenho programada:** se seu aplicativo tiver cenários específicos que exigem mais desempenho somente em determinados momentos do dia/semana, você poderá usar a [Automação do Azure](http://msdn.microsoft.com/library/azure/dn643629.aspx) para ampliar/reduzir seu banco de dados para um nível de desempenho superior/inferior de acordo com uma operação planejada.

	Por exemplo, atualizar o banco de dados para um nível mais alto de desempenho para a duração de um trabalho em lotes/manutenção semanal e reduzi-lo depois que o trabalho for concluído. Esse tipo de agendamento também é útil para qualquer operação de uso intensivo de recursos grandes como carregamento de dados, recompilação de índices, etc. Observe que o modelo de cobrança do banco de dados SQL do Azure baseia-se em uso por hora de uma camada de serviço/nível de desempenho. Essa flexibilidade permite que você planeje atualizações agendadas ou planejadas com mais eficiência de custos.



## Resumo
O serviço de banco de dados SQL do Azure fornece os dados e ferramentas de telemetria para avaliar suas cargas de trabalho de banco de dados Web/Business e determinar a melhor camada de serviço adequada para atualização. O processo de atualização é bem simples e pode ser feito sem colocar o banco de dados offline e sem perda de dados. Bancos de dados atualizados se beneficiam do desempenho previsível e recursos adicionais fornecidos pelo novas camadas de serviço.




<!--Image references-->
[1]: ./media/sql-database-upgrade-new-service-tiers/service-tier-features.png
[2]: ./media/sql-database-upgrade-new-service-tiers/portal-dtus.JPG
[3]: ./media/sql-database-upgrade-new-service-tiers/web-business-noisy-neighbor.png
[4]: ./media/sql-database-upgrade-new-service-tiers/resource_consumption.png


<!--HONumber=47-->
 