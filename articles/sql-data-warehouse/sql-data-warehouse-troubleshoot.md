---
title: "Solução de problemas do Azure SQL Data Warehouse | Microsoft Docs"
description: "Solução de problemas do Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 18d3a5eca0b272a3fbe48c06e668c33aff2b3f11


---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Solução de problemas do Azure SQL Data Warehouse
Este tópico lista algumas das perguntas mais comuns que ouvimos de nossos clientes sobre solução de problemas.

## <a name="connecting"></a>Conexão
| Problema | Resolução |
|:--- |:--- |
| Falha de logon do usuário 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, erro: 18456) |Esse erro ocorre quando um usuário do AAD tenta se conectar no banco de dados mestre, mas não tem um usuário no mestre.  Para corrigir esse problema, especifique o SQL Data Warehouse que deseja conectar no momento da conexão ou adicione o usuário ao banco de dados mestre.  Confira o artigo [Visão geral da segurança][Visão geral da segurança] para obter mais detalhes. |
| A entidade de segurança do servidor "MyUserName" não é capaz de acessar o banco de dados "mestre" no contexto de segurança atual. Não é possível abrir o banco de dados padrão do usuário. Falha no logon. Falha de logon do usuário 'MyUserName'. (Microsoft SQL Server, erro: 916) |Esse erro ocorre quando um usuário do AAD tenta se conectar no banco de dados mestre, mas não tem um usuário no mestre.  Para corrigir esse problema, especifique o SQL Data Warehouse que deseja conectar no momento da conexão ou adicione o usuário ao banco de dados mestre.  Confira o artigo [Visão geral da segurança][Visão geral da segurança] para obter mais detalhes. |
| Erro CTAIP |Esse erro pode ocorrer quando um logon tiver sido criado no banco de dados mestre do SQL Server, mas não no banco de dados do SQL Data Warehouse.  Se você encontrar esse erro, examine o artigo [Visão geral da segurança][Visão geral da segurança].  Este artigo explica como criar um logon e um usuário no mestre e, em seguida, como criar um usuário no banco de dados do SQL Data Warehouse. |
| Bloqueado pelo firewall |Bancos de Dados SQL do Azure são protegidos pelo servidor e firewalls de nível de banco de dados para garantir que somente os endereços IP conhecidos possam acessar um bancos de dados. Os firewalls são seguros por padrão, o que significa que você deve habilitar explicitamente um endereço IP ou um intervalo de endereços antes de se conectar.  Para configurar seu firewall para acesso, siga as etapas na seção [Configurar o acesso ao firewall do servidor para o IP do cliente][Configurar o acesso ao firewall do servidor para o IP do cliente] das [instruções de provisionamento][instruções de provisionamento]. |
| Não é possível conectar-se com a ferramenta ou driver |O SQL Data Warehouse recomenda o uso do [SSMS][SSMS], do [SSDT para Visual Studio 2015][SSDT para Visual Studio 2015] ou do [sqlcmd][sqlcmd] para consultar os dados. Para obter mais detalhes sobre os drivers e como se conectar ao SQL Data Warehouse, confira os artigos [Drivers do Azure SQL Data Warehouse][Drivers do Azure SQL Data Warehouse] e [Conectar-se ao SQL Data Warehouse do Azure][Conectar-se ao SQL Data Warehouse do Azure]. |

## <a name="tools"></a>Ferramentas
| Problema | Resolução |
|:--- |:--- |
| O Pesquisador de objetos do Visual Studio não tem usuários de AAD |Esse é um problema conhecido.  Como alternativa, exiba os usuários em [sys.database_principals][sys.database_principals].  Confira [Autenticação do Azure SQL Data Warehouse][Autenticação do Azure SQL Data Warehouse] para saber mais sobre como usar o Azure Active Directory com o SQL Data Warehouse. |

## <a name="performance"></a>Desempenho
| Problema | Resolução |
|:--- |:--- |
| Solucionar problemas de desempenho da consulta |Caso você esteja tentando solucionar problemas de uma determinada consulta, comece com [Aprendendo a monitorar suas consultas][Aprendendo a monitorar suas consultas]. |
| Planos e consultas com um desempenho ruim normalmente são o resultado da falta de estatísticas |A causa mais comum do mau desempenho é a falta de estatísticas em tabelas.  Confira [Como manter estatísticas de tabela][Estatísticas] para obter detalhes sobre como criar estatísticas e por que elas são essenciais para o desempenho. |
| Baixa simultaneidade/consultas em fila |É importante entender o [Gerenciamento de carga de trabalho][Gerenciamento de carga de trabalho] para compreender como equilibrar a alocação de memória com simultaneidade. |
| Como implementar as práticas recomendadas |O melhor lugar para começar a aprender maneiras de melhorar o desempenho de consulta é o artigo [Práticas recomendadas do SQL Data Warehouse][Práticas recomendadas do SQL Data Warehouse]. |
| Como melhorar o desempenho com o dimensionamento |Às vezes, a solução para aprimorar o desempenho é simplesmente adicionar mais poder de computação às suas consultas [Dimensionando seu SQL Data Warehouse][Dimensionando seu SQL Data Warehouse]. |
| Desempenho ruim da consulta como resultado da baixa qualidade do índice |Às vezes, as consultas podem apresentar lentidão devido à [Baixa qualidade do índice columnstore][Baixa qualidade do índice columnstore].  Consulte este artigo para saber mais e para saber como [Recriar índices a fim de melhorar a qualidade do segmento][Recriar índices a fim de melhorar a qualidade do segmento]. |

