<properties 
   pageTitle="Perguntas frequentes sobre continuidade dos negócios do Banco de Dados SQL" 
   description="Perguntas frequentes que os clientes fazem e as respectivas respostas sobre recursos internos e opcionais para continuidade dos negócios e recuperação de desastres com o Banco de Dados SQL do Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/14/2015"
   ms.author="elfish"/>

# Perguntas frequentes sobre continuidade dos negócios

## 1. O que acontece com o período de retenção do meu ponto de restauração quando faço downgrade/atualização pela camada de serviço?
Após o downgrade para um nível de desempenho inferior, o período de retenção do ponto de restauração é imediatamente truncado para o período de retenção do nível desempenho do banco de dados atual.

Se a camada de serviço do banco de dados for atualizada, o período de retenção começará a ser estendido somente depois que o banco de dados for atualizado.

Por exemplo, se for feito downgrade do banco de dados, de P1 para S3, o período de retenção mudará de 35 dias para 14 dias imediatamente; todos os pontos de restauração antes de 14 dias não estarão mais disponíveis. Subsequentemente, se ele for atualizado para P1 novamente, o período de retenção começará a partir de 14 dias e começara a ser construído até 35 dias.

## 2. De quanto tempo é o período de retenção para um banco de dados descartado? 
O período de retenção é determinado pela camada de serviço do banco de dados enquanto ele existiu ou pelo número de dias que o banco de existe, o que for menor.

## 3. Como posso restaurar um servidor excluído?

No momento, não há suporte para restaurar um servidor excluído.

## 4. Quanto tempo leva para restaurar um banco de dados?

O tempo que se leva para restaurar um banco de dados depende de vários fatores, como tamanho do banco de dados, número do log de transações, largura de banda da rede, etc. A maioria das restaurações de banco de dados dura 12 horas.

## 5. Posso alterar o período de retenção do ponto de restauração do meu banco de dados?

Nº

## 6. Como descubro o ponto de restauração disponível para meu banco de dados?

Para recuperação de um erro do usuário — a hora atual é o ponto de restauração mais recente disponível. Para localizar e obter o ponto de restauração mais antigo disponível, use [Get Database](https://msdn.microsoft.com/library/dn505708.aspx) (*RecoveryPeriodStartDate*) (ponto de restauração não replicado geograficamente).

Para recuperação de uma interrupção — use [Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) para obter o ponto de restauração mais recente replicado geograficamente.

## 7. Como posso restaurar bancos de dados em massa em meu servidor?

Não há nenhuma funcionalidade interna para restauração em massa. Você pode usar o script [Banco de Dados SQL do Azure: recuperação completa do servidor](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) para realizar essa tarefa.

## 8. Qual é a diferença entre replicação geográfica padrão e replicação geográfica ativa?

Para replicação geográfica padrão, o banco de dados secundário não é legível. Ele está disponível apenas para failover durante interrupções.

Para replicação geográfica ativa, todos os bancos de dados secundários são legíveis (até 4 secundários).

## 9. Qual é o atraso da replicação ao usar a replicação geográfica padrão ou replicação geográfica ativa?

A replicação geográfica usa cópia contínua. Desse modo, use os DMVs (exibição de gerenciamento dinâmico) [sys.dm_continuous_copy_status](https://msdn.microsoft.com/library/azure/dn741329.aspx) para obter a hora da última replicação e outras informações.




 

<!---HONumber=July15_HO4-->