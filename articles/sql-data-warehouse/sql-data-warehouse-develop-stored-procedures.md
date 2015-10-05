<properties
   pageTitle="Procedimentos armazenados no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para implementar procedimentos armazenados no SQL Data Warehouse do Azure para desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Procedimentos armazenados no SQL Data Warehouse 

O SQL Data Warehouse oferece suporte a muitos recursos Transact-SQL encontrados no SQL Server. Mais importante, há recursos específicos de expansão que vamos aproveitar para maximizar o desempenho da sua solução.

No entanto, para manter a escala e o desempenho do SQL Data Warehouse também há alguns recursos e funcionalidades que apresentam diferenças de comportamento e outros que não têm suporte.

Este artigo explica como implementar procedimentos armazenados no SQL Data Warehouse.

## Apresentação dos procedimentos armazenados
Os procedimentos armazenados são uma ótima maneira de encapsular o código SQL, armazenando-o perto de seus dados no data warehouse. O encapsulamento do código em unidades gerenciáveis de procedimentos armazenados ajuda os desenvolvedores a modularizar suas soluções; facilitando a maior reutilização do código. Cada procedimento armazenado também pode aceitar parâmetros para torná-lo ainda mais flexível.

O SQL Data Warehouse fornece uma implementação simplificada e otimizada de procedimentos armazenados. A maior diferença em comparação ao SQL Server é que o procedimento armazenado não é um código pré-compilado. Normalmente, quando se trata de data warehouses, nos preocupamos menos com o tempo de compilação. É mais importante que o código do procedimento armazenado seja otimizado corretamente ao operar com grandes volumes de dados. O objetivo é economizar horas, minutos e segundos, não milissegundos. Portanto, é mais útil pensar em procedimentos armazenados como contêineres para a lógica SQL.
 
Quando o SQL Data Warehouse executa o procedimento armazenado, as instruções SQL são analisadas, traduzidas e otimizadas no tempo de execução. Durante esse processo, cada instrução é convertida em consultas distribuídas. O código SQL realmente executado nos dados é diferente da consulta enviada.

## Aninhamento de procedimentos armazenados
Quando os procedimentos armazenados chamam outros procedimentos armazenados ou executam sql dinâmico, a invocação interna de código ou procedimento armazenado é considerada aninhada.

O SQL Data Warehouse oferece suporte a um máximo de 8 níveis de aninhamento. Isso é um pouco diferente do SQL Server. O nível de aninhamento no SQL Server é de 32.

A chamada de procedimento armazenado de nível superior é igual ao nível 1 de aninhamento

```
EXEC prc_nesting
``` 
Se o procedimento armazenado também fizer outra chamada EXEC, isso aumentará o nível de aninhamento para 2 ```
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
``` Se o segundo procedimento executar algum sql dinâmico, isso aumentará o nível de aninhamento para 3 ```
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Observe o que SQL Data Warehouse não oferece suporte no momento para @@NESTLEVEL. Você precisará manter o controle de seu nível de aninhamento por conta própria. É improvável que você atinja o limite 8 de nível de aninhamento, mas, se atingir, será necessário trabalhar novamente seu código e "nivelá-lo" para que fique dentro do limite.

## INSERT..EXECUTE
O SQL Data Warehouse não permite que você consuma o conjunto de resultados de um procedimento armazenado com uma instrução INSERT. No entanto, há uma abordagem alternativa.

Confira o seguinte artigo sobre [tabelas temporárias] para obter um exemplo de como fazer isso.

## Limitações

Há alguns aspectos de procedimentos armazenados Transact-SQL que não são implementados no SQL Data Warehouse.

Eles são:

- procedimentos armazenados temporariamente
- procedimentos armazenados numerados
- procedimentos armazenados estendidos
- procedimentos armazenados de CLR
- opção de criptografia
- opção de replicação
- parâmetros com valor de tabela
- parâmetros somente leitura
- parâmetros padrão
- contextos de execução
- instrução return

## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[tabelas temporárias]: sql-data-warehouse-develop-temporary-tables.md
[Visão geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/PT-BR/library/ms187371.aspx

<!--Other Web references-->

<!---HONumber=Sept15_HO4-->