<properties 
   pageTitle="Análises de recuperação de desastres do Banco de Dados SQL" 
   description="Obtenha orientação e as práticas recomendadas para usar o Banco de Dados SQL do Azure para executar os análises de recuperação de desastres que ajudarão a manter seus aplicativos comerciais importantes resilientes a falhas e interrupções." 
   services="sql-database" 
   documentationCenter="" 
   authors="mihaelablendea" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/18/2016"
   ms.author="mihaelab"/>

#Executar análise de recuperação de desastres

Recomenda-se a validação periódica da preparação do aplicativo para o fluxo de trabalho de recuperação. Consideramos uma boa prática de engenharia a verificação do comportamento do aplicativo e as implicações de perda de dados e/ou interrupção que envolvem o failover. Isso também é uma exigência da maioria dos padrões do setor, como parte da certificação de continuidade dos negócios.

A execução de uma análise de recuperação de desastres é composta por:

- Simulação da interrupção da camada de dados
- Recuperando 
- Validação da integridade do aplicativo após a recuperação

Dependendo de como você [criou seu aplicativo para continuidade de negócios](sql-database-business-continuity.md), o fluxo de trabalho para executar a análise pode variar. A seguir, descrevemos as práticas recomendadas para condução de uma análise de recuperação de desastres no contexto do Banco de Dados SQL do Azure.

##Restauração geográfica

Para evitar a possível perda de dados durante a realização de uma análise de recuperação de desastres, recomendamos a execução da análise usando um ambiente de teste criando uma cópia do ambiente de produção e usando-a para verificar o fluxo de trabalho de failover do aplicativo.
 
####Simulação de interrupção

Para simular a interrupção, você pode excluir ou renomear o banco de dados de origem. Isso causará falha de conectividade do aplicativo.

####Recuperação

- Execute a Restauração geográfica do banco de dados em um servidor diferente, conforme descrito [aqui](sql-database-disaster-recovery.md). 
- Altere a configuração de aplicativo para se conectar aos bancos de dados recuperados e siga o guia [Configurar um banco de dados após a recuperação](sql-database-disaster-recovery.md) para concluir a recuperação.

####Validação

- Conclua a análise verificando a integridade do aplicativo após a recuperação (isto é, cadeias de conexão, logons, teste de funcionalidade básica ou outras validações que fazem parte dos procedimentos de aprovações padrão do aplicativo).

##Replicação Geográfica

Para um banco de dados protegido usando a replicação geográfica, o exercício de análise envolverá failover planejado para o banco de dados secundário. O failover planejado garante que os bancos de dados primário e secundário permaneçam em sincronia quando as funções são alternadas. Ao contrário de failover não planejado, essa operação não resultará na perda de dados, assim a análise pode ser executada no ambiente de produção.

####Simulação de interrupção

Para simular a interrupção, você pode desabilitar o aplicativo Web ou a máquina virtual conectada ao banco de dados. Isso resultará em falhas de conectividade para os clientes da web.

####Recuperação

- Verifique se a configuração do aplicativo na região de DR aponta para o secundário anterior que se tornará o novo primário totalmente acessível. 
- Execute [failover planejado](sql-database-geo-replication-powershell.md#initiate-a-planned-failover) para tornar o banco de dados secundário um novo primário
- Siga o guia [Configurar um banco de dados após a recuperação](sql-database-disaster-recovery.md) para concluir a recuperação.

####Validação

- Conclua a análise verificando a integridade do aplicativo após a recuperação (isto é, cadeias de conexão, logons, teste de funcionalidade básica ou outras validações que fazem parte dos procedimentos de aprovações padrão do aplicativo).

<!---HONumber=AcomDC_0420_2016-->