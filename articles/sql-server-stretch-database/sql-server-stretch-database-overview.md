---
title: Arquivar dados frios no Azure - Stretch Database | Microsoft Docs
description: Saiba como o Stretch Database migra seus dados passivos de forma transparente e segura para a nuvem do Microsoft Azure.
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: c360dc10-a02b-446f-91a0-278358f7a297
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/27/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: cfa45dbb2d6e7579ed151c0213d394581cb73c4d
ms.openlocfilehash: 0c4355eb424f169238f274241747acff4532d589


---
# <a name="introduction-to-stretch-database"></a>Introdução ao Stretch Database
O Stretch Database migra seus dados passivos de forma transparente e segura para a nuvem do Microsoft Azure.

Se você deseja começar com o Stretch Database imediatamente, consulte [Introdução executando Habilitar Banco de Dados para o assistente Stretch](sql-server-stretch-database-wizard.md).

## <a name="what-are-the-benefits-of-stretch-database"></a>Quais são os benefícios do Stretch Database?
O Stretch Database oferece os seguintes benefícios:

### <a name="provides-cost-effective-availability-for-cold-data"></a>Fornece disponibilidade econômica para dados frios
Estenda dinamicamente os dados transacionais ativos e frios do SQL Server para o Microsoft Azure usando o SQL Server Stretch Database. Ao contrário do armazenamento comum de dados frio, seus dados estão sempre online e disponíveis para consulta. Você pode fornecer uma retenção de dados mais longa, sem ir à falência, para tabelas grandes como o Histórico de Pedidos do Cliente. Beneficie-se do baixo custo do Azure em vez de expandir um armazenamento local caro. Escolha o tipo de preço e defina as configurações no Portal do Azure para manter controle sobre os custos. Escale vertical ou horizontalmente conforme o necessário. Visite a página [Preços do SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) para obter detalhes.

### <a name="doesnt-require-changes-to-queries-or-applications"></a>Não requer alterações em consultas ou em aplicativos
Acesse os dados do SQL Server de forma direta, não importa se eles são locais ou estendidos para a nuvem.  Defina a política que determina onde os dados são armazenados, e o SQL Server tratará da movimentação dos dados em segundo plano. A tabela inteira está sempre online e passível de consulta. Além disso, o Stretch Database não exige mudanças nos aplicativos ou consultas existentes. O local dos dados é completamente transparente ao aplicativo.

### <a name="streamlines-on-premises-data-maintenance"></a>Simplifica a manutenção de dados locais
Reduza a manutenção e o armazenamento local para seus dados. Os backups de seus dados locais são executados e concluídos com mais rapidez dentro da janela de manutenção. Os backups para a parte de seus dados que está na nuvem são executados automaticamente. Suas necessidades de armazenamento local reduzem bastante. O Armazenamento do Azure pode ser 80% mais barato do que adicionar SSD local.

### <a name="keeps-your-data-secure-even-during-migration"></a>Mantém seus dados seguros mesmo durante a migração
Aproveite a tranquilidade enquanto amplia, com segurança, os seus aplicativos mais importantes para a nuvem. Sempre Criptografado do SQL Server fornece criptografia para seus dados em movimento. RLS (Segurança de Nível de Linha) e outros recursos de segurança avançados do SQL Server também funcionam com o Stretch Database para proteger seus dados.

## <a name="what-does-stretch-database-do"></a>Qual é a função do Stretch Database?
Depois que você habilita o Stretch Database para uma instância, um banco de dados e pelo menos uma tabela do SQL Server, o Stretch Database começa a migrar silenciosamente os dados passivos para o Azure.

* Se armazenar os dados inativos em uma tabela separada, você poderá migrar toda a tabela.
* Se sua tabela contiver dados ativos e inativos, você poderá especificar uma função de filtro para selecionar as linhas para migrar.

**Você não precisa alterar as consultas e aplicativos de cliente existentes.** Você continua a ter um acesso contínuo aos dados locais e remotos, mesmo durante a migração dos dados. Há uma pequena quantidade de latência para consultas remotas, mas você enfrentará essa latência apenas ao consultar dados passivos.