## <a name="system-management"></a>Gerenciamento do sistema
| Problema | Resolução |
|:--- |:--- |
| Msg 40847: não foi possível executar a operação, pois o servidor excederia a cota de Unidade de Transação de Banco de Dados permitida de 45000. |Reduza o [DWU][DWU] do banco de dados que você está tentando criar ou [solicitar um aumento de cota][solicitar um aumento de cota]. |
| Investigação da utilização de espaço |Confira [Tamanhos da tabela][Tamanhos da tabela] para entender a utilização do espaço de seu sistema. |
| Ajuda com o gerenciamento de tabelas |Confira o artigo [Visão geral da tabela][Visão geral] para obter ajuda com o gerenciamento de suas tabelas.  Esse artigo também inclui links para tópicos mais detalhados, como [Tipos de dados da tabela][Tipos de dados], [Distribuindo uma tabela][Distribuir], [Indexando uma tabela][Índice],  [Particionando uma tabela][Partition], [Mantendo estatísticas de tabela][Estatísticas] e [Tabelas temporárias][Temporário]. |

## <a name="polybase"></a>Polybase
| Problema | Resolução |
|:--- |:--- |
| Erro UTF-8 |No momento, o PolyBase somente dá suporte ao carregamento de arquivos de dados codificados em UTF-8.  Confira [Solução alternativa para o requisito de PolyBase UTF-8][Solução alternativa para o requisito de PolyBase UTF-8] para obter diretrizes sobre como contornar essa limitação. |
| Falha no carregamento devido a linhas grandes |Atualmente, o suporte para linhas grandes não está disponível para o Polybase.  Isso significa que, se a tabela contiver VARCHAR(MAX), NVARCHAR(MAX) ou VARBINARY(MAX), as Tabelas externas não poderão ser usadas para carregar seus dados.  Só há suporte no momento para carregamentos de linhas grandes por meio do Azure Data Factory (com BCP), Stream Analytics do Azure, SSIS, BCP ou a classe .NET SQLBulkCopy. O suporte do PolyBase para linhas grandes será adicionado em uma versão futura. |
| Falha de carregamento bcp de tabela com o tipo de dados MAX |Há um problema conhecido que exige o posicionamento de VARCHAR(MAX), NVARCHAR(MAX) ou VARBINARY(MAX) no final da tabela em alguns cenários.  Tente mover as colunas MAX para o final da tabela. |

## <a name="differences-from-sql-database"></a>Diferenças do Banco de Dados SQL
| Problema | Resolução |
|:--- |:--- |
| Recursos do Banco de Dados SQL sem suporte |Consulte [Recursos de tabela sem suporte][Recursos de tabela sem suporte]. |
| Tipos de dados do Banco de Dados SQL sem suporte |Consulte [Tipos de dados sem suporte][Tipos de dados sem suporte]. |
| Limitações de DELETE e UPDATE |Consulte [Soluções alternativas de MERGE][Soluções alternativas de MERGE], [Soluções alternativas de DELETE][Soluções alternativas de DELETE] e [Usando o CTAS para resolver as sintaxes DELETE e UPDATE sem suporte][Usando o CTAS para resolver as sintaxes DELETE e UPDATE sem suporte]. |
| Não há suporte para a instrução MERGE |Consulte [Substituir instruções MERGE][Substituir instruções MERGE]. |
| Limitações de procedimento armazenado |Confira [Limitações de procedimento armazenado][Limitações de procedimento armazenado] para entender algumas dessas limitações de procedimentos armazenados. |
| UDFs não oferecem suporte a instruções SELECT |Esta é uma limitação atual de nossos UDFs.  Confira [CREATE FUNCTION][CREATE FUNCTION] para ver a sintaxe para a qual damos suporte. |

## <a name="next-steps"></a>Próximas etapas
Se você não é capaz de encontrar uma solução para seu problema acima, aqui estão alguns outros recursos que você pode experimentar.

* [Blogs]
* [Solicitações de recursos]
* [Vídeos]
* [Blogs da equipe CAT]
* [Criar um tíquete de suporte]
* [Fórum do MSDN]
* [Fórum Stack Overflow]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Visão geral da segurança]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT para Visual Studio 2015]: ./sql-data-warehouse-install-visual-studio.md
[Drivers do Azure SQL Data Warehouse]: ./sql-data-warehouse-connection-strings.md
[Conectar-se ao SQL Data Warehouse do Azure]: ./sql-data-warehouse-connect-overview.md
[Criar um tíquete de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Dimensionando seu SQL Data Warehouse]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[solicitar um aumento de cota]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change 
[Aprendendo a monitorar suas consultas]: ./sql-data-warehouse-manage-monitor.md
[instruções de provisionamento]: ./sql-data-warehouse-get-started-provision.md
[Configurar o acesso ao firewall do servidor para o IP do cliente]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[Práticas recomendadas do SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Tamanhos da tabela]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Recursos de tabela sem suporte]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Tipos de dados sem suporte]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Visão geral]: ./sql-data-warehouse-tables-overview.md
[Tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[Distribuir]: ./sql-data-warehouse-tables-distribute.md
[Índice]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporário]: ./sql-data-warehouse-tables-temporary.md
[Baixa qualidade do índice columnstore]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Recriar índices a fim de melhorar a qualidade do segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Gerenciamento de carga de trabalho]: ./sql-data-warehouse-develop-concurrency.md
[Usando o CTAS para resolver as sintaxes DELETE e UPDATE sem suporte]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[Soluções alternativas de MERGE]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[Soluções alternativas de DELETE]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[Substituir instruções MERGE]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Limitações de procedimento armazenado]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Autenticação do Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md
[Solução alternativa para o requisito de PolyBase UTF-8]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs da equipe CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitações de recursos]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse




<!--HONumber=Nov16_HO3-->


