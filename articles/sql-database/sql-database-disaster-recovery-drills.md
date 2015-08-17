<properties 
   pageTitle="Análises de recuperação de desastres do Banco de Dados SQL" 
   description="Obtenha orientação e as práticas recomendadas para usar o Banco de Dados SQL do Azure para executar os análises de recuperação de desastres que ajudarão a manter seus aplicativos comerciais importantes resilientes a falhas e interrupções." 
   services="sql-database" 
   documentationCenter="" 
   authors="mihaelablendea" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/15/2015"
   ms.author="mihaelab"/>

#Executar análise de recuperação de desastres

Recomenda-se a validação periódica da preparação do aplicativo para o fluxo de trabalho de recuperação. Consideramos uma boa prática de engenharia a verificação do comportamento do aplicativo e as implicações de perda de dados e/ou interrupção que envolvem o failover. Isso também é uma exigência da maioria dos padrões do setor, como parte da certificação de continuidade dos negócios.

A execução de uma análise de recuperação de desastres é composta por:

- Simulação da interrupção da camada de dados
- Recuperando 
- Validação da integridade do aplicativo após a recuperação

Dependendo de como você [projetou a continuidade dos negócios](sql-database-business-continuity.md), o fluxo de trabalho de execução da análise pode variar. A seguir, descrevemos as práticas recomendadas para condução de uma análise de recuperação de desastres no contexto do Banco de Dados SQL do Azure.

##Restauração geográfica

Para evitar a possível perda de dados durante a realização de uma análise de recuperação de desastres, recomendamos a execução da análise usando um ambiente de teste criando uma cópia do ambiente de produção e usando-a para verificar o fluxo de trabalho de failover do aplicativo.
 
####Simulação de interrupção

- Simule a interrupção excluindo ou renomeando o banco de dados de origem e causando uma falha de conectividade do aplicativo. Dessa forma, você pode validar a detecção/alerta da interrupção e medir o RTO da duração da recuperação.

####Recuperação

- Execute a Restauração geográfica do banco de dados em um servidor diferente, conforme descrito [aqui](sql-database-disaster-recovery.md). 
- Altere a configuração de aplicativo a fim de se conectar aos bancos de dados recuperados e siga o guia [Finalizar um banco de dados recuperado](sql-database-recovered-finalize.md) para concluir a recuperação.

####Validação

- Conclua a análise verificando a integridade do aplicativo após a recuperação (isto é, cadeias de conexão, logons, teste de funcionalidade básica ou outras validações que fazem parte dos procedimentos de aprovações padrão do aplicativo).

##Replicação geográfica padrão

Um banco de dados protegido usando a Replicação geográfica padrão pode ter apenas um banco de dados secundário não legível. O exercício de análise envolverá o encerramento forçado do link, ponto no qual o banco de dados ficará desprotegido. Além disso, há a possibilidade de perda de dados, portanto, não recomendamos aos clientes a execução desse teste em bancos de dados de produção. Em vez disso, recomendamos a criação de uma cópia do ambiente de produção e usá-la para verificar o fluxo de trabalho de failover do aplicativo.

####Simulação de interrupção

- Simule a carga de trabalho no banco de dados primário. Se o primário estiver ativo no momento do encerramento, talvez ocorra a perda de dados, o que tornará a análise mais realista.
- Exclua o banco de dados primário ou [realize o encerramento forçado](sql-database-disaster-recovery.md) do link no lado do banco de dados secundário.

####Recuperação

- Altere a configuração do aplicativo para se conectar ao secundário e, anteriormente, somente leitura, que ficará totalmente acessível, e o aplicativo poderá ser usado como o novo primário. 
- Siga o guia [Finalizar um banco de dados recuperado](sql-database-recovered-finalize.md) para concluir a recuperação.

####Validação

- Conclua a análise verificando a integridade do aplicativo após a recuperação (isto é, cadeias de conexão, logons, teste de funcionalidade básica ou outras validações que fazem parte dos procedimentos de aprovações padrão do aplicativo).

##Replicação geográfica ativa

A análise de recuperação de desastres será realizada usando um servidor de destino paralelo e criando outro conjunto contendo secundários somente leitura. Uma versão de teste da camada do aplicativo deve ser usada para verificar a integridade dos dados e a integridade da operação executando testes nesse servidor após o encerramento forçado.

####Simulação de interrupção

- [Crie um novo link de replicação geográfica ativo](sql-database-business-continuity-design.md) do banco de dados primário com um servidor de teste secundário. Se o primário estiver ativo no momento do encerramento, talvez ocorra a perda de dados, tornando a análise mais realista.
- [Execute o encerramento forçado](sql-database-disaster-recovery.md) do link no banco de dados secundário que reside no servidor de teste.

####Recuperação

- Altere a configuração do aplicativo para conectar-se ao secundário e, anteriormente, somente de leitura, que ficará disponível para gravações após o encerramento.
- Siga o guia [Finalizar um banco de dados recuperado](sql-database-recovered-finalize.md) para concluir a recuperação.

####Validação

- Conclua a análise verificando a integridade do aplicativo após a recuperação (isto é, cadeias de conexão, logons, teste de funcionalidade básica ou outras validações que fazem parte dos procedimentos de aprovações padrão do aplicativo).

<!---HONumber=August15_HO6-->