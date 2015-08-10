<properties
   pageTitle="Camadas de serviço do Banco de Dados SQL"
   description="Compare os recursos de continuidade dos negócios e desempenho das camadas de serviço do Banco de Dados SQL do Azure para encontrar o equilíbrio certo entre custo e capacidade à medida que as escala horizontalmente sob demanda e sem tempo de inatividade."
   services="sql-database"
   documentationCenter=""
   authors="shontnew"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="07/24/2015"
   ms.author="shkurhek"/>

# Camadas de serviço

O [Banco de Dados SQL](sql-database-technical-overview.md) está disponível nas camadas Basic, Standard e Premium. As três camadas de serviço têm um [SLA](http://azure.microsoft.com/support/legal/sla/) de tempo de atividade de 99,99% e oferecem desempenho previsível, opções de continuidade dos negócios flexíveis, recursos de segurança e cobrança por hora. Com vários níveis de desempenho em cada camada de serviço, você tem a flexibilidade de escolher o nível que melhor atende às suas demandas de carga de trabalho. Se precisar expandir ou reduzir, você poderá alterar as camadas do banco de dados com facilidade no Portal do Azure, sem tempo de inatividade dos aplicativos. Consulte [Alterando camadas de serviços e níveis de desempenho do banco de dados](https://msdn.microsoft.com/library/azure/dn369872.aspx) para obter detalhes.

> [AZURE.IMPORTANT]As edições Web e Business estão sendo desativadas. Descubra como [atualizar as edições Web e Business](sql-database-upgrade-new-service-tiers.md). Leia [Perguntas frequentes sobre a descontinuação](http://azure.microsoft.com/pricing/details/sql-database/web-business/) se planeja continuar usando as edições Web e Business.

## Basic

A camada Basic foi desenvolvida para aplicativos com pouca carga de trabalho transacional. Um caso de uso típico é um aplicativo simples que precisa de um banco de dados pequeno com uma única operação em um determinado momento.

**Desempenho, tamanho e recursos**


| Camada de serviço | Basic |
| :-- | :-- |
| DTUs (Unidades de Taxa de Transferência de Banco de Dados) | 5 |
| Tamanho máximo do banco de dados | 2 GB |
| PITR (Restauração Pontual) | Até milissegundos nos últimos 7 dias |
| Recuperação de desastre | Restauração geográfica, restauração em qualquer região do Azure |
| Objetivos de desempenho | Taxa de transações por hora |


## Padrão

A camada Standard é a opção ideal para começar a lidar com cargas de trabalho transacionais. Ela oferece melhor desempenho e melhores recursos internos de continuidade dos negócios em comparação com a camada Basic. Um caso de uso típico é um aplicativo com várias transações simultâneas.

**Desempenho e tamanho**


| Camada de serviço | Padrão S0 | Padrão S1 | Padrão S2 | Padrão S3 |
| :-- | :-- | :-- | :-- | :-- |
| DTUs (Unidades de Taxa de Transferência de Banco de Dados) | 10 | 20 | 50 | 100 |
| Tamanho máximo do banco de dados | 250 GB | 250 GB | 250 GB | 250 GB |


**Recursos**


| Camada de serviço | Padrão (S0, S1, S2, S3) |
| :-- | :-- |
| PITR (Restauração Pontual) | Até milissegundos nos últimos 14 dias |
| Recuperação de desastre | Replicação geográfica padrão, 1 secundária offline |
| Objetivos de desempenho | Taxa de transações por minuto |


## Premium

A camada Premium foi desenvolvida para aplicativos críticos. Ela oferece o melhor nível de desempenho e acesso aos recursos avançados de continuidade dos negócios, incluindo replicação geográfica ativa em até 4 regiões do Azure de sua escolha. Um caso de uso típico é um aplicativo crítico com alto volume transacional e muitos usuários simultâneos.

**Desempenho e tamanho**


| Camada de serviço | Premium P1 | Premium P2 | Premium P6 (anteriormente, P3) |
| :-- | :-- | :-- | :-- |
| DTUs (Unidades de Taxa de Transferência de Banco de Dados) | 125 | 250 | 1000 |
| Tamanho máximo do banco de dados | 500 GB | 500 GB | 500 GB |


**Recursos**


| Camada de serviço | Premium (P1, P2, P6) |
| :-- | :-- |
| PITR (Restauração Pontual) | Até milissegundos nos últimos 35 dias |
| Recuperação de desastre | Replicação geográfica ativa, até 4 secundárias legíveis online |
| Objetivos de desempenho | Taxa de transações por segundo |


Saiba mais sobre os preços para essas camadas em [Preços de Banco de Dados SQL](http://azure.microsoft.com/pricing/details/sql-database/).

Agora que você conhece as camadas do Banco de Dados SQL, teste-as usando uma versão de [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/) e [aprenda a criar seu primeiro banco de dados SQL](sql-database-get-started.md)!
 

<!---HONumber=July15_HO5-->