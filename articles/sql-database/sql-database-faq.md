<properties 
   pageTitle="Perguntas frequentes sobre o Banco de Dados SQL do Azure" 
   description="Respostas a perguntas comuns feitas por clientes sobre bancos de dados de nuvem e o Banco de Dados SQL do Azure, o sistema de gerenciamento de bancos de dados relacionais da Microsoft (RDBMS) e o banco de dados como um serviço na nuvem." 
   services="sql-database" 
   documentationCenter="" 
   authors="jeffgoll" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="02/25/2016"
   ms.author="sashan"/>

# Perguntas frequentes sobre o Banco de Dados SQL

## Como o uso do Banco de Dados SQL aparece na minha fatura? 
O Banco de Dados SQL é cobrado de acordo com uma taxa por hora previsível baseada na camada de serviço mais o nível de desempenho para bancos de dados únicos ou em eDTUs por pool de bancos de dados elásticos. O uso real é calculado e rateado por hora, de modo que sua fatura poderá mostrar frações de hora. Por exemplo, se um banco de dados existir por 12 horas em um mês, sua fatura mostrará a utilização de 0,5 dia. Além disso, as camadas de serviço mais o nível de desempenho e os eDTUs por pool são divididos na fatura para facilitar a leitura do número de dias de banco de dados você usou para cada em um único mês.

## E se um banco de dados único ficar ativo por menos de uma hora ou usar uma camada de serviço mais alta por menos de uma hora?
Você será cobrado por cada hora em que um banco de dados existir usando a camada de serviço mais alta mais o nível de desempenho aplicado durante aquela hora, independentemente do uso ou se o banco de dados ficou ativo por menos de uma hora. Por exemplo, se você criar um banco de dados único e o excluir depois de 5 minutos, sua fatura apresentará uma cobrança referente a 1 hora de banco de dados.

Exemplos
	
- Se você criar um banco de dados Básico e atualizá-lo imediatamente para Standard S1, será cobrado pela tarifa Standard S1 na primeira hora.

- Por exemplo, se você atualizar um banco de dados de Básico para Premium às 22h e a atualização for concluída à 1h35 do dia seguinte, a tarifa Premium somente será cobrada a partir da 1h.

- Se você fizer o downgrade de um banco de dados Premium para Básico às 11:00 e se ele for concluído às 14:15h, o banco de dados será cobrado na taxa Premium até 15:00 e depois serão cobradas as taxas Básico.

