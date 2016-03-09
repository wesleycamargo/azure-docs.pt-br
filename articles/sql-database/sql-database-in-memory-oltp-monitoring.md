<properties
	pageTitle="Monitorar o armazenamento na memória XTP | Microsoft Azure"
	description="Estimar e monitorar o uso do armazenamento na memória XTP, capacidade; resolver o erro de capacidade 41805"
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="jodebrui"/>


# Monitorar o armazenamento OLTP na memória

Ao usar o [In-Memory OLTP](sql-database-in-memory.md), os dados em tabelas com otimização de memória e as variáveis de tabela residem no armazenamento OLTP na memória. Cada camada de serviço Premium tem um tamanho máximo de armazenamento na memória, que está documentado no [artigo Camadas de serviço do banco de Dados SQL](sql-database-service-tiers.md#service-tiers-for-single-databases). Quando esse limite for excedido, as operações insert e update poderão começar a falhar (com o erro 41805). Nesse ponto, você precisará excluir dados para obter memória ou atualizar a camada de desempenho do seu banco de dados.

## Determinar se os dados se ajustarão ao limite de armazenamento na memória

Determine o limite de armazenamento: consulte o [artigo Camadas de serviço do Banco de dados SQL](sql-database-service-tiers.md#service-tiers-for-single-databases) para obter os limites de armazenamento das diferentes camadas do serviço Premium.

A estimativa dos requisitos de memória para uma tabela com otimização de memória funciona no SQL Server da mesma forma como no Banco de Dados SQL do Azure. Reserve alguns minutos para examinar este tópico no [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Observe que a tabela e as linhas de variável de tabela, bem como índices, contam para o tamanho máximo dos dados do usuário. Além disso, ALTER TABLE precisa de espaço suficiente para criar uma nova versão da tabela inteira e de seus índices.

## Monitoramento e alertas

Você pode monitorar o uso de armazenamento na memória como uma porcentagem do [limite de armazenamento para sua camada de desempenho](sql-database-service-tiers.md#service-tiers-for-single-databases) no [portal](https://portal.azure.com/) do Azure:

- Na folha Banco de Dados, localize a caixa de utilização Recurso e clique em Editar.
- Em seguida, selecione a métrica percentual de Armazenamento OLTP na Memória.
- Para adicionar um alerta, clique na caixa Utilização de Recursos para abrir a folha Métrica e clique em Adicionar alerta.

Ou use a consulta a seguir para mostrar a utilização de armazenamento na memória:

    select xtp_storage_percent from sys.dm_db_resource_stats


## Corrigir situações de memória insuficiente - Erro 41805

A insuficiência de memória resulta na falha das operações INSERT, UPDATE e CREATE com a mensagem de erro 41805.

A mensagem de erro 41805 indica que as variáveis de tabela e as tabelas com otimização de memória excederam o tamanho máximo.

Para resolver esse erro:


- Exclua os dados das tabelas com otimização de memória, potencialmente descarregando os dados em tabelas baseadas em disco tradicionais ou
- Atualize a camada de serviço para uma com armazenamento na memória suficiente para os dados que você precisa manter em tabelas com otimização de memória.

## Próximas etapas
Saiba mais sobre [Monitoramento de Banco de Dados SQL usando exibições de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md)

<!---HONumber=AcomDC_0224_2016-->