**O Stretch Database assegura que nenhum dado será perdido** se ocorrer uma falha durante a migração. Ele também tem uma lógica de repetição para lidar com problemas de conexão que podem ocorrer durante a migração. Um modo de exibição de gerenciamento dinâmico fornece o status da migração.

**Você pode pausar a migração de dados** para solucionar problemas no servidor local ou para maximizar a largura de banda de rede disponível.

![Visão geral do Stretch Database][StretchOverviewImage1]

## <a name="is-stretch-database-for-you"></a>O Stretch Database serve para você?
Se você puder fazer as seguintes declarações, o Stretch Database poderá ajudar a atender às suas necessidades e a resolver seus problemas.

| Se você for um tomador de decisões | Se você for um DBA |
| --- | --- |
| Preciso manter dados transacionais por um longo período. |O tamanho das minhas tabelas está saindo do controle. |
| Às vezes, eu preciso consultar os dados passivos. |Meus usuários dizem que querem acesso aos dados passivos, mas raramente os usam. |
| Tenho aplicativos, incluindo aplicativos mais antigos, que não quero atualizar. |Preciso comprar e adicionar mais armazenamento frequentemente. |
| Quero encontrar um jeito de economizar dinheiro com armazenamento. |Não consigo fazer backup ou restaurar essas tabelas grandes no SLA. |

## <a name="what-kind-of-databases-and-tables-are-candidates-for-stretch-database"></a>Quais tipos de bancos de dados e tabelas são candidatos ao Stretch Database?
O Stretch Database destina-se a bancos de dados transacionais com grandes quantidades de dados passivos, geralmente armazenados em uma pequena quantidade de tabelas. Essas tabelas podem conter mais de um bilhão de linhas.

Se você usar o recurso de tabela temporal do SQL Server 2016, use o Stretch Database para migrar toda ou parte da tabela de histórico associada para o armazenamento econômico no Azure. Para saber mais, confira [Gerenciar a retenção de dados históricos em Tabelas temporais com versão do sistema](https://msdn.microsoft.com/library/mt637341.aspx).

Use o Supervisor do Stretch Database, um recurso do Supervisor de Atualização do SQL Server 2016, para identificar os bancos de dados e tabelas candidatos ao Stretch Database. Para saber mais, consulte [Identificar bancos de dados e tabelas para o Stretch Database](sql-server-stretch-database-identify-databases.md). Para saber mais sobre os possíveis problemas de bloqueio, confira [Limitações do Stretch Database](sql-server-stretch-database-limitations.md).

## <a name="test-drive-stretch-database"></a>Testar o Stretch Database.
**Teste o Stretch Database com o banco de dados de exemplo AdventureWorks.** Para obter o banco de dados de exemplo AdventureWorks, baixe pelo menos o arquivo de banco de dados e o arquivo de exemplos e scripts [daqui](https://www.microsoft.com/download/details.aspx?id=49502). Depois de restaurar o banco de dados de exemplo para uma instância do SQL Server 2016, descompacte o arquivo de exemplos e abra o arquivo Stretch DB Samples da pasta do Stretch DB. Execute os scripts neste arquivo para verificar o espaço usado por seus dados antes e depois de habilitar o Stretch Database, para acompanhar o andamento da migração de dados e para confirmar que você pode continuar a consultar os dados existentes e a inserir novos dados durante e após a migração.

## <a name="next-step"></a>Próxima etapa
**Identificar os bancos de dados e tabelas que são candidatos ao Stretch Database.** Baixe o Supervisor de Atualização do SQL Server 2016 e execute o Supervisor do Stretch Database para identificar os bancos de dados e tabelas que são candidatos ao Stretch Database. O Supervisor do Banco de Dados de Stretch também identifica os problemas de bloqueio. Para saber mais, consulte [Identificar bancos de dados e tabelas para o Stretch Database](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png



<!--HONumber=Jan17_HO4-->


