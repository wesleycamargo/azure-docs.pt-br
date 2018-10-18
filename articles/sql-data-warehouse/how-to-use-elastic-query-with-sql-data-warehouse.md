---
title: Consulta elástica - acesso a dados no SQL Data Warehouse do Azure do Banco de Dados SQL do Microsoft Azure | Microsoft Docs
description: Saiba mais sobre as melhores práticas para usar a Consulta Elástica para o acesso a dados no SQL Data Warehouse do Azure a partir do Banco de Dados SQL do Microsoft Azure.
services: sql-data-warehouse
author: hirokib
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/11/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: d861e1d4cd891e1f1e1be3209ae4dfdbf4420165
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44718267"
---
# <a name="best-practices-for-using-elastic-query-in-azure-sql-database-to-access-data-in-azure-sql-data-warehouse"></a>Melhores práticas para usar a Consulta Elástica no Banco de Dados SQL do Microsoft Azure para o acesso a dados no SQL Data Warehouse do Azure
Saiba mais sobre as melhores práticas para usar a Consulta Elástica para o acesso a dados no SQL Data Warehouse do Azure a partir do Banco de Dados SQL do Microsoft Azure. 

## <a name="what-is-an-elastic-query"></a>O que é uma consulta elástica?
Uma consulta elástica permite usar tabelas externas e T-SQL para gravar uma consulta em um Banco de Dados SQL do Microsoft Azure que é enviado remotamente para um SQL Data Warehouse do Azure. Usar esse recurso proporciona economias de custo e arquiteturas de maior desempenho, dependendo do cenário.

Esse recurso possibilita dois cenários principais:

1. Isolamento de domínio
2. Execução de consultas remota

### <a name="domain-isolation"></a>Isolamento de domínio

Isolamento de domínio refere-se ao cenário de data mart clássico. Em determinados cenários, você poderá desejar fornecer um domínio lógico de dados a usuários downstream que estejam isolados o restante do data warehouse por vários motivos, incluindo, entre outros:

1. Isolamento de recursos – o banco de dados SQL é otimizado para atender a uma grande base de usuários simultâneos atendendo a cargas de trabalho levemente diferentes das consultas analíticas grandes às quais o data warehouse está reservado. Isolamento garante que as cargas de trabalho certas sejam atendidas pelas ferramentas corretas.
2. Isolamento de segurança – para separar um subconjunto de dados autorizados seletivamente por meio de determinados esquemas.
3. Área restrita – fornece um conjunto de dados de exemplo como um espaço para explorar as consultas de produção etc.

Consulta elástica pode permitir a fácil seleção de subconjuntos de dados do SQL Data Warehouse e transferência deles para uma instância do Banco de Dados SQL. Além disso, esse isolamento não impede a capacidade de também habilitar também a execução de consulta Remota, permitindo cenários de "cache" mais interessantes.

### <a name="remote-query-execution"></a>Execução de consultas remota

A consulta elástica permite a execução de consulta remota em uma instância do SQL Data Warehouse. É possível utilizar o melhor do banco de dados SQL e do SQL Data Warehouse, separando os dados quentes e frios entre os dois bancos de dados. Os usuários podem manter os dados mais recentes no banco de dados SQL, que pode atender a relatórios e a um grande número de usuários corporativos padrão. No entanto, quando forem necessário mais dados ou computação, um usuário poderá descarregar a parte da consulta para uma instância do SQL Data Warehouse em que agregações em grande escala podem ser processadas com muito mais rapidez e eficiência.

## <a name="elastic-query-process"></a>Processo de consulta elástica
Uma consulta elástica pode ser usada para disponibilizar os dados localizados em um SQL Data Warehouse disponíveis a instâncias do banco de dados SQL. Consulta elástica permite que consultas de um banco de dados SQL façam referência a tabelas em uma instância remota do SQL Data Warehouse. 

A primeira etapa é criar uma definição de fonte de dados externa que refira-se à instância do SQL data warehouse, que usa credenciais de usuário existentes dentro do SQL Data Warehouse. Não são necessárias alterações na instância remota do SQL Data Warehouse. 

