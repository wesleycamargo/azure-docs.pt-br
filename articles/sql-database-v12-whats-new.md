<properties 
	pageTitle="O que há de novo no Banco de Dados SQL V12" 
	description="Descreve os recursos mais recentes que foram adicionados ao Banco de Dados V12 do Azure, mas somente até maio de 2015." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="genemi"/>


# O que há de novo no Banco de Dados SQL V12


O Banco de Dados SQL V12 do Azure ([no modo de visualização em algumas regiões](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable)) agora fornece compatibilidade quase total com o mecanismo do Microsoft SQL Server. Esses aprimoramentos recentes ajudam a simplificar as migrações de aplicativos do SQL Server para o Banco de Dados SQL e ajudam seu sistema a processar de modo robusto cargas de trabalho mais pesadas do banco de dados.


[Inscreva-se](https://portal.azure.com) no Banco de Dados SQL para obter uma [introdução](sql-database-get-started.md) dessa nova geração no Microsoft Azure. É necessário primeiro uma assinatura do Microsoft Azure. Você pode se inscrever para uma [avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial) e analisar as informações de [preço](http://azure.microsoft.com/pricing/details/sql-database).


O caminho de planejamento e atualização dos seus bancos de dados anteriores à versão V12 começa em [Planejar e se preparar para atualizar para o Banco de Dados SQL V12](sql-database-v12-plan-prepare-upgrade.md).


> [AZURE.TIP]A principal página da Web com informações sobre novos recursos no Banco de Dados SQL do Azure é nossa página da Web “Atualizações de serviço”, em [http://azure.microsoft.com/updates/](http://azure.microsoft.com/updates/). Na página da Web, Atualizações de serviço, no controle **Serviços**, selecione **Banco de Dados SQL**.


## Destaques


- O **portal de visualização do Azure** está [disponível](http://portal.azure.com/) para criar bancos de dados e servidores do Banco de Dados SQL na versão V12 ou, se preferir, na versão anterior. No portal de visualização do Azure, especifique o banco de dados do Banco de Dados SQL e prossiga para especificar um servidor do Banco de Dados SQL para hospedá-lo.
 - A partir de abril de 2015, o [antigo portal do Azure](http://manage.windowsazure.com/) ainda terá suporte.


- **Escolha uma versão** do servidor do Banco de Dados SQL quando você usar o portal de visualização do Azure para criar um novo banco de dados. O padrão é V12, mas você pode escolher a versão anterior do servidor do Banco de Dados SQL.


- A **segurança** aproveita o novo recurso de [usuários em bancos de dados independentes](sql-database-v12-whats-new.md#UsersInContainedDatabases). Dois outros recursos são a [segurança no nível de linha](sql-database-v12-whats-new.md#RowLevelSecurity) e [mascaramento de dados dinâmico](sql-database-v12-whats-new.md#DynamicDataMasking), embora ainda estejam no modo de visualização e não estejam disponíveis para o público em geral.


- **Gerenciamento mais fácil** de bancos de dados grandes para oferecer suporte a cargas de trabalho mais pesadas com consultas paralelas (apenas Premium), [particionamento de tabela](http://msdn.microsoft.com/library/ms187802.aspx), [indexação online](http://msdn.microsoft.com/library/ms188388.aspx), recompilação segura de índice amplo com remoção do limite de tamanho de 2 GB e mais opções no comando[ALTER DATABASE](http://msdn.microsoft.com/library/bb522682.aspx).


- **Suporte às principais funções de programação** para orientação de design de aplicativos mais robusto com [integração de CLR](http://msdn.microsoft.com/library/ms189524.aspx), Transact-SQL [funções de janela](http://msdn.microsoft.com/library/ms189461.aspx), [índices XML](http://msdn.microsoft.com/library/bb934097.aspx) e [controle de alterações](http://msdn.microsoft.com/library/bb933875.aspx) para dados.


- **Desempenho inovador** com suporte para consultas de [índice columnstore](http://msdn.microsoft.com/library/gg492153.aspx) (apenas camada Premium) para data mart e cargas de trabalho analíticas menores.


- **Monitoramento e solução de problemas** foram aprimorados com visibilidade em mais de 100 novas exibições de tabela em um conjunto expandido de [DMVs](http://msdn.microsoft.com/library/ms188754.aspx) (Exibições de Gerenciamento de Banco de Dados).


- **Novo nível de desempenho S3 na camada Standard:** oferece mais flexibilidade de [preço](sql-database-upgrade-new-service-tiers.md) entre Standard e Premium. O S3 fornecerá mais DTU (unidades de taxa de transferência de banco de dados) e todos os recursos disponíveis na camada Standard.


## Aprimoramentos da V12: gerenciamento de banco de dados expandido


| Recurso | Descrição |
| :--- | :--- |
| . | ***1º de maio de 2015:*** |
| PowerShell para atualização de versão anterior para V12 | Novos cmdlets do PowerShell estão disponíveis para iniciar, cancelar ou monitorar uma atualização para o Banco de Dados SQL do Azure da V11 para V12 ou qualquer outra versão anterior à V12.<br/><br/>Para obter a documentação de referência sobre esses cmdlets, confira:<br/>* [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)<br/>* [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)<br/>* [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx) |
| . | ***Abril de 2015:*** |
| TDE | Criptografia Transparente de Dados (visualização) ajuda a proteger contra atividades mal-intencionadas executando criptografia e descriptografia do banco de dados em tempo real, backups associados e arquivos de log de transações em repouso. A TDE não exige alterações no seu aplicativo.<br/><br/>Para obter detalhes, confira:<br/>- [Criptografia  Transparente de Dados com o Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/dn948096.aspx) - instruções passo a passo.<br/>- [TDE (Criptografia Transparente de Dados)](http://msdn.microsoft.com/library/bb934049.aspx) - descrição geral. |
| Certificados, chave e funções criptográficas do Transact-SQL | Certificados, chaves simétricas e chaves assimétricas agora podem ser criados no Banco de Dados SQL V12 do Azure. Agora há suporte para a maioria das funções criptográficas.<br/><br/>Para saber mais, confira:<br/>- [CREATE CERTIFICATE (Transact-SQL)](http://msdn.microsoft.com/library/ms187798.aspx)<br/>- [CREATE SYMMETRIC KEY (Transact-SQL)](http://msdn.microsoft.com/library/ms188357.aspx)<br/>- [CREATE ASYMMETRIC KEY (Transact-SQL)](http://msdn.microsoft.com/library/ms174430.aspx)<br/>- [Funções criptográficas (Transact-SQL)](http://msdn.microsoft.com/library/ms173744.aspx) |
| Pool elástico do Banco de Dados SQL | Tarefas simples se tornam complicadas quando você expande para milhares de bancos de dados, e essa é a desvantagem do crescimento explosivo. Os desenvolvedores de SaaS passam horas incontáveis escrevendo lógica complexa para lidar com alterações de esquema e outras operações administrativas à medida que os negócios se expandem.<br/><br/>Com [bancos de dados elásticos](sql-database-elastic-pool.md), a vida se torna mais fácil. Você adota um script, envia-o como um trabalho e o Banco de Dados SQL faz o restante. |
| Recomendações de camada de preços e o STA | Agora, ao mover o banco de dados das edições Web ou Business para uma das novas camadas de serviço Basic, Standard ou Premium, você pode obter recomendações de camada de preços diretamente no portal de visualização do Azure.<br/><br/>O serviço de Banco de Dados SQL do Azure analisa os requisitos de desempenho e recursos para bancos de dados existentes. O [STA (Consultor de Camada de Serviço)](sql-database-service-tier-advisor.md) lê os dados de desempenho para recomendar a camada de serviço ideal para seu banco de dados. |
| Permissões DMV | No caso de várias DMVs (exibições de gerenciamento dinâmico), a execução delas anteriormente exigia a permissão VIEW SERVER STATE. Agora, no Banco de Dados SQL V12 do Azure, em muitos casos, as DMVs podem ser executadas pela conta de administrador do Banco de Dados SQL nos bancos de dados que estão na camada de serviço Basic ou Standard.<br/><br/>Na camada Premium, o acesso às DMVs podem ser concedido a mais usuários usando a permissão VIEW DATABASE STATE no banco de dados.<br/><br/>Para ver detalhes, confira a seção Permissões de uma determinada [DMV](http://msdn.microsoft.com/library/ms188754.aspx) individual. |
| DBCC SQLPERF | O comando [DBCC SQLPERF](http://msdn.microsoft.com/library/ms189768.aspx) agora está disponível no Banco de Dados SQL V12 do Azure. As opções *reset wait* e *latch statistics* não têm suporte no Banco de Dados SQL V12. |
| . | ***Dezembro de 2014:*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>Usuários independentes em bancos de dados | Agora você pode criar usuários no banco de dados sem ter um logon correspondente no banco de dados mestre. Eles são chamados de *usuários independentes*. Isso torna muito mais simples mover o banco de dados para outro servidor. O código de conexão nos seus aplicativos cliente é igual, usando usuários de banco de dados independentes ou não.<br/><br/>Usar esse recurso é uma excelente maneira de se beneficiar de contratos de nível de serviços com garantias mais altas.<br/><br/>De modo geral, incentivamos você a considerar o uso desse recurso. No entanto, você pode ter cenários específicos que tornam o par *logon+usuário* a sua melhor opção no momento.<br/><br/>Para saber mais, confira:<br/>- [Diretrizes e limitações de segurança do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/>- [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/>- [Bancos de dados independentes](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| Partições de tabela | As limitações anteriores no [particionamento de tabela](http://msdn.microsoft.com/library/ms190787.aspx) foram eliminadas. |
| Transações maiores | Com a V12 você não está mais limitado a um máximo de 2 GB de modificações de dados em uma única transação. <br/><br/> Um dos benefícios é que recompilar um índice amplo não se limita mais ao limite de tamanho de transação de 2 GB. Para obter informações gerais, confira [Limites de recursos do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/dn338081.aspx). |
| Compilação e recompilação e índices online | Antes da V12, o Banco de Dados SQL do Azure geralmente oferecia suporte à cláusula (ONLINE=ON) do comando [ALTER INDEX](http://msdn.microsoft.com/library/ms188388.aspx), mas isso não tinha suporte de índices em uma coluna do tipo BLOB. Agora, a V12 oferece suporte (ONLINE=ON) até mesmo para índices em colunas BLOB.<br/><br/> O recurso ONLINE permite consultas para aproveitar um índice, mesmo enquanto o índice estiver sendo recompilado. |
| Suporte ao PONTO DE VERIFICAÇÃO | Com a V12, você pode emitir o comando [CHECKPOINT](http://msdn.microsoft.com/library/ms188748.aspx) do Transact-SQL para seu banco de dados. |
| Aprimoramento de ALTER TABLE | Permite que muitas alterações na coluna ações sejam realizadas enquanto a tabela permanece disponível. Para saber mais, confira [ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms190273.aspx) |
| Aprimoramento de TRUNCATE TABLE | Permite o truncamento de partições específicas. Para saber mais, confira [TRUNCATE TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx). |
| Mais opções de ALTER DATABASE | A V12 oferece suporte a mais opções que estão disponíveis no comando [ALTER DATABASE](http://msdn.microsoft.com/library/ms174269.aspx). <br/><br/> Para saber mais, confira [Referência de Transact-SQL do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Mais comandos DBCC | Muito mais comandos [DBCC](http://msdn.microsoft.com/library/ms188796.aspx) agora estão disponíveis na V12. Para obter detalhes, confira [Referência de Transact-SQL do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |


## Suporte a programação e aplicativos


| Recurso | Descrição |
| :--- | :--- |
| . | ***1º de maio de 2015:*** |
| Aumento nos tamanhos do índice | No Banco de Dados SQL V12, até 32 colunas podem ser combinadas em uma única chave de índice composta. O tamanho máximo permitido de valores de índice é de 900 bytes para um índice clusterizado ou de 1.700 bytes para um índice não clusterizado.<br/><br/>Os limites são 16 colunas e 900 bytes para a versão V11 e todas as outras versões antes do Banco de Dados SQL V12. |
| . | ***Abril de 2015:*** |
| Visualização de pesquisa de texto completo | [A FTS (Pesquisa de Texto Completo)](http://msdn.microsoft.com/library/ms142571.aspx) permite consultar colunas baseadas em caracteres de maneira mais eficiente do que usando o operador LIKE. Por exemplo:<br/><br/>- FREETEXT: pesquisa frases que correspondam ao *significado* da sua frase de pesquisa, mesmo quando a expressão exata não corresponde.<br/>- CONTAINS: pesquisa valores de célula que contenham dois termos da pesquisa em estreita *proximidade física* um do outro.<br/><br/>**OBSERVAÇÃO:** esse recurso está no status de visualização e ainda não foi anunciado para disponibilidade geral para uso em produção. O intervalo da funcionalidade de visualização da FTS é um subconjunto do intervalo da FTS no Microsoft SQL Server. |
| . | ***Fevereiro de 2015:*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> Visualização do mascaramento de dados dinâmico | Quando um conjunto de linhas é gerado a partir de uma consulta, uma política de mascaramento dos dados estabelecida pode substituir partes dos dados com caracteres 'X’ para sobrepor e proteger informações confidenciais. Depois que a operação de mascaramento é concluída, o conjunto de linhas modificado é enviado ao cliente.<br/><br/>Um exemplo de uso pode ser mascarar tudo, menos os últimos dígitos do número de um cartão de crédito.<br/><br/>**OBSERVAÇÃO:** esse recurso está no status de visualização e ainda não foi anunciado para disponibilidade geral para uso em produção.<br/><br/>Para obter informações detalhadas, confira [Introdução ao mascaramento de dados dinâmico do Banco de Dados SQL do Azure](sql-database-dynamic-data-masking-get-started.md). |
| . | ***Janeiro de 2015:*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> Visualização da RLS (segurança no nível de linha) | O novo comando [CREATE SECURITY POLICY](http://msdn.microsoft.com/library/dn765135.aspx) no Transact-SQL permite implementar a RLS. A RLS faz com que o servidor do banco de dados adicione condições que filtram algumas linhas de dados antes que um conjunto de linhas seja retornado ao chamador.<br/><br/>No setor, a RLS, às vezes, também é chamada de controle de acesso refinado.<br/><br/>**OBSERVAÇÃO:** esse recurso está no status de visualização e ainda não foi anunciado para disponibilidade geral para uso em produção.<br/><br/>Para obter um exemplo de código e muito mais, confira [Visualização da segurança no nível de linha](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx). |
| . | ***Dezembro de 2014:*** |
| Funções de janela em consultas do Transact-SQL | As funções de janela ANSI são acessadas com a cláusula [OVER](http://msdn.microsoft.com/library/ms189461.aspx). <br/><br/> Itzik Ben-Gan possui uma excelente [postagem de blog](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1) que explica detalhadamente as funções de janela e a cláusula OVER. |
| Integração CLR do .NET | O CLR (common language runtime) do .NET Framework é integrado ao V12. <br/><br/> Somente assemblies SAFE que sejam totalmente compilados em código binário têm suporte. Para obter detalhes, confira [Restrições do modelo de programação da integração de CLR](http://msdn.microsoft.com/library/ms403273.aspx). <br/><br/> Você pode encontrar informações sobre esse recurso nos tópicos a seguir: <br/> * [Introdução à integração de CLR do SQL Server](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY (Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx). |
| Controle de alterações de dados | O controle de alterações de dados agora podem ser configurado no banco de dados ou nível de tabela. <br/><br/> Para obter informações sobre controle de alterações, confira [Sobre o controle de alterações (SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx). |
| Índices XML | A V12 permite usar os comandos [CREATE XML INDEX](http://msdn.microsoft.com/library/bb934097.aspx) e [CREATE SELECTIVE XML INDEX](http://msdn.microsoft.com/library/jj670104.aspx) do Transact-SQL. |
| Tabela como um heap | A V12 permite que você crie uma tabela que não tem nenhum índice clusterizado. <br/><br/> Esse recurso é especialmente útil para o suporte ao comando [SELECT...INTO](http://msdn.microsoft.com/library/ms188029.aspx) do Transact-SQL, que cria uma tabela do resultado de uma consulta. |
| Funções de aplicativo | Para controle de permissões e segurança, a V12 permite emitir os comandos [GRANT](http://msdn.microsoft.com/library/ms187965.aspx) - [DENY](http://msdn.microsoft.com/library/ms188338.aspx) - [REVOKE](http://msdn.microsoft.com/library/ms187728.aspx) em relação às [funções de aplicativo](http://msdn.microsoft.com/library/ms190998.aspx). |


## Melhores resultados ao cliente


| Recurso | Descrição |
| :--- | :--- |
| . | ***Dezembro de 2014:*** |
| DMVs (Exibições de Gerenciamento Dinâmico) | Várias DMVs são adicionadas à V12. Para obter detalhes, confira [Referência de Transact-SQL do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Controle de alterações | A V12 é totalmente compatível com o controle de alterações. <br/><br/> Para obter detalhes sobre esse recurso, confira [Habilitar e desabilitar o controle de alterações (SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx). |
| Índices ColumnStore | Um índice columnstore melhorará o desempenho do sistema para data warehouses quando uma coluna indexada contiver repetições do mesmo valor. O [índice columnstore compacta](http://msdn.microsoft.com/library/gg492088.aspx) os valores duplicados para reduzir o número de linhas físicas que devem ser acessadas durante as consultas. |


## Melhorias de desempenho na camada de serviço Premium


Esses aprimoramentos de desempenho se aplicam aos níveis P2 e P3 dentro da camada de serviço Premium.


| Recurso | Descrição |
| :--- | :--- |
| . | ***Dezembro de 2014:*** |
| Processamento paralelo para consultas | A V12 fornece um maior grau de paralelismo para consultas que podem se beneficiar dele. |
| Latência de e/s mais resumida | A V12 tem latência significativamente mais resumida para operações de entrada/saída. |
| Aumento de IOPS | A V12 pode processar uma quantidade maior de entrada/saída por segundos (IOPS). |
| Taxa de log | A V12 pode fazer mais alterações de dados por segundo. |


## A V12 se torna API padrão em 1º de agosto de 2015  


| Recurso | Descrição |
| :--- | :--- |
| . | ***Agosto de 2015:*** |
| Criar o servidor usando REST ou PowerShell | Quando você cria um servidor sem especificar uma versão de servidor, a versão padrão será alterada de V11 para V12.<br/><br/>Isso se alinha com o [portal de visualização do Azure](http://portal.azure.com). |
| Crie banco de dados usando Transact-SQL, [REST](http://msdn.microsoft.com/library/azure/gg715283.aspx) ou [PowerShell](http://msdn.microsoft.com/library/azure/dn806332.aspx) | Em servidores V11, quando você cria um novo banco de dados sem especificar uma edição ou o objetivo do serviço, o objetivo de serviço padrão será [S0](http://azure.microsoft.com/pricing/details/sql-database/) em vez de Web e Business. Isso se alinha com servidores V12 do portal de visualização do Azure. |


## Resumo dos aprimoramentos


A V12 eleva nosso Banco de Dados SQL próximo a compatibilidade completa de recursos com nosso produto SQL Server. A V12 concentra-se nos recursos mais populares e no suporte à programação. Isso torna o desenvolvimento mais eficiente e mais agradável. Agora é ainda mais fácil mover seus aplicativos do banco de dados SQL para a nuvem.


E na camada Premium a V12 traz mais melhorias de desempenho. Alguns aplicativos perceberão ganhos extremos na velocidade de consulta. Aplicativos com cargas de trabalho pesadas perceberão a taxa de transferência confiável robusta.


## <a name="V12AzureSqlDbPreviewGaTable"></a>Status de GA (disponibilidade geral) da V12 por região


> [AZURE.NOTE]
> [Pricing](http://azure.microsoft.com/pricing/details/sql-database/) durante a visualização tem desconto. Os preços retornam ao nível de GA para todas as regiões em 31 de março de 2015, terça-feira.


| Região do Azure | Status<br/>da versão atual da V12 | Data da promoção<br/>para GA |
| :--- | :--- | :--- |
| Centro-Sul dos Estados Unidos | Disponibilidade geral (DG) | 9 de fevereiro de 2015 |
| Centro dos EUA | Disponibilidade geral (DG) | 9 de fevereiro de 2015 |
| Centro-Norte dos EUA | Disponibilidade geral (DG) | 9 de fevereiro de 2015 |
| Oeste dos EUA | Disponibilidade geral (DG) | 9 de fevereiro de 2015 |
| Leste dos EUA | Disponibilidade geral (DG) | 9 de fevereiro de 2015 |
| Leste dos EUA 2 | Disponibilidade geral (DG) | 9 de fevereiro de 2015 |
| Ásia Oriental | Disponibilidade geral (DG) | 24 de fevereiro de 2015 |
| Sudeste Asiático | Disponibilidade geral (DG) | 24 de fevereiro de 2015 |
| Oeste do Japão | Disponibilidade geral (DG) | 24 de fevereiro de 2015 |
| Leste do Japão | Disponibilidade geral (DG) | 24 de fevereiro de 2015 |
| Norte da Europa | Disponibilidade geral (DG) | 29 de janeiro de 2015 |
| Europa Ocidental | Disponibilidade geral (DG) | 29 de janeiro de 2015 |
| Sul do Brasil | Disponibilidade geral (DG) | 21 de abril de 2015 |
| Leste da Austrália | Visualização | Estimado para os segundo trimestre de 2015 |
| Sudeste da Austrália | Visualização | Estimado para os segundo trimestre de 2015 |


Para qualquer região que tenha atingido a DG, todas as novas inscrições e seus bancos de dados subsequentes usam a arquitetura de serviço V12 e, portanto, têm acesso aos recursos mais recentes. O presente artigo lista os novos recursos trazidos pela V12.


Para qualquer região que esteja no modo de visualização e ainda não esteja em disponibilidade geral, você deverá [ativar a opção para usar os bancos de dados V12](sql-database-v12-sign-up.md).


Na disponibilidade geral, se tiver servidores e bancos de dados anteriores à V12, você poderá optar por atualizar (ou mover) os bancos de dados para a arquitetura de serviço V12. Em seguida, você poderá usar os novos recursos para produção. Os bancos de dados V12 devem estar na [camada de preços](sql-database-upgrade-new-service-tiers.md) Basic, Standard ou Premium.


## Como proceder


Você pode saber como atualizar os bancos de dados do Banco de Dados SQL V11 para V12 do Azure em [Planejar e se preparar para atualizar para o Banco de Dados SQL V12 do Azure](sql-database-v12-plan-prepare-upgrade.md).


Os números de versão, como V12, referem-se ao valor retornado pela instrução a seguir do Transact-SQL:<br/> **SELECT @@version;**


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(ou um pouco maior, V12)*


Para obter detalhes de preço mais recentes sobre a V12, confira [Preços do Banco de Dados SQL](http://azure.microsoft.com/pricing/details/sql-database/).


## Cuidados com a atualização da V12


Há itens importantes que devem ser conhecidos sobre as limitações durante e após uma atualização do seu banco de dados V11 para V12. Você pode ler os detalhes neste link para um [ponto médio](sql-database-v12-plan-prepare-upgrade.md#limitations) no tópico *Planejar e se preparar para atualizar para o Banco de Dados SQL V12*.


## Outras fontes de notícias mais recentes


Este tópico de *novidades* do Banco de Dados SQL V12 do Azure será finalizado e não será mais atualizado, provavelmente, depois de 30 de abril de 2015. Informações sobre novos recursos e outros anúncios estão sendo transferidas para o link de anúncios que se segue:


- [Anúncios](http://azure.microsoft.com/updates/?service=sql-database) do Banco de Dados SQL do Azure em nossa página da Web **Atualizações de serviço**.
 - Talvez você queira clicar no ícone **RSS** na página da Web, quando o controle Serviço estiver definido como *Banco de Dados SQL*.
- Siga-no Twitter: **@SQLTechCenter**.


[V12 general availability (GA) status per region]: #V12AzureSqlDbPreviewGaTable

<!---HONumber=58-->