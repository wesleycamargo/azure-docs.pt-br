<properties
	pageTitle="Limites de Recurso de Banco de Dados SQL do Azure"
	description="Esta página descreve alguns limites de recurso comuns para o Banco de Dados SQL do Azure."
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="08/28/2015"
	ms.author="jroth"/>


# Limites de recursos do Banco de Dados SQL do Azure

## Visão geral

O Banco de Dados SQL do Azure gerencia os recursos disponíveis para um banco de dados usando dois mecanismos diferentes: **Governança de recursos** e **Imposição de limites**. Este tópico explica essas duas áreas principais do gerenciamento de recursos.

## Governança de recursos
Uma das metas de design das camadas de serviço Básica, Standard e Premium é que o Banco de Dados SQL do Azure se comporte como se o banco de dados estivesse sendo executado em seu próprio computador, completamente isolado de outros bancos de dados. A governança de recursos emula esse comportamento. Se a utilização de recursos agregada atingir o máximo de recursos da CPU, Memória, E/S de log e de E/S de dados disponíveis atribuídos ao banco de dados, a governança de recursos colocará na fila as consultas em execução e atribuirá recursos às consultas em fila conforme eles sejam liberados.

Assim como em um computador dedicado, utilizar todos os recursos disponíveis resultará em uma execução mais longa das consultas atualmente em execução, o que poderá resultar em tempos limite de comando no cliente. Aplicativos com uma lógica de repetição agressiva e aplicativos que executam consultas no banco de dados com uma alta frequência podem encontrar mensagens de erro ao tentar executar novas consultas quando o limite de solicitações simultâneas for atingido.

### Recomendações:
Monitore a utilização de recursos, bem como os tempos de resposta médios das consultas quando se aproximar da utilização máxima de um banco de dados. Quando encontrar latências maiores de consulta, você geralmente terá três opções:

1.	Reduzir a quantidade de solicitações de entrada para o banco de dados para prevenir o tempo limite e o acúmulo de solicitações.

2.	Atribuir um nível mais alto de desempenho ao banco de dados.

3.	Otimizar consultas para reduzir a utilização de recursos de cada consulta. Para obter mais informações, veja a seção Ajuste/dicas de consulta no artigo Diretrizes de desempenho do Banco de Dados SQL do Azure.

## Imposição de limites
Os recursos, além da CPU, Memória, E/S de log e E/S de dados, são impostos pela negação de novas solicitações quando os limites são atingidos. Os clientes receberão uma [mensagem de erro](sql-database-develop-error-messages.md), dependendo do limite que foi atingido.

Por exemplo, o número de conexões a um Banco de Dados SQL, bem como o número de solicitações simultâneas que podem ser processadas, é restrito. O Banco de Dados SQL permite que o número de conexões ao banco de dados seja maior que o número de solicitações simultâneas para dar suporte ao pooling de conexões. Embora a quantidade de conexões disponíveis possam ser facilmente controladas pelo aplicativo, a quantidade de solicitações paralelas costuma ser mais difícil de estimar e controlar. Especialmente durante os picos de carga quando o aplicativo envia um número excessivo de solicitações ou quando o banco de dados atinge seus limites de recursos e começa a acumular threads de trabalho devido a consultas com execução mais longa, é possível encontrar erros.

## Camadas de serviço e níveis de desempenho

Os limites reais de um banco de dados são definidos pelo nível de desempenho do banco de dados. Para obter uma explicação detalhada, veja [Camadas de serviço e níveis de desempenho do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn741336.aspx).

[AZURE.INCLUDE [Tabela de camadas de serviço do Banco de Dados SQL](../../includes/sql-database-service-tiers-table.md)]

## Cota de provisionamento do banco de dados

Atualmente, o Banco de Dados SQL do Azure tem uma cota de DTU por servidor lógico de 2.000 DTUs. Essa cota representa as DTUs que podem ser hospedadas por um servidor lógico, com base na soma das DTUs se o nível de desempenho de cada banco de dados no servidor. Por exemplo, um servidor com cinco bancos de dados Básicos (máximo de 5 x 5 DTUs), dois bancos de dados Standard S1 (máximo de 2 X 20 DTUs) e três bancos de dados Premium P1 (máximo de 3 X 100 DTUs) consumiu 365 DTUs de sua cota de 2.000 DTUs.

>[AZURE.NOTE]Você pode solicitar um aumento dessa cota [entrando em contato com o suporte](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

## Recursos

[Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md)

[Faixas de Serviço de Banco de Dados SQL do Azure e Níveis de Desempenho](https://msdn.microsoft.com/library/azure/dn741336.aspx)

[Mensagens de erro para programas cliente do Banco de Dados SQL](sql-database-develop-error-messages.md)

<!---HONumber=September15_HO1-->