> [!IMPORTANT] 
> 
> Você deve ter a permissão para ALTERAR QUALQUER FONTE DE DADOS EXTERNA. Essa permissão está incluída na permissão ALTERAR BANCO DE DADOS. As permissões ALTERAR QUALQUER FONTE DE DADOS EXTERNA são necessárias para referenciar a fonte de dados remotas.

Em seguida, crie uma definição de tabela externa remota em uma instância do Banco de Dados SQL que aponta para uma tabela remota no SQL Data Warehouse. Quando uma consulta usa uma tabela externa, a parte da consulta que faz referência à tabela externa é enviada para a instância do SQL Data Warehouse a ser processada. Depois que a consulta for concluída, o conjunto de resultados é enviado de volta à instância do banco de dados SQL de chamada. Para obter um breve tutorial da configuração de uma Consulta Elástica entre banco de dados SQL e o SQL data warehouse, consulte [Configurar Consulta Elástica com o SQL Data Warehouse][Configure Elastic Query with SQL Data Warehouse].

Para obter mais informações sobre Consulta Elástica com o banco de dados SQL, consulte a [Visão geral de consulta elástica do Banco de Dados SQL do Azure][Azure SQL Database elastic query overview].

## <a name="best-practices"></a>Práticas recomendadas
Use essas melhores práticas para usar a consulta elástica com eficiência.

### <a name="general"></a>Geral

- Ao usar a execução de consulta remota, verifique se você está selecionando apenas as colunas necessárias e aplicando os filtros corretos. Não apenas isso aumenta a computação necessária, como também aumenta o tamanho do conjunto de resultados e, portanto, a quantidade de dados que precisam ser movidos entre as duas instâncias.
- Mantenha os dados para fins analíticos tanto no SQL Data Warehouse quanto no banco de dados SQL no columnstore clusterizado para desempenho analítico.
- Verifique se as tabelas de origem estão particionadas para a movimentação de dados e consulta.
- Verifique se as instâncias do banco de dados SQL usadas como cache são particionadas para permitir atualizações mais granulares e gerenciamento mais fácil. 
- O ideal é usar bancos de dados PremiumRS, pois eles oferecem benefícios analíticos do indexação de columnstore clusterizada com foco em cargas de trabalho com uso intensivo de E/S com desconto de bancos de dados Premium.
- Após os carregamentos, utilize colunas de identificação de data de carregamento ou efetiva para upserts nas instâncias de Banco de Dados SQL para manter a integridade da fonte de cache. 
- Crie um logon separado e um usuário em sua instância do SQL Data Warehouse para suas credenciais de Acesso Remoto do banco de dados SQL definido na fonte de dados externa. 

### <a name="elastic-querying"></a>Consulta Elástica

- Em muitos casos, alguém pode querer gerenciar um tipo de tabela ampliada, em que uma parte da sua tabela está no Banco de Dados SQL em cache dados de desempenho com o restante dos dados armazenados no SQL Data Warehouse. Você precisa de dois objetos no Banco de Dados SQL: uma tabela externa dentro do Banco de Dados SQL que faz referência à tabela base no SQL Data Warehouse, e a parte "em cache" da tabela no Banco de Dados SQL. Considere a criação de uma exibição pela parte superior da parte em cache da tabela e a tabela externa que une as tabelas e aplica os filtros que separam dados materializados no Banco de Dados SQL e no dados expostos do SQL Data Warehouse por meio de tabelas externas.

  Imagine que você deseja manter o ano mais recente dos dados em uma instância do Banco de Dados SQL. A tabela **ext.Orders** faz referência às tabelas de pedidos do data warehouse. O **dbo.Orders** representa os anos mais recentes dos dados na instância do Banco de Dados SQL. Em vez de solicitar aos usuários que decidam se desejam consultar uma tabela ou outra, crie uma exibição na parte superior das duas tabelas no ponto de partição do ano mais recente.

  ```sql
  CREATE VIEW dbo.Orders_Elastic AS
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]
  FROM
    [dbo].[Orders]
  WHERE 
    YEAR([o_orderdate]) >= '<Most Recent Year>'
  UNION
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]         
  FROM
    [ext].[Orders]
  WHERE
    YEAR([o_orderdate]) < '<Most Recent Year>'
  ```

  Uma exibição produzida assim permite ao compilador de consulta determinar se ele precisa usar a instância data warehouse para responder a consultas dos usuários. 

  Há sobrecarga em enviar, compilar, executar e mover dados associados a cada consulta elástica em relação à instância do data warehouse. Saiba que cada consulta elástica conta em relação a seus slots de simultaneidade e usa recursos.  


