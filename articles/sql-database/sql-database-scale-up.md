<properties 
   pageTitle="Alterando as camadas de serviço e os níveis de desempenho do banco de dados" 
   description="Saiba como expandir/reduzir dinamicamente um banco de dados na nuvem usando as camadas de serviço do Banco de Dados SQL do Azure, que podem ser usadas para aumentar ou reduzir o desempenho com base nos requisitos de negócios e custo sem tempo de inatividade do aplicativo." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="04/02/2015"
   ms.author="sstein"/>


# Alterando as camadas de serviço e os níveis de desempenho do banco de dados 

Este tópico descreve as etapas envolvidas na movimentação de um Banco de Dados SQL do Azure entre as camadas de serviço e os níveis de desempenho.

## Alterando camadas de serviço 

Use as informações em [Atualizar banco de dados Web/Business do Banco de Dados SQL para novas camadas de serviço](sql-database-upgrade-new-service-tiers.md) e [Camadas de serviço e níveis de desempenho do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn741336.aspx) para determinar o nível de desempenho e a camada de serviço apropriados para o Banco de Dados SQL do Azure.

Você pode passar de uma camada de serviço para outra com facilidade usando o Portal de Gerenciamento do Azure, o [PowerShell](https://msdn.microsoft.com/library/azure/dn546726.aspx) ou a [API REST](https://msdn.microsoft.com/library/dn505719.aspx).

Durante a movimentação entre as camadas de serviço, lembre-se do seguinte:
- antes da atualização entre camadas de serviço ou níveis de desempenho, verifique se você tem cota disponível no servidor. Se precisar de mais cota, ligue para o atendimento ao cliente.
- bancos de dados federados não podem ser atualizados para camadas de serviço Basic, Standard ou Premium.

- Para fazer downgrade de um banco de dados, este deve ter um tamanho menor do que o máximo permitido para a camada de serviço de destino. Para saber mais sobre o tamanho permitido para cada camada de serviço, confira a tabela de tamanho de banco de dados e camada de serviço, mais adiante nesta seção.

- Ao atualizar um banco de dados com [Replicação geográfica padrão](https://msdn.microsoft.com/library/azure/dn758204.aspx) ou [Replicação geográfica](https://msdn.microsoft.com/library/azure/dn741339.aspx) habilitada, é necessário atualizar primeiro seus bancos de dados secundários para o nível de desempenho desejado antes de atualizar o banco de dados primário.

- Ao fazer downgrade de uma camada de serviço Premium, primeiro, você deve encerrar todos os relacionamentos de Replicação Geográfica. Você pode seguir as etapas descritas no tópico [Finalizar uma relação de cópia contínuo](https://msdn.microsoft.com/library/azure/dn741323.aspx) para interromper o processo de replicação entre os bancos de dados primários e secundários ativos.

- As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se estiver fazendo downgrade, talvez você perca a capacidade de fazer uma restauração pontual ou tenha um período menor de retenção do backup. Para saber mais, confira [Backup e restauração do Banco de dados SQL do Azure](https://msdn.microsoft.com/library/azure/jj650016.aspx).

- Você pode fazer até quatro alterações individuais de banco de dados (camada de serviço ou níveis de desempenho) em um período de 24 horas.

- As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.

Observe o seguinte: 
- as camadas de serviço Web e de negócios serão desativadas em setembro de 2015. Para saber mais, confira [Perguntas frequentes sobre a descontinuação das edições Web e Business](https://msdn.microsoft.com/library/azure/dn741330.aspx).

<note included> 
- é importante observar que a implementação atual das [Federações será descontinuada com as camadas de serviço Web e Business](https://msdn.microsoft.com/library/azure/dn741330.aspx). É recomendável usar [Escala elástica para Banco de Dados SQL do Azure](sql-database-elastic-scale-get-started.md) para criar uma solução de expansão fragmentada no Banco de Dados SQL do Azure. Para tentar isso, confira Introdução à visualização da escala elástica do Banco de Dados SQL do Azure.

## Atualizar para uma camada de serviço superior
Use um dos métodos a seguir para atualizar um banco de dados. As etapas são específicas para atualizar para uma camada de serviço Premium, mas se aplicam a todas as atualizações.

### Usando o Portal de Gerenciamento do Azure
1. Use sua conta da Microsoft para entrar no Portal de Gerenciamento do Azure.
2. Navegue até a guia **BANCOS DE DADOS SQL**.
3. Selecione um banco de dados na lista **Bancos de dados**. Essa ação abre o banco de dados no **Painel de Banco de Dados** ou na página **Início Rápido**.
4. Selecione a guia **Escala** do banco de dados.
5. Na seção **Geral**, selecione **PREMIUM** para a camada de serviço.
6. Para **Nível de Desempenho**, selecione **P1**, **P2** ou **P3**. Os recursos que capacitam cada nível de desempenho são representados em DTUs. Para saber mais sobre níveis de desempenho e DTUs, confira Camadas de serviço e níveis de desempenho do Banco de Dados SQL do Azure.
8. Na barra de comandos na parte inferior da tela, clique no botão **Salvar**.
9. Será exibida uma **Confirmação**. Leia as informações fornecidas e marque a caixa de seleção para confirmar.


### Usando o PowerShell do Azure
1. Use o Set-AzureSqlDatabase para especificar o nível de desempenho, o tamanho máximo do banco de dados e a camada de serviço para o banco de dados. Para obter uma lista de tamanhos de banco de dados com suporte das diferentes camadas de serviço, confira Camadas de serviço do Banco de Dados SQL do Azure (edições).
2. Defina o contexto do servidor com o cmdlet New-AzureSqlDatabaseServerContext. A sintaxe de exemplo é fornecida na seção Usando comandos do PowerShell do Azure.
3. Obtenha um identificador para o banco de dados e o nível de desempenho de destino. Especifique o nível de desempenho usando Set-AzureSqlDatabase –ServiceObjective

**Exemplo de uso** 
Neste exemplo:
- este exemplo demonstra a atualização para uma camada de serviço Premium. 
- O identificador $db é criado e aponta para o nome do banco de dados "somedb". 
- O identificador $P1 é criado e aponta para o nível de desempenho Premium 1. 
- O nível de desempenho do banco de dados $db é definido como $P1.

		Windows PowerShell:

		$db = Get-AzureSqlDatabase $serverContext –DatabaseName "somedb"

		$P1= Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "P1"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $P1 –Edition Premium



## Downgrade para uma camada de serviço inferior
Use um dos seguintes métodos para fazer downgrade de um banco de dados para uma camada de serviço inferior:

### Usando o Portal de Gerenciamento do Azure
1. Use sua conta da Microsoft para entrar no Portal de Gerenciamento do Azure.
2. Navegue até a guia **BANCOS DE DADOS SQL**.
3. Selecione a guia **ESCALA** do banco de dados desejado.
4. Na seção **Geral**, selecione a camada de serviço para a qual deseja fazer downgrade.
5. Na barra de comandos na parte inferior da tela, clique no botão **Salvar**.
6. Se for o caso, na página **Confirmação**, leia as informações fornecidas e marque a caixa de seleção para confirmar a alteração.

### Usando o PowerShell do Azure
1. Use Set-AzureSqlDatabase para especificar a camada de serviço, o nível de desempenho e o tamanho máximo do banco de dados.
2. Defina o contexto do servidor usando New-AzureSqlDatabaseServerContext na sintaxe de exemplo fornecida na seção Usando comandos do PowerShell do Azure.
3. Faça o seguinte:
 - Obtenha um identificador para o banco de dados.
 - Obtenha um identificador para o nível de desempenho.
 - Especifique a camada de serviço, o nível de desempenho e o tamanho máximo para o banco de dados usando Set-AzureSqlDatabase –ServiceObjective.

**Exemplo de uso**

Este exemplo demonstra como fazer downgrade de um banco de dados da camada de serviço Premium para um banco de dados da camada de serviço Standard:

- O identificador $db é criado e aponta para o nome do banco de dados "somedb".

- A variável $S2 é criada e aponta para o nível de desempenho Standard S2.

- O nível de desempenho do banco de dados $db é definido como $S2.

- Especifique a camada de serviço do banco de dados e o tamanho máximo do banco de dados usando os parâmetros –Edition e –MaxSizeGB. O valor especificado para o parâmetro –MaxSizeGB deve ser válido para a camada de serviço de destino. Uma tabela com os valores MaxSize para cada camada de serviço está disponível neste tópico.

		Windows PowerShell:

		$db = Get-AzureSqlDatabase $serverContext –DatabaseName “somedb”

		$S2 = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "S2"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $S2 –Edition Standard –MaxSizeGB 40

##Alterando os níveis de desempenho
Você pode aumentar ou diminuir os níveis de desempenho de um banco de dados Standard ou Premium usando um dos métodos a seguir. A alteração do nível de desempenho do banco de dados pode ser uma ação demorada. Para saber mais, confira a seção [Impacto das alterações no banco de dados Premium](https://msdn.microsoft.com/library/azure/dn369872.aspx#Impact), mais adiante.

Se você estiver alterando o nível de desempenho de um banco de dados Premium que tem relacionamentos de Replicação Geográfica Ativa configurados, use a seguinte ordem para os bancos de dados primário e secundários ativos:

Isso porque os bancos de dados secundários ativos devem ter o nível de desempenho igual ou maior que os primários. 
- Se estiver alterando de um nível de desempenho superior para um inferior, comece com o banco de dados primário seguido por um ou mais bancos de dados secundários ativos.

- Se você estiver alterando de um nível de desempenho inferior para um nível superior, comece com os bancos de dados secundários ativos e, por fim, os primários.

###Usando o Portal de Gerenciamento do Azure
1. Use sua conta da Microsoft para entrar no Portal de Gerenciamento do Azure.
2. Navegue até a guia **BANCOS DE DADOS SQL**.
3. Selecione um banco de dados na lista **Bancos de dados** da conta ou de um servidor específico. Essa ação abre o banco de dados no **Painel de Banco de Dados** ou na página **Início Rápido**.
5. Selecione a guia **Escala** do banco de dados.
6. Para a opção de **nível de desempenho**, selecione um nível de desempenho.
7. Na barra de comandos na parte inferior da tela, clique no botão **Salvar**

###Usando o PowerShell do Azure
1. Use Set-AzureSqlDatabase para especificar o nível de desempenho do banco de dados
2. Defina o contexto do servidor usando o cmdlet New-AzureSqlDatabaseServerContext. A sintaxe de exemplo é fornecida na seção Usando comandos do PowerShell do Azure.
3. Faça o seguinte:
- Obtenha um identificador para o banco de dados.
- Obtenha um identificador para o nível de desempenho.
- Especifique o nível de desempenho usando Set-AzureSqlDatabase –ServiceObjective.

**Exemplo de uso**

Neste exemplo:

- O identificador $db é criado e aponta para o nome do banco de dados "somedb".

- O identificador $P2 é criado e aponta para o nível de desempenho Premium 2.

- O nível de desempenho do banco de dados $db é definido como $P2.

		Windows PowerShell
		$db = Get-AzureSqlDatabase $serverContext –DatabaseName “somedb”

		$P2 = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "P2"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $P2

##Impacto das alterações no banco de dados
Esta seção fornece informações sobre os efeitos de atualizar para uma camada de serviço Standard ou Premium ou de fazer alterações de nível de desempenho no banco de dados.

###Manipulando conexões de aplicativo durante alterações no banco de dados
As conexões com o banco de dados poderão ser temporariamente perdidas quando uma alteração no nível de desempenho ou atualização/downgrade for concluída, e talvez seja preciso esperar alguns segundos para que as conexões possam ser restabelecidas. Os aplicativos do Banco de Dados SQL devem ser codificados para serem resilientes à perda de conexão, uma vez que isso poderá ocorrer a qualquer momento no Banco de Dados SQL quando um computador falhar no data center e o serviço do Banco de Dados SQL fizer failover no banco de dados. O aplicativo não exige nenhuma alteração na implementação para usar um banco de dados Premium ou fazer alterações no nível de desempenho de um banco de dados Premium.

###Noções básicas e estimativa de latência em alterações no banco de dados
Geralmente, uma alteração de SLO para um banco de dados envolve a movimentação de dados e, portanto, talvez sejam necessárias muitas horas para que a solicitação de alteração seja concluída e para que a alteração de cobrança associada entre em vigor. A movimentação de dados ocorre em alterações no upgrade ou downgrade de um banco de dados, mas também pode correr na alteração do nível de desempenho do banco de dados.

**Latência de alterações de SLO que envolvem movimentação de dados**

Depois de determinar o tamanho do armazenamento do banco de dados, a latência de uma solicitação de alteração de SLO pode ser estimada pela heurística a seguir:

3 x (5 minutos + tamanho do banco de dados / 150 MB por minuto)

Por exemplo, se o tamanho do banco de dados for 50 GB, a latência da solicitação de alteração de SLO será estimada pela seguinte heurística:

3 x (5 minutos + 50 GB x 1024 MB por GB / 150 MB por minuto) ≈ 17 horas

As estimativas de limite inferior e superior usando essa heurística variam entre 15 minutos para um banco de dados vazio e, aproximadamente, 2 dias para um banco de dados de 150 GB. As estimativas podem ainda variar de acordo com as condições do data center.

**Latência ao alterar de um nível de desempenho superior para um nível de desempenho inferior**

De modo geral, não haverá movimentação de dados se o nível de desempenho do banco de dados for alterado de um nível de desempenho superior para um tamanho menor. Nesses casos, a latência da alteração de SLO é muito mais rápida e, normalmente, é concluída em questão de segundos.

Observe que a declaração acima aplica-se apenas aos downgrades entre camadas de serviço Premium e Standard. O downgrade para uma camada de serviço Web, Business ou Basic envolve movimentação de dados.

###Verificando o status da alteração de banco de dados
Você pode verificar o status do banco de dados durante uma atualização ou um downgrade entre camadas de serviço, ou ao alterar o nível de desempenho usando um dos métodos a seguir.

####Usando o Portal de Gerenciamento do Azure
1. Use sua conta da Microsoft para entrar no Portal de Gerenciamento do Azure.
2. Selecione um banco de dados na lista **Bancos de dados**. Essa ação abre o banco de dados no **Painel de Banco de Dados** ou na página **Início Rápido**.
3. No **Painel de Banco de Dados**, confira a área **Visão Rápida** para ver as informações de status na seção **Edição**.
4. O SLO (Objetivo de Nível de Serviço) representa o nível de desempenho em uma camada de serviço.


##Usando comandos do PowerShell do Azure
Esta seção fornece os pré-requisitos para usar os comandos do PowerShell do Azure.

**Pré-requisitos**

Para usar os cmdlets do PowerShell do Azure descritos neste tópico, você deve ter o software a seguir instalado no computador onde o PowerShell é executado. 
1. Baixe uma versão do Windows PowerShell que não seja inferior a 3.0 em http://www.microsoft.com/pt-br/download/details.aspx?id=34595.

2. Baixe o PowerShell do Azure na seção Ferramenta de linha de comando em [Downloads de ferramentas e SDK do Azure](http://azure.microsoft.com/downloads/).

Faça o seguinte: 
na tela **Inicial** ou no menu **Iniciar**, navegue até o PowerShell do Azure e o inicie.

Digite o nome de usuário e a senha do servidor.

Crie contexto do servidor usando **New-AzureSqlDatabaseServerContext**.

**Exemplo**

		Windows PowerShell
		$subId = <Subscription ID>
		$thumbprint = <Certificate Thumbprint>
		$myCert = Get-Item Cert:\CurrentUser\My\$thumbprint
		Set-AzureSubscription -SubscriptionName "mySubscription" -SubscriptionId $subId -Certificate $myCert
		Select-AzureSubscription -SubscriptionName "mySubscription"
		$serverContext = New-AzureSqlDatabaseServerContext -ServerName "myserver" -UseSubscription


**Referência do PowerShell do Azure**
Para ver informações detalhadas sobre os cmdlets do PowerShell do Azure usados neste tópico, confira [Cmdlets do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/dn546726.aspx).

[New-AzureSqlDatabaseServerContext](http://go.microsoft.com/fwlink/?LinkId=391026)

[New-AzureSqlDatabase](http://go.microsoft.com/fwlink/?LinkId=391027)

[Set-AzureSqlDatabase](http://go.microsoft.com/fwlink/?LinkId=391412)
 

<!---HONumber=July15_HO4-->