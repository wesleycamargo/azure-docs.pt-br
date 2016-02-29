<properties 
	pageTitle="O que há de novo na V12 do Banco de dados SQL | Microsoft Azure" 
	description="Descreve por que os sistemas de negócios que estão usando o Banco de Dados SQL do Azure na nuvem terão benefícios com a atualização para a versão V12 agora." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jhubbard" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="genemi"/>


# O que há de novo no Banco de Dados SQL V12


Este tópico descreve as muitas vantagens que a nova versão V12 do Banco de Dados SQL do Azure tem em relação à versão V11.


Continuamos a adicionar recursos à V12. Portanto, recomendamos que você visite a nossa página da Web de atualizações de serviço do Azure e use os seus filtros:


- Filtrado para o [Serviço de Banco de Dados SQL](https://azure.microsoft.com/updates/?service=sql-database).
- Filtrado para disponibilidade geral [Anúncios (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) para recursos do Banco de Dados SQL.


As informações mais recentes sobre os limites de recurso para o Banco de Dados SQL estão documentadas em:<br/>[Limites de Recurso de Banco de Dados SQL do Azure](sql-database-resource-limits.md).


## Maior compatibilidade do aplicativo com o SQL Server


O objetivo principal da V12 de Banco de dados SQL era melhorar a compatibilidade com o Microsoft SQL Server 2014. Entre outras áreas, a V12 atinge paridade com o SQL Server na importante área da programação. Por exemplo,


- [Funções de Janela](http://msdn.microsoft.com/library/bb934097.aspx), com [OVER](http://msdn.microsoft.com/library/ms189461.aspx) 
- [Índices XML](http://msdn.microsoft.com/library/bb934097.aspx) e [índices XML seletivos](http://msdn.microsoft.com/library/jj670104.aspx)
- [Controle de alterações](http://msdn.microsoft.com/library/bb933875.aspx)
- [SELECT...INTO](http://msdn.microsoft.com/library/ms188029.aspx)
- [Pesquisa de texto completo](http://msdn.microsoft.com/library/ms142571.aspx)


Consulte [aqui](sql-database-transact-sql-information.md) para obter um pequeno conjunto de recursos ainda não tem suporte no Banco de dados SQL.


## Mais desempenho Premium, novos níveis de desempenho


Na V12, aumentamos as unidades de taxa de transferência de banco de dados (DTUs) alocadas para todos os níveis de desempenho Premium em 25% sem custo adicional. Podem ser obtidos ganhos de desempenho ainda maiores com os novos recursos, como:


- Suporte para memória [índices columnstore](http://msdn.microsoft.com/library/gg492153.aspx).
- [Particionamento da tabela pelas linhas](http://msdn.microsoft.com/library/ms187802.aspx) com aprimoramentos relacionados [TRUNCATE TABLE](http://msdn.microsoft.com/library/ms177570.aspx).
- A disponibilidade das exibições de gerenciamento dinâmico [(DMVs)](http://msdn.microsoft.com/library/ms188754.aspx) para ajudar a monitorar e ajustar o desempenho.


### Desempenho confiável


Se o seu programa cliente se conecta ao Banco de Dados SQL V12 enquanto seu cliente é executado em uma máquina virtual do Azure (VM), abra os seguintes intervalos de porta na VM:

- 11000-11999
- 14000-14999


Clique [aqui](sql-database-develop-direct-route-ports-adonet-v12.md) para obter detalhes sobre as portas para o Banco de Dados SQL V12. As portas são exigidas por aprimoramentos de desempenho no Banco de Dados SQL V12.


## Melhor suporte para fornecedores de SaaS na nuvem


Somente na V12, lançamos o novo nível de desempenho padrão S3 e a demonstração pública do [Pool de Banco de Dados Elástico](sql-database-elastic-pool.md). Essa é uma solução projetada especificamente para os fornecedores de SaaS de nuvem. Com o Pool de Banco de Dados Elástico, você pode:


- Compartilhar DTUs entre bancos de dados para reduzir os custos para um grande número de bancos de dados.
- Executar [Trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-overview.md) para gerenciar bancos de dados em escala.


## Aprimoramentos de segurança


A segurança é uma preocupação para qualquer pessoa que execute os seus negócios na nuvem. Os recursos de segurança mais recentes lançados na V12 incluem:


- [Segurança em nível de linha](http://msdn.microsoft.com/library/dn765131.aspx) (RLS)
- [Mascaramento de dados dinâmicos](sql-database-dynamic-data-masking-get-started.md)
- [Bancos de dados independentes](http://msdn.microsoft.com/library/ff929188.aspx)
- [Funções de aplicativo](http://msdn.microsoft.com/library/ms190998.aspx) gerenciado com GRANT, DENY, REVOKE
- [Criptografia transparente de dados](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) (TDE)
- [Conectar-se ao Banco de Dados SQL usando a autenticação do Active Directory do Azure](sql-database-aad-authentication.md)
 - Agora, o Banco de Dados SQL oferece suporte à autenticação do Active Directory do Azure, um mecanismo de conexão com o Banco de Dados SQL usando identidades no Active Directory do Azure (Azure AD). Com a autenticação do Active Directory do Azure, você pode gerenciar centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em um único local central.
- [Sempre Criptografado](https://msdn.microsoft.com/library/mt163865.aspx) (na visualização) torna a criptografia transparente para os aplicativos e permite que os clientes criptografem dados confidenciais em aplicativos cliente sem compartilhar as chaves de criptografia com o Banco de Dados SQL.


## Maior continuidade dos negócios quando a recuperação for necessária


A V12 oferece objetivos de ponto de recuperação (RPOs) significativamente aprimorados e tempos de recuperação estimados (ERTs):


| Recursos da continuidade dos negócios | Versão anterior | V12 |
| :-- | :-- | :-- |
| Restauração geográfica | • RPO < 24 horas.<br/>• ERT < 12 horas. | • RPO < 1 hora.<br/>• ERT < 12 horas. |
| Replicação geográfica padrão | • RPO < 30 minutos.<br/>• ERT < 2 horas. | • RPO < 5 segundos.<br/>• ERT < 30 segundos. |
| Replicação geográfica ativa | • RPO < 5 minutos.<br/>• ERT < 1 hora. | • RPO < 5 segundos.<br/>• ERT < 30 segundos. |


Consulte [Continuidade de negócios do banco de dados SQL](sql-database-business-continuity.md) para obter mais informações.


## Mais motivos para atualizar agora


Existem vários bons motivos pelos quais os clientes devem atualizar agora da V11 para a V12 do Banco de Dados SQL do Azure:


- A V12 do Banco de dados SQL tem uma longa lista de recursos além daqueles já existentes na V11.
- Continuamos a adicionar novos recursos à V12, mas nenhum recurso novo será adicionado à V11.
- A maioria dos novos recursos é lançada na V12 do Banco de Dados SQL antes de eles serem lançados para o Microsoft SQL Server.


## Você já está usando a V12?


Uma maneira fácil de verificar se você tem um banco de dados ou servidor lógico em execução em uma versão anterior do serviço de Banco de dados SQL é fazendo o seguinte:


1. Vá para o [Portal do Azure](https://portal.azure.com/).
2. Clique em **Procurar**.
3. Clique em **Servidores SQL**.
4. O ícone ao lado de servidor ou banco de dados revela a história:
 - ![Ícone para um servidor V12](./media/sql-database-v12-whats-new/v12_icon.png) **Servidor lógico V12**
 - ![Ícone do servidor da versão anterior](./media/sql-database-v12-whats-new/earlier_icon.png) **Servidor lógico com versão anterior**


Outra técnica para determinar a versão é executar a instrução `SELECT @@version;` em seu banco de dados e exibir resultados semelhantes a:


- **12**.0.2000.10 &nbsp; *(versão V12)*
- **11**.0.9228.18 &nbsp; *(versão V11)*


Um banco de dados V12 pode ser hospedado somente em um servidor lógico V12. E um servidor V12 pode hospedar apenas bancos de dados V12.


Se você ainda não estiver usando a V12, pode atualizar o seu servidor lógico seguindo as etapas em [Atualizar para o Banco de Dados SQL V12 in-loco](sql-database-v12-plan-prepare-upgrade.md).


## <a name="V12AzureSqlDbPreviewGaTable"></a> Regiões de Disponibilidade Geral


- Em 31 de julho de 2015, todas as regiões tinham foi promovidas para Disponibilidade Geral (GA).
- A V12 foi lançada em dezembro de 2014, mas apenas com o status de Visualização.

[Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

<!---HONumber=AcomDC_0218_2016-->