- Se alguém planejar fazer mais drill down no conjunto de resultados da instância do data warehouse, considere materializá-lo em uma tabela temporária no Banco de Dados SQL para desempenho e para evitar o uso de recursos desnecessários.

### <a name="moving-data"></a>Movimentação de dados 

- Se possível, mantenha o gerenciamento de dados mais fácil com tabelas de origem somente de acréscimo de modo que atualizações sejam de fácil manutenção entre as instâncias de data warehouse e banco de dados.
- Mova dados no nível da partição com a semântica de liberação e preenchimento para minimizar o custo da consulta no nível do data warehouse e a quantidade de dados movidos para manter a instância de banco de dados atualizada. 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>Quando escolher o Azure Analysis Services versus Banco de Dados SQL

Use o Azure Analysis Services quando:

- Você planeja usar o cache com uma ferramenta de BI que envie grandes números de pequenas consultas
- Você precisa de uma latência de consulta inferior a um segundo
- Você têm experiência em gerenciar/desenvolver modelos para o Analysis Services 

Use o Banco de Dados SQL do Microsoft Azure quando:

- Você deseja consultar os dados do cache com SQL
- Você precisa de execução remota para determinadas consultas
- Você precisa de um cache maior

## <a name="faq"></a>Perguntas frequentes

P: Posso usar bancos de dados em um pool elástico com o Elastic Query?

R: Sim. Bancos de dados SQL em um pool elástico podem usar o Elastic Query. 

P: Há um limite a quantos bancos de dados eu posso usar para consulta elástica?

R: Não há um limite a quantos bancos de dados eu posso usar para consulta elástica. No entanto, cada consulta elástica (consultas que atingem o SQL Data Warehouse) contará para limites de simultaneidade normais.

P: Há limites de DTU envolvidos na consulta elástica?

R: Os limites de DTU não são impostos de maneira diferente da consulta elástica. A política padrão é que servidores lógicos têm limites de DTU em vigor para impedir que os clientes gastando demais acidentalmente. Se você estiver habilitando vários bancos de dados para consulta elástica junto com uma instância do SQL Data Warehouse, poderá atingir o limite inesperadamente. Se isso ocorrer, envie uma solicitação para aumentar o limite de DTU em seu servidor lógico. Você pode aumentar sua cota [criando um tíquete de suporte](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) e selecionando *Cota* como o tipo de solicitação

P: Posso usar Segurança em Nível de Linha/Máscara de Dados Dinâmicos com Consulta Elástica?

R: clientes de desejem usar recursos de segurança mais avançados com o Banco de Dados SQL podem fazer isso primeiro movendo e armazenando os dados no Banco de Dados SQL. No momento, não é possível aplicar a segurança em nível de linha nem DDM nos dados consultados por meio de tabelas externas. 

P: Posso gravar da minha instância do banco de dados SQL para a instância do data warehouse?

R: Não há suporte para esse recurso no momento. Visite nossa [página de Comentários][Feedback page] para criar/votar para essa funcionalidade se esse for um recurso que você gostaria de ver no futuro. 

P: Posso usar tipos espaciais como geometria/geografia?

R: Você pode armazenar tipos espaciais no SQL Data Warehouse como valores varbinary(max). Ao consultar essas colunas usando consulta elástica, você pode convertê-las para os tipos apropriados no tempo de execução.

![tipos espaciais](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)

<!--Article references-->

[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Configure Elastic Query with SQL Data Warehouse]: tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md


