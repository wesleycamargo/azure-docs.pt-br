---
title: Identificar bancos de dados e tabelas do Banco de Dados de Stretch executando o Supervisor do Banco de Dados de Stretch | Microsoft Docs
description: "Saiba como identificar os bancos de dados e tabelas que são candidatos ao Banco de Dados de Stretch."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 9253c5a2-f135-4782-95ec-8ff39454c2c0
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 47b666a7f2178daebc1b2b442fd1bb8cf31e40bb
ms.openlocfilehash: 3aef66c7b80ee356faedcaaadacd15aea3438714


---
# <a name="identify-databases-and-tables-for-stretch-database-by-running-stretch-database-advisor"></a>Identificar bancos de dados e tabelas do Banco de Dados de Stretch executando o Supervisor do Banco de Dados de Stretch
Para identificar os bancos de dados e tabelas que são candidatos ao Stretch Database, baixe o Supervisor de Atualização do SQL Server 2016 e execute o Supervisor do Stretch Database. O Supervisor do Banco de Dados de Stretch também identifica os problemas de bloqueio.

## <a name="download-and-install-upgrade-advisor"></a>Baixe e instale o Supervisor de Atualização
Baixe e instale o Supervisor de Atualização [aqui](http://go.microsoft.com/fwlink/?LinkID=613421). Essa ferramenta não está incluída na mídia de instalação do SQL Server.

## <a name="run-the-stretch-database-advisor"></a>Executar o Supervisor do Banco de Dados de Stretch
1. Execute o Supervisor de Atualização.
2. Escolha **cenários** e escolha **EXECUTAR SUPERVISOR DO BANCO DE DADOS DE STRETCH**.
3. Na folha **Executar Supervisor do Banco de Dados de Stretch**, clique em **SELECIONAR BANCOS DE DADOS PARA ANALISAR**.
4. Na folha **Selecionar bancos de dados** , insira ou selecione o nome do servidor e as informações de autenticação. Clique em **Conectar**.
5. Uma lista de bancos de dados no servidor selecionado é exibida. Selecione os bancos de dados que você deseja analisar. Clique em **Selecionar**.
6. Na folha **Executar Supervisor do Stretch Database**, clique em **Executar**.  A análise é executada.

## <a name="review-the-results"></a>Revise os resultados
1. Quando a análise for concluída, na folha **Bancos de dados analisados**, escolha um dos bancos de dados que você analisou para exibir a folha **Resultados da análise**.
   
   A folha **Resultados da análise** relaciona as tabelas recomendadas no banco de dados selecionado que correspondem aos critérios de recomendação padrão.
2. Na lista de tabelas na folha **Resultados da análise**, escolha uma das tabelas recomendadas para exibir a folha **Resultados da tabela**.
   
   Se houver problemas de bloqueio, a folha **Resultados da tabela** relacionará os problemas de bloqueio da tabela selecionada. Para obter informações sobre os problemas de bloqueio detectados pelo Supervisor do Stretch Database, confira [Limitações do Stretch Database](sql-server-stretch-database-limitations.md).
3. Na lista de problemas de bloqueio da folha **Resultados da tabela** , selecione um dos problemas para exibir mais informações sobre o problema selecionado e proponha etapas de redução. Implemente as etapas de mitigação sugeridas se quiser configurar a tabela selecionada para o Banco de Dados de Stretch.

## <a name="next-step"></a>Próxima etapa
Habilite o Banco de Dados de Stretch.

* Para habilitar o Stretch Database em um **banco de dados**, confira [Enable Stretch Database for a database](sql-server-stretch-database-enable-database.md)(Habilitar o Stretch Database para um banco de dados).
* Para habilitar o Stretch Database em outra **tabela**, quando o Stretch já estiver habilitado no banco de dados, confira [Enable Stretch Database for a table](sql-server-stretch-database-enable-table.md)(Habilitar o Stretch Database para uma tabela).

## <a name="see-also"></a>Confira também
[Limitações do Stretch Database](sql-server-stretch-database-limitations.md)

[Habilitar o Banco de Dados de Stretch para um banco de dados](sql-server-stretch-database-enable-database.md)

[Habilitar o Banco de Dados de Stretch para uma tabela](sql-server-stretch-database-enable-table.md)




<!--HONumber=Jan17_HO1-->


