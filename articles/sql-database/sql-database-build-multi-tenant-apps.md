<properties
   pageTitle="O Banco de Dados SQL do Azure compila aplicativos multilocatário"
   description="Saiba como o Banco de Dados SQL compila aplicativos multilocatário"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/04/2016"
   ms.author="carlrab"/>

# Compila aplicativos multilocatário com o Banco de Dados SQL do Azure

## Aproveite os pools Elásticos e compile aplicativos multilocatário mais eficientes

Se você é um desenvolvedor de aplicativos SaaS escrevendo um aplicativo multilocatário e atendendo muitos clientes, normalmente é preciso fazer compromissos quanto à segurança, ao gerenciamento e ao desempenho do cliente. Com os Pools de Bancos de Dados elásticos do Banco de Dados SQL do Azure, você não precisa mais fazer esses compromissos. Esses pools ajudam você a gerenciar e a monitorar aplicativos multilocatário e a receber os benefícios do isolamento de um cliente por banco de dados.

![build-multi-tenant-apps](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

## Dimensionamento automático que você controla

Pools dimensionam automaticamente o desempenho e a capacidade de armazenamento para bancos de dados elásticos em tempo real. Você pode controlar o desempenho atribuído a um pool, adicionar ou remover bancos de dados elásticos sob demanda e definir o desempenho de bancos de dados elásticos sem afetar o custo geral do pool. Isso significa que você não precisa se preocupar quanto ao gerenciamento do uso de bancos de dados individuais.

[Ler a documentação](sql-database-elastic-pool.md)

## Gerenciamento inteligente de seu ambiente

Recomendações de dimensionamento internas identificam proativamente os bancos de dados que se beneficiariam de pools. Essas recomendações permitem um teste de hipóteses para otimização rápida, a fim de atender às suas metas de desempenho. Monitoramento de desempenho avançado e painéis de solução de problemas de desempenho lhe ajudam a visualizar o histórico de utilização de pool.

[Ler a documentação](sql-database-elastic-pool-guidance.md)

## Desempenho e preço para atender às suas necessidades

Pools Básicos, Standard e Premium fornecem a você um amplo espectro de opções de desempenho, armazenamento e preços. Pools podem conter até 400 bancos de dados elásticos. Bancos de dados elásticos podem escalar verticalmente de forma automática até 1000 eDTUs (unidades de transação de banco de dados elástico).

[Ler a documentação](https://azure.microsoft.com/pricing/details/sql-database/?b=16.50)

## Ferramentas elásticas

Além dos pools elásticos, há recursos de banco de dados SQL para ajudar a gerenciar atividades operacionais em vários bancos de dados:

** Realize consultas e gere relatórios em bancos de dados. ** [A consulta de banco de dados elástico](sql-database-elastic-query-overview.md) permite que você execute consultas ou relatórios em bancos de dados em seu pool elástico e acesse dados remotos armazenados em vários bancos de dados de seu pool de uma só vez.

** Execute transações de banco de dados. ** [Transações de banco de dados elástico](sql-database-elastic-transactions-overview.md) permitem a execução de transações que abrangem vários bancos de dados em bancos de dados SQL e executam operações (por exemplo, ao processar transações financeiras em bancos de dados ou ao atualizar o estoque em um banco de dados e os em pedidos).

** Execute as mesmas operações em vários bancos de dados. ** [Trabalhos de banco de dados elástico](sql-database-elastic-jobs-overview.md) executam operações administrativas como recompilar índices ou atualizar esquemas em cada banco de dados em seu pool elástico.

Vá para a home page para ver o que mais o Banco de Dados SQL tem a oferecer. [Confira](https://azure.microsoft.com/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->