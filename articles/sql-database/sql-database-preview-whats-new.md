<properties 
	pageTitle="O que há de novo no banco de dados SQL V12" 
	description="Descreve os aprimoramentos mais recentes para o banco de dados SQL do Azure que foram adicionados em dezembro de 2014 ou posteriormente." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="genemi"/>


# O que há de novo no banco de dados SQL V12


<!--
GeneMi , 05-março-2015, quinta-feira 20:06pm
Remover 'Azure' do título do tópico, acrescentar 'V12' (conforme J.G.).
-->



Versão V12 do banco de dados SQL foi promovida para disponibilidade geral (GA) em janeiro de 2015. Com essa importante atualização, o banco de dados SQL agora fornece quase total compatibilidade com o mecanismo do Microsoft SQL Server. O banco de dados SQL agora traz mais desempenho Premium para os clientes. Esses aprimoramentos ajudam a simplificar as migrações de aplicativos do SQL Server para o banco de dados SQL e ajudar os clientes que têm cargas mais pesadas de banco de dados.


Algumas regiões geográficas ainda têm o status de visualização e não estão disponíveis. Localize sua região na [tabela de disponibilidade geral por região](../sql-database-preview-whats-new/#V12AzureSqlDbPreviewGaTable). Até que região esteja disponível no mercado, a V12 é mais adequada para testar bancos de dados, em vez de bancos de dados de produção.


**[Inscreva-se](https://portal.azure.com) no Banco de Dados SQL para aproveitar essa nova geração no Microsoft Azure. É necessário primeiro uma assinatura do Microsoft Azure. Você pode inscrever-se para uma [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial) e examinar as informações de [preços.](http://azure.microsoft.com/pricing/details/sql-database)**


O caminho para o planejamento e atualizar seus bancos de dados V11 para V12 inicia em [Planejar e preparar para atualizar para a mais recente atualização de banco de dados SQL V12 (visualização)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


### Principais destaques


Principais destaques do Banco de Dados SQL do Azure V12 incluem o seguinte:


- **Segurança** aproveite o novo recurso de [usuários em bancos de dados independentes](../sql-database-preview-whats-new/#UsersInContainedDatabases). Há dois outros recursos [segurança em nível de linha](../sql-database-preview-whats-new/#RowLevelSecurity) e [mascaramento de dados dinâmicos](../sql-database-preview-whats-new/#DynamicDataMasking), embora eles ainda estão em visualização e ainda não estão disponíveis no mercado.


- **Gerenciamento mais fácil** de grandes bancos de dados para oferecer suporte a cargas de trabalho mais pesadas com consultas paralelas (Premium somente), [particionamento de tabela](http://msdn.microsoft.com/library/ms187802.aspx)[, indexação online](http://msdn.microsoft.com/library/ms188388.aspx), recompilações de grandes índices sem preocupações com remoção do limite de tamanho de 2 GB e mais opções na instrução de[alter database](http://msdn.microsoft.com/library/bb522682.aspx).


- **Suporte para as principais funções de programação** para orientação de design do aplicativo mais robustos com [integração CLR](http://msdn.microsoft.com/library/ms189524.aspx), funções da janela [T-SQL](http://msdn.microsoft.com/library/ms189461.aspx)[, índices XML](http://msdn.microsoft.com/library/bb934097.aspx), e [o controle de alterações](http://msdn.microsoft.com/library/bb933875.aspx) para dados.


- **Desempenho inovador** com suporte para consultas [columnstore](http://msdn.microsoft.com/library/gg492153.aspx) na memória (somente para a camada Premium) para menores cargas de trabalho analíticas e armazém de dados.


- **Monitoramento e Solução de problemas** foram aprimorados com visibilidade em mais de 100 novas exibições de tabela em um conjunto expandido de Exibições de Gerenciamento de banco de dados ([DMV](http://msdn.microsoft.com/library/ms188754.aspx))


- **Novo nível de desempenho S3 na camada padrão:** oferece mais flexibilidade de preço entre Standard e Premium. O S3 fornecerá mais DTU (unidades de taxa de transferência de banco de dados) e todos os recursos disponíveis na camada Standard.


## 1. Detalhes dos aprimoramentos da nova V12 explicados


Esta seção menciona e explica os novos recursos em cada categoria.


### 1.1 Categoria: Gerenciamento de banco de dados expandido


| Recurso | Descrição |
| :--- | :--- |
| . | ***Dezembro de 2014:*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>Usuários em bancos de dados independentes | Agora você pode criar usuários no banco de dados independente sem ter um logon correspondente no banco de dados mestre. Isso torna muito mais simples mover o banco de dados para outro servidor. O código de conexão em seus aplicativos de cliente é o mesmo se você usar usuários de banco de dados independentes ou não.<br/><br/>O uso desse recurso pode ser necessário para clientes que desejam desfrutar de contratos de nível de serviço garantido superiores.<br/><br/>Geralmente, encorajamos os clientes a considerar o uso desse recurso. No entanto, alguns clientes podem ter cenários específicos que tornam o par *login+user* tradicional a melhor opção para você neste momento.<br/><br/>Para obter mais informações, consulte:<br/>- [Limitações e diretrizes de segurança de Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/>[Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/>- [Bancos de dados independentes](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| Partições de tabela | Limitações anteriores no [particionamento de tabela](http://msdn.microsoft.com/library/ms190787.aspx) são eliminadas. |
| Transações maiores | Com a V12 você não está mais limitado a um máximo de 2 GB de modificações de dados em uma única transação. <br/><br/> Um benefício é que a recompilação de um índice grande não está mais limitada pelo limite de tamanho de transação de 2 GB. Para obter informações gerais, consulte [Limites de recursos de banco de dados de SQL Azure](http://msdn.microsoft.com/library/azure/dn338081.aspx). |
| Compilação e recompilação e índices online | Antes de V12, o Banco de Dados SQL do Azure geralmente tinha suporte para a cláusula (ONLINE = ON) da instrução ALTER INDEX, mas isso não foi suportado para índices em uma coluna BLOB type. Agora a V12 oferece suporte para (ONLINE = ON) até mesmo para índices em colunas BLOB.<br/><br/> O recurso ONLINE permite consultas para aproveitar um índice, mesmo enquanto o índice estiver sendo recompilado. |
| Suporte ao PONTO DE VERIFICAÇÃO | Com a V12 você pode emitir a instrução T-SQL CHECKPOINT para o banco de dados. |
| Aprimoramento de ALTER TABLE | Permite que muitas alterações na coluna ações sejam realizadas enquanto a tabela permanece disponível. Para obter mais informações, consulte [ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms190273.aspx) |
| Aprimoramento de TRUNCATE TABLE | Permite o truncamento de partições específicas. Para obter mais informações, consulte [TRUNCATE TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx). |
| Mais opções de ALTER DATABASE | A V12 oferece suporte a mais das opções que estão disponíveis na instrução ALTER DATABASE. <br/><br/> Para obter mais informações, consulte [ALTER DATABASE (Transact-SQL)](http://msdn.microsoft.com/library/ms174269.aspx) ou a [Referência Transact-SQL do Azure SQL Database](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Mais comandos DBCC | Vários comandos DBCC agora estão disponíveis em V12. Para obter detalhes, consulte [Referência do Transact-SQL do banco de dados SQL Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |


### 1.2 Categoria: Suporte a programação e aplicativos


| Recurso | Descrição |
| :--- | :--- |
| . | ***Fevereiro de 2015:*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> Visualização de mascaramento dos dados dinâmicos | Quando um conjunto de linhas é gerado a partir de uma consulta, uma política de mascaramento dos dados estabelecida pode substituir partes dos dados com caracteres 'X' para sobrepor e proteger informações confidenciais. Após a operação de mascaramento ser concluída, o conjunto de linhas modificado é enviado ao cliente.<br/><br/>Um exemplo de uso pode ser a máscara de todos, menos alguns dígitos finais de um número de cartão de crédito.<br/><br/>**Observação:** Esse recurso está no status de visualização e ainda não foi anunciada a disponibilidade geral para uso em produção.<br/><br/>Para obter informações detalhadas, consulte [Introdução ao mascaramento de dados dinâmicos do banco de dados SQL Azure](http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/). |
| . | ***Janeiro de 2015:*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> Visualização de segurança de nível de linha (RLS) | **Cuidado:** O recurso RLS está atualmente em *preview* status apenas, mesmo em regiões geográficas onde a V12 está em status de disponibilidade geral (DG). Até que a RLS esteja no status DG, a RLS ainda não é adequado para uso em um banco de dados de produção crítico de negócios.<br/><br/>A nova instrução CREATE SECURITY POLICY em T-SQL permite que você implemente a RLS. A RLS faz com que o servidor de banco de dados adicione condições que filtram algumas linhas de dados antes de um conjunto de linhas ser retornado ao chamador.<br/><br/>No setor, a RLS também é chamada algumas vezes de controle de acesso refinado.<br/><br/>Para um exemplo de código e muito mais, consulte [Visualização de segurança a nível de linha](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx). |
| . | ***Dezembro de 2014:*** |
| Funções de janela em consultas do T-SQL | As funções de janela ANSI são acessadas com a [cláusula OVER](http://msdn.microsoft.com/library/ms189461.aspx). <br/><br/> Itzik Ben-Gan possui uma excelente [publicação no blog](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1) que explica detalhadamente as funções de janela e a cláusula OVER. |
| Integração CLR do .NET | O CLR (common language runtime) do .NET Framework é integrado ao V12. <br/><br/> Somente assemblies SAFE que não eram totalmente compiladas para código binário são suportadas. Para obter detalhes, consulte [Restrições do Modelo de programação de integração de CLR](http://msdn.microsoft.com/library/ms403273.aspx). <br/><br/> Você pode encontrar informações sobre esse recurso nos tópicos a seguir: <br/> *[Introdução à integração de CLR do SQL Server](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY (Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx). |
| Controle de alterações de dados | O controle de alterações de dados agora podem ser configurado no banco de dados ou nível de tabela. <br/><br/> Para obter informações sobre o controle de alterações, consulte [Sobre Controle de transações (SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx). |
| Índices XML | A V12 permite que você use as instruções T-SQL CREATE XML INDEX e CREATE SELECTIVE XML INDEX. <br/><br/> Informações sobre os índices XML estão disponíveis em: <br/> * [CREATE XML INDEX (Transact-SQL)](http://msdn.microsoft.com/library/bb934097.aspx) <br/> *[Criar, alterar e remover índices XML seletivos](http://msdn.microsoft.com/library/jj670109.aspx) |
| Tabela como um heap | A V12 permite que você crie uma tabela que não tem nenhum índice clusterizado. <br/><br/> Esse recurso é especialmente útil para o suporte da instrução T-SQL SELECT...INTO que cria uma tabela a partir de um resultado de consulta. |
| Funções de aplicativo | Para controle de segurança e permissões, a V12 permite emitir instruções GRANT - DENY - REMOVE em [funções de aplicativo](http://msdn.microsoft.com/library/ms190998.aspx). |


### 1.3 Categoria: Melhores resultados ao cliente


| Recurso | Descrição |
| :--- | :--- |
| . | ***Dezembro de 2014:*** |
| DMVs (Exibições de Gerenciamento Dinâmico) | Várias DMVs são adicionadas à V12. Para obter detalhes, consulte [Referência do Transact-SQL do banco de dados SQL Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Controle de alterações | A V12 é totalmente compatível com o controle de alterações. <br/><br/> Para obter detalhes desse recurso, consulte [Habilitar e desabilitar o Controle de alterações (SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx). |


### 1.4 Melhorias de desempenho na camada de serviço Premium


Esses aprimoramentos de desempenho se aplicam aos níveis P2 e P3 dentro da camada de serviço Premium.


| Recurso | Descrição |
| :--- | :--- |
| . | ***Dezembro de 2014:*** |
| Processamento paralelo para consultas | A V12 fornece um maior grau de paralelismo para consultas que podem se beneficiar dele. |
| Latência de e/s mais resumida | A V12 tem latência significativamente mais resumida para operações de entrada/saída. |
| Aumento de IOPS | A V12 pode processar uma quantidade maior de entrada/saída por segundos (IOPS). |
| Taxa de log | A V12 pode fazer mais alterações de dados por segundo. |


### 1.5 Resumo dos aprimoramentos


A V12 eleva nosso Banco de Dados SQL próximo a compatibilidade completa de recursos com nosso produto SQL Server. A V12 concentra-se nos recursos mais populares e no suporte à programação. Isso torna o desenvolvimento mais eficiente e mais agradável.  Agora é ainda mais fácil mover seus aplicativos do banco de dados SQL para a nuvem.


E na camada Premium a V12 traz mais melhorias de desempenho. Alguns aplicativos perceberão ganhos extremos na velocidade de consulta. Aplicativos com cargas de trabalho pesadas perceberão a taxa de transferência confiável robusta.


## <a name="V12AzureSqlDbPreviewGaTable"></a>2. Status de disponibilidade geral (DG) da V12 por região.


A versão V12 do banco de dados do SQL Azure foi lançada apenas para visualização e testes em dezembro de 2014, nas regiões indicadas na tabela a seguir. A alteração do status de visualização para a versão de disponibilidade geral (DG) completa está ocorrendo em datas diferentes dependendo da região. A tabela a seguir exibe o status atual do lançamento do V12 para cada região.


> [AZURE.NOTE]
> [Os preços](http://azure.microsoft.com/pricing/details/sql-database/) durante a visualização tem sido com desconto. Os preços retornam ao nível de DG para todas as regiões em 31 de março de 2015.


| Região do Azure | Versão atual<br/>status da V12 | Data da promoção<br/>para DG |
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
| Sul do Brasil | Não disponível | Estimado para o terceiro trimestre de 2015 |
| Leste da Austrália | Visualização | Estimado para os segundo trimestre de 2015 |
| Sudeste da Austrália | Visualização | Estimado para os segundo trimestre de 2015 |


Para qualquer região que tenha atingido a DG, todas as novas inscrições e seus bancos de dados subsequentes usam a arquitetura de serviço V12 e, portanto, têm acesso aos recursos mais recentes. O presente artigo lista os novos recursos trazidos pela V12.


Na DG, os clientes com servidores pré-V12 e bancos de dados poderão optar por atualizar (ou mover) seus bancos de dados para a arquitetura de serviço V12 para usar esses novos recursos para produção. Os banco de dados V12 devem estar na [faixa de preços](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/) basic, standard ou premium.


## 3. Como proceder


Você pode aprender como atualizar seus bancos de dados do Banco de Dados SQL do Azure V11 para V12 em [Planejar e preparar para atualizar para a mais recente visualização de atualização de Banco de Dados SQL do Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


Números de versão como V12 referem-se ao valor retornado pela instrução Transact-SQL a seguir:<br/>
**Selecione @@version;**


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(ou um pouco maior, V12)*


*Desconto:* Durante o período de visualização V12, você pode usar V12 em um valor de desconto. O novo nível S3 oferece 100 DTU (unidades de taxa de transferência de banco de dados) e todos os recursos disponíveis na camada Standard por US US$ 0,2016 por hora. O preço é descontado até US$ 0,1008 por hora durante a visualização V12. Para obter detalhes, consulte [Preços de Banco de dados SQL](http://azure.microsoft.com/pricing/details/sql-database/).


## 4. Precauções para a visualização V12


Certifique-se sobre os seguintes avisos sobre a atualização e pós-atualização para atualização de banco de dados V12 SQL Azure (visualização).


### 4.1 Portal de visualização para V12


Somente o primeiro dos dois portais de gerenciamento do Azure a seguir oferece suporte ao bancos de dados V12:


- [http://portal.azure.com/](http://portal.azure.com/)
 - Esse portal mais recente é o status de visualização e ainda não está em disponibilidade geral (DG).<br/><br/>
- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)
 - Esse portal mais antigo não será atualizado para oferecer suporte a V12.


Encorajamos os clientes a conectarem a seus bancos de dados SQL do Azure com o Visual Studio 2013 (VS2013). O VS2013 pode ser usado para tarefas como as seguintes:


- Para executar uma instrução T-SQL.
- Para criar um esquema.
- Para desenvolver um banco de dados online ou offline.


Em vez disso, você pode se conectar ao [Visual Studio Community 2013](https://www.visualstudio.com/pt-br/news/vs2013-community-vs.aspx/), que é uma versão gratuita e completa do VS2013.


No portal de gerenciamento do Azure mais antigo, na página de banco de dados, você pode clicar em **Aberto no Visual Studio** para iniciar o VS2013 no seu computador para conexão com o Banco de Dados SQL do Azure.


Para outra alternativa, você pode usar o SQL Server Management Studio (SSMS) 2014 com [CU6](http://support.microsoft.com/kb/3031047/) para se conectar ao Banco de Dados SQL do Azure. Mais detalhes estão nesta postagem do blog:<br/>[Cliente de atualizações de ferramentas para o Banco de Dados SQL do Azure](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


### 4.2 Precauções *during* a atualização para V12


> [AZURE.NOTE]
> O banco de dados pré-V12 *remains available* para acesso a dados durante a atualização para V12.


- Para bancos de dados maiores que 50 GB, a atualização para a V12 pode levar até 24 horas.
- Durante a atualização de um banco de dados para um servidor de Banco de Dados SQL V12, você não pode executar as seguintes ações no servidor até que a atualização esteja concluída:
 - Criar um novo banco de dados.
 - Copiar um banco de dados para o servidor.
 - Restaurar um banco de dados excluído.
 - Restaurar um banco de dados para um momento específico.
 - Replicação Geográfica.
- Desde o momento em que uma atualização para a V12 for concluída, o sistema precisa de alguns minutos para atualizar a entrada do sistema de nome de domínio (DNS) para a V12 do seu banco de dados. O aplicativo cliente pode se conectar ao banco de dados após a atualização de DNS.
- A faixa de preços do serviço Web e Business não é suportada no V12. Nem terá suporte em qualquer versão futura.


### 4.3 Precauções *after* a atualização para V12


- Qualquer banco de dados que é atualizado no local para a V12 não pode ser revertido de volta para a versão anterior.
- O desconto de 50% de custo para a V12 do banco de dados do SQL Azure está em vigor em todas as regiões geográficas até expirar na terça-feira, 31 de março de 2015. O desconto se aplica às regiões, para os dois status de visualização e DG.


### 4.4 Exportar e importar *after* a atualização para a V12


Você pode exportar ou importar um banco de dados V12 usando o [portal da web do Azure](http://portal.azure.com/). Ou você pode exportar ou importar usando qualquer uma das seguintes ferramentas:


- SQL Server Management Studio (SSMS) 2014
- Visual Studio 2013
- Estrutura de Aplicativo da Camada de Dados (DacFX)


No entanto, para usar as ferramentas, primeiro instale as atualizações mais recentes para garantir que elas oferecem suporte aos novos recursos V12:


- [Atualização cumulativa 6 do SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Atualização de fevereiro de 2015 da ferramenta de banco de dados do SQL Server no Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Estrutura de Aplicativo da Camada de Dados (DacFx) de fevereiro de 2015 para o Banco de Dados SQL do Azure V12](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] Os links a ferramenta anteriores foram atualizados em ou após 2 de março de 2015. É recomendável que você use essas atualizações mais recentes dessas ferramentas.


[2. Status de disponibilidade geral (DG) da V12 por região]:#V12AzureSqlDbPreviewGaTable


<!-- EndOfFile -->


<!--HONumber=47-->
 