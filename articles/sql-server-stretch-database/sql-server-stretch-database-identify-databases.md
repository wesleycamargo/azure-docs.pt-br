<properties
	pageTitle="Identificar bancos de dados e tabelas do Banco de Dados de Stretch executando o Supervisor do Banco de Dados de Stretch | Microsoft Azure"
	description="Saiba como identificar os bancos de dados e tabelas que são candidatos ao Banco de Dados de Stretch."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# Identificar bancos de dados e tabelas do Banco de Dados de Stretch executando o Supervisor do Banco de Dados de Stretch

Para identificar os bancos de dados e tabelas que são candidatos ao Banco de Dados de Stretch, baixe o Supervisor de Atualização do SQL Server 2016 e execute o Supervisor do Banco de Dados de Stretch. O Supervisor do Banco de Dados de Stretch também identifica os problemas de bloqueio.

## Baixe e instale o Supervisor de Atualização
Baixe e instale o Supervisor de Atualização [aqui](http://go.microsoft.com/fwlink/?LinkID=613421). Essa ferramenta não está incluída na mídia de instalação do SQL Server.

## Executar o Supervisor do Banco de Dados de Stretch

1.  Execute o Supervisor de Atualização.

2.  Escolha **cenários** e escolha **EXECUTAR SUPERVISOR DO BANCO DE DADOS DE STRETCH**.

3.  Na folha **Executar Supervisor do Banco de Dados de Stretch**, clique em **SELECIONAR BANCOS DE DADOS PARA ANALISAR**.

4.  Na folha **Selecionar bancos de dados**, clique em **INSTÂNCIA SQL**.

5.  Na folha **Conectar-se à Instância SQL**, insira o nome da instância do SQL Server. E clique em **Conectar**.

6.  Na folha **Selecionar bancos de dados**, selecione os bancos de dados para analisar. Em seguida, clique em **Selecionar**.

7.  Na folha **Executar Supervisor do Banco de Dados de Stretch**, clique em **Executar**. A análise é executada.

## Revise os resultados

1.  Quando a análise for concluída, na folha **Supervisor do Banco de Dados de Stretch**, escolha um dos bancos de dados analisados para exibir a folha **Resultados da análise**.

    A folha **Resultados da análise** lista as tabelas recomendadas no banco de dados selecionado que correspondem aos critérios de recomendação padrão. Você pode optar por ajustar a contagem mínima de tamanho e de linha para expandir ou reduzir a lista de tabelas recomendadas.

2.  Na lista de tabelas recomendadas na folha **Resultados da análise**, escolha uma das tabelas recomendadas para exibir a folha **Resultados da tabela**.

    A folha **Resultados da tabela** lista os problemas de bloqueio da tabela selecionada. Para obter informações sobre os problemas de bloqueio detectados pelo Supervisor do Banco de Dados de Stretch, confira [Limitações de área de superfície e problemas de bloqueio do Banco de Dados de Stretch](sql-server-stretch-database-limitations.md).

3.  Na lista de problemas de bloqueio na folha **Resultados da tabela**, escolha um dos problemas para exibir a folha **Resultado da regra**.

    A folha **Resultado da regra** descreve o problema selecionado e propõe etapas de mitigação. Implemente as etapas de mitigação sugeridas se quiser configurar a tabela selecionada para o Banco de Dados de Stretch.

## Próxima etapa
Habilite o Banco de Dados de Stretch.

-   Para habilitar o Stretch Database para um **banco de dados**, confira [Habilitar o Stretch Database para um banco de dados](sql-server-stretch-database-enable-database.md).

-   Para habilitar o Banco de Dados de Stretch em outra **tabela**, quando o Stretch já estiver habilitado no banco de dados, confira [Habilitar o Banco de Dados de Stretch para uma tabela](sql-server-stretch-database-enable-table.md).

## Consulte também

[Limitações de área de superfície e problemas de bloqueio do Stretch Database](sql-server-stretch-database-limitations.md)

[Habilitar o Banco de Dados de Stretch para um banco de dados](sql-server-stretch-database-enable-database.md)

[Habilitar o Banco de Dados de Stretch para uma tabela](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0518_2016-->