## Como o uso do pool de bancos de dados elásticos é mostrado em minha fatura e o que acontecerá se eu alterar eDTUs por pool?
As cobranças do pool de bancos de dados elásticos são mostradas em sua fatura como DTUs Elásticas (eDTUs) nos incrementos mostrados sob eDTUs por pool na [página de preços](https://azure.microsoft.com/pricing/details/sql-database/). Não há cobrança por banco de dados para pools de bancos de dados elásticos. Você será cobrado por hora de existência de um pool no eDTU mais alto, independentemente do uso ou se o pool estive ativo por menos de uma hora.

Exemplos

- Se você criar um pool de bancos de dados elásticos Standard com 200 eDTUs às 11:18h, adicionando cinco banco de dados ao pool, será cobrado por 200 eDTUs pela hora cheia, começando às 11:00h até o restante do dia.
- No Dia 2, às 5:05h, o Banco de Dados 1 começa a consumir 50 eDTUs e se mantém estável ao longo do dia. Os Bancos de Dados 2 a 5 flutuam entre 0 e 80 eDTUs. Durante o dia, você adiciona cinco outros bancos de dados que consomem eDTUs variados durante o dia. O Dia 2 é um dia inteiro cobrado a 200 eDTUs. 
- No Dia 3, às 5:00h, você adiciona mais 15 bancos de dados. O uso do banco de dados aumenta durante o tia até o ponto em que você decide aumentar o número de eDTUs para o pool de 200 para 400, às 20:05h. As cobranças no nível de 200 eDTUs estavam em vigor até às 20:00h e aumentam para 400 eDTUs pelas quatro horas restantes. 

## Como o uso da Replicação Geográfica em um agrupamento de banco de dados elástico é mostrado na minha fatura?
Ao contrário dos bancos de dados únicos, o uso da GEO-DR com os bancos de dados elásticos não tem um impacto direto no faturamento. Você só será cobrado pelos eDTUs provisionados para cada um dos pools (o pool primário e o pool secundário)

## Como o uso do recurso de auditoria afeta minha fatura? 
A auditoria é interna ao serviço Banco de Dados SQL sem custo extra e está disponível para os bancos de dados Básico, Standard e Premium. No entanto, para armazenar os logs de auditoria, o recurso de auditoria usa uma conta do Armazenamento do Azure e as taxas para tabelas e consultas no Armazenamento do Azure são aplicadas com base no tamanho do seu log de auditoria.

## Como posso descobrir a camada de serviço e o nível de desempenho corretos para bancos de dados únicos e pools de bancos de dados elásticos? 
Existem algumas ferramentas disponíveis para você.

- Para os bancos de dados locais, use o [supervisor de dimensionamento de DTU](http://dtucalculator.azurewebsites.net/), que recomendará os bancos de dados e os DTUs necessários, além de avaliar vários bancos de dados para os pools de banco de dados elásticos.
- Se um banco de dados puder se beneficiar de estar em um pool, o mecanismo inteligente do Azure recomendará um pool de banco de dados elásticos se perceber um padrão de uso histórico que garanta isso. Veja [Monitorar, gerenciar e dimensionar um pool de banco de dados elástico com o Portal do Azure](sql-database-elastic-pool-manage-portal.md). Para obter detalhes sobre como fazer os cálculos você mesmo, consulte [Considerações de preço e desempenho para um pool de banco de dados elástico](sql-database-elastic-pool-guidance.md).
- Para ver se você precisa aumentar ou reduzir um banco de dados único, consulte [diretrizes sobre desempenho para bancos de dados únicos](sql-database-performance-guidance.md).

## Com que frequência posso alterar a camada de serviço ou o nível de desempenho de um banco de dados único? 
Com bancos de dados da versão 12, você pode alterar a camada de serviço (entre Básico, Standard e Premium) ou o nível de desempenho dentro de uma camada de serviço (por exemplo, S1 para S2) sempre que desejar. Para bancos de dados de versão anteriores, você pode alterar o nível de desempenho ou de camada de serviço no máximo quatro vezes em um período de 24 horas.

##Com que frequência posso ajustar eDTUs por pool? 
Quantas vezes desejar.

## Quanto tempo leva para alterar a camada de serviço ou o nível de desempenho de um banco de dados único ou incluir ou retirar um banco de dados de um pool de bancos de dados elásticos? 
A alteração da camada de serviço de um banco de dados e a inclusão e a retirada de um pool exige que o banco de dados seja copiado para a plataforma como uma operação em segundo plano. Isso pode levar alguns minutos a várias horas, dependendo do tamanho dos bancos de dados. Em ambos os casos, os bancos de dados permanecem online e disponíveis durante a movimentação. Para obter detalhes sobre como alterar os bancos de dados únicos, consulte [Alterar a camada de serviço de um banco de dados](sql-database-scale-up.md). Para bancos de dados elásticos, consulte [Referência do pool elástico](sql-database-elastic-pool-reference.md#latency-of-elastic-pool-operations)

##Quando eu devo usar um banco de dados únicos em vez de bancos de dados elásticos? 
Em geral, os pools de bancos de dados elásticos são projetados para típico padrão de aplicativo de software como serviço (SaaS), onde há um banco de dados por cliente ou locatário. Comprar bancos de dados individuais e provisionar em excesso para atender as demandas variáveis e de pico de cada banco de dados frequentemente não é uma opção com bom custo-benefício. Com os pools, você gerencia o desempenho coletivo do pool e os bancos de dados são escalados e reduzidos verticalmente.

O mecanismo inteligente do Azure recomendará um pool para bancos de dados se perceber um padrão de uso que o garanta. Para obter detalhes, veja [Recomendações sobre tipo de preço do Banco de Dados SQL](sql-database-service-tier-advisor.md). Para obter orientações detalhadas sobre como escolher entre bancos de dados únicos e elásticos, consulte [Considerações sobre preço e desempenho para pools de bancos de dados elásticos](sql-database-elastic-pool-guidance.md).

## O que significa ter até 200% de seu armazenamento máximo de banco de dados provisionado para o armazenamento de backup? 
O armazenamento para backup é aquele associado aos seus backups automatizados de banco de dados utilizados para Restauração Pontual e Restauração Geográfica. O Banco de Dados SQL do Microsoft Azure fornece até 200% de seu armazenamento máximo de banco de dados provisionado em armazenamento de backup, sem custo adicional. Por exemplo, se você tiver uma instância de banco de dados Standard com tamanho provisionado de 250 GB, serão oferecidos a você 500 GB de espaço de armazenamento para backup sem custo adicional. Se seu banco de dados exceder o armazenamento de backup fornecido, é possível optar por reduzir o período de retenção ao entrar em contato com o Suporte do Azure ou pagar pelo armazenamento de backup adicional com a taxa padrão do Armazenamento com Redundância Geográfica com Acesso de Leitura (RA-GRS). Para saber mais sobre a cobrança de RA-GRS, consulte Detalhes de preços de armazenamento.

## Estou mudando de Web/Negócios para novas camadas de serviço, o que eu preciso saber?
Os bancos de dados SQL Business e Web do Azure foram desativados. As camadas Básico, Standard, Premium e Elástica substituem os bancos de dados Web e Business obsoletos. Temos perguntas frequentes adicionais que deverão ajudar você neste período de transição. [Últimas perguntas frequentes de Web e Business Edition ](sql-database-web-business-sunset-faq.md)

## Qual é o retardo de replicação esperado durante a replicação geográfica de um banco de dados entre duas regiões na mesma região geográfica do Azure?  
Atualmente, damos suporte a um RPO de 5 segundos e o retardo de replicação tem sido menor que isso, desde que o secundário geográfico esteja hospedado na região emparelhada recomendada pelo Azure e esteja na mesma camada de serviço.

## Qual é o retardo de replicação esperado quando o secundário geográfico é criado na mesma região do banco de dados primário?  
Com base em dados empíricos, não há muita diferença entre o retardo de replicação intra e entre regiões quando a região emparelhada recomendada pelo Azure é usada.

## Se houver uma falha de rede entre duas regiões, como a lógica de repetição funciona quando a replicação geográfica é configurada?  
Se houver uma desconexão, haverá uma repetição a cada 10 segundos para restabelecer as conexões.

## O que posso fazer para garantir que uma alteração crítica no banco de dados primário seja replicada?
O secundário geográfico é uma réplica assíncrona e não tentamos mantê-la em sincronização completa com o primário. No entanto, fornecemos um método para forçar a sincronização. Ele foi projetado para garantir a replicação de alterações críticas (por exemplo, atualizações de senha). Isso afetará o desempenho, já que bloqueará o thread de chamada até que todas as transações confirmadas sejam replicadas. Para obter detalhes, veja [sp\_wait\_for\_database\_copy\_sync](https://msdn.microsoft.com/library/dn467644.aspx).

## Quais ferramentas estão disponíveis para monitorar o retardo de replicação entre o banco de dados primário e o secundário geográfico?
Expomos o retardo de replicação em tempo real entre o banco de dados primário e o secundário geográfico por meio de um DMV. Para obter detalhes, veja [sys.dm\_geo\_replication\_link\_status](https://msdn.microsoft.com/library/mt575504.aspx).

<!-----------HONumber=AcomDC_0330_2016-->