<properties
	pageTitle="Farm do SharePoint Server 2013 Fase 5 | Microsoft Azure"
	description="Criar um grupo de disponibilidade e adicionar seus bancos de dados do SharePoint a ele na fase 5 do farm do SharePoint Server 2013 no Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# Fase 5 da carga de trabalho da intranet do SharePoint: Criar o grupo de disponibilidade e adicionar os bancos de dados do SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda a criação de recursos com o modelo clássico de implantação.

Na última fase da implantação de um farm do SharePoint 2013 somente intranet com Grupos de Disponibilidade AlwaysOn do SQL Server nos serviços de infraestrutura do Azure, você deve criar um novo grupo de disponibilidade AlwaysOn e adicionar os bancos de dados do farm do SharePoint.

Consulte [Implantando o SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure](virtual-machines-workload-intranet-sharepoint-overview.md) para conhecer todas as fases.

## Criar o grupo de disponibilidade e adicionar bancos de dados

Como parte da configuração inicial, o SharePoint cria vários bancos de dados. Esses bancos de dados devem ser preparados de acordo com as etapas:

1.	Faça um backup completo e um backup do log de transações do banco de dados na máquina primária.
2.	Restaure o backups completo e do log na máquina de backup.

Depois que os bancos de dados tiverem sido armazenados em backup e restaurados, eles poderão ser adicionados ao grupo de disponibilidade. O SQL Server só permite no grupo bancos de dados que tenham sido armazenados em backup (pelo menos uma vez) e restaurados em outra máquina.

### Compartilhar as pastas de backup

Para habilitar o backup e a restauração, certifique-se de que os arquivos de backup (.bak) podem ser acessados pela VM secundária do SQL Server. Use este procedimento:

1.	Faça logon no host do SQL Server primário como **[domínio]\\sp\_farm\_db**.
2.	Navegue até o disco F:\\.
3.	Clique com o botão direito na pasta **Backup**, clique em **Compartilhar com** e em **pessoas específicas**.
4.	Na caixa de diálogo **Compartilhamento de arquivo**, digite **[domain]\\sqlservice** e clique em **Adicionar**.
5.	Clique na coluna **Nível de permissão** para o nome da conta **sqlservice** e, em seguida, clique em **Leitura/Gravação**.
6.	Clique em **Compartilhar** duas e em **Concluído**.

Execute o procedimento acima no host do SQL Server secundário, mas conceda à conta sqlservice permissão de **Leitura** para a pasta F:\\Backup na etapa 5.

### Fazer backup e restaurar um banco de dados

Os procedimentos a seguir devem ser repetidos para cada banco de dados que precise ser adicionado ao grupo de disponibilidade. Alguns bancos de dados do SharePoint 2013 não dão suporte a Grupos de Disponibilidade AlwaysOn do SQL Server. Para saber mais, consulte [Opções de alta disponibilidade e recuperação de desastres compatíveis com bancos de dados do SharePoint](http://technet.microsoft.com/library/jj841106.aspx).

Use estas etapas para fazer backup de um banco de dados:

1.	Na tela inicial do computador do SQL Server primário, digite **SQL Studio** e clique em **SQL Server Management Studio**.
2.	Clique em **Conectar**.
3.	No painel esquerdo, expanda o nó **Banco de Dados**.
4.	Clique com o botão direito no banco de dados que deseja armazenar em backup, aponte para **Tarefas**, e, em seguida, clique em **Fazer backup**.
5.	Na seção **Destino**, clique em **Remover** para remover o caminho de arquivo padrão para o arquivo de backup.
6.	Clique em **Adicionar**. Em **Nome do arquivo**, digite **\\[nomeDaMáquina]\\backup[nomeDoBancoDeDados].bak**, em que nomeDaMáquina é o nome do computador do SQL Server primário e nomeDoBancoDeDados é o nome do banco de dados. Clique em **OK** e em **OK** novamente após a mensagem avisando que o backup foi bem-sucedido.
7.	No painel esquerdo, clique com o botão direito do mouse em **[nomeDoBancoDeDados]**, aponte para **Tarefas** e clique em **Fazer Backup**.
8.	Em **Tipo de backup**, selecione **Log de Transações** e clique em **OK** duas vezes.
9.	Mantenha essa sessão de área de trabalho remota aberta.

Use estas etapas para restaurar um banco de dados:

1.	Faça logon no computador do SQL Server secundário como **[nomeDoDomínio]\\sp\_farm\_db**.
2.	Na tela inicial, digite **SQL Studio** e clique em **SQL Server Management Studio**.
3.	Clique em **Conectar**.
4.	No painel esquerdo, clique com botão direito em **Bancos de Dados** e clique em **Restaurar Banco de Dados**.
5.	Na seção **Fonte**, selecione **Dispositivo** e clique no botão de reticências (...).
6.	Em **Selecionar dispositivos de backup**, clique em **Adicionar**.
7.	Em **Local do arquivo de backup**, digite **\\[nomeDaMáquina]\\backup**, pressione Enter, selecione **[nomeDoBancoDeDados].bak** e, em seguida, clique em **OK** duas vezes. Agora você deverá ver o backup completo e o backup do log na seção **Conjuntos de backup a serem restaurados**.
8.	Em **Selecionar uma página**, clique em **Opções**. Na seção **Opções de restauração**, em **Estado de recuperação**, selecione **RESTAURAR COM NORECOVERY** e, em seguida, clique em **OK**.
9.	Quando solicitado, clique em **OK**.

### Criar um grupo de disponibilidade

Depois que pelo menos um banco de dados estiver preparado (usando o método de backup e restauração), você poderá criar um grupo de disponibilidade.

1.	Retorne para a sessão de área de trabalho remota do computador do SQL Server primário.
2.	No **SQL Server Management Studio**, no painel esquerdo, clique com o botão direito em **Alta Disponibilidade AlwaysOn** e em **Assistente de Novo Grupo de Disponibilidade**.
3.	Na página **Introdução**, clique em **Avançar**.
4.	Na página **Especificar o Nome do Grupo de Disponibilidade**, digite o nome do seu grupo de disponibilidade em **Nome do grupo de disponibilidade** (exemplo: AG1) e, em seguida, clique em **Avançar**.
5.	Na página **Selecionar Bancos de Dados**, selecione os bancos de dados do farm do SharePoint que foram armazenados em backup e clique em **Avançar**. Esses bancos de dados atendem aos pré-requisitos para um grupo de disponibilidade, pois você fez pelo menos um backup completo na réplica primária pretendida.
6.	Na página **Especificar Réplicas**, clique em **Adicionar Réplica**.
7.	Em **Conectar ao Servidor**, digite o nome do computador do SQL Server secundário e, em seguida, clique em **Conectar**.
8.	Na página **Especificar Réplicas**, o host do SQL Server secundário estará listado em **Réplicas de Disponibilidade**. Para ambas as instâncias, defina os seguintes valores de opção:

Função inicial | Opção | Valor
--- | --- | ---
Primário | Failover Automático (até 2) | Selecionado
Secundário | Failover Automático (até 2) | Selecionado
Primário | Confirmação Síncrona (até 3) | Selecionado
Secundário | Confirmação Síncrona (até 3) | Selecionado
Primário | Secundário Legível | Sim
Secundário | Secundário Legível | Sim

9.	Clique em **Próximo**.  
10.	Na página **Selecionar Sincronização de Dados Inicial**, clique em **Somente união** e clique em **Avançar**. A sincronização de dados é executada manualmente ao restaurar os backups completo e de transações no servidor primário. Você também pode optar por selecionar **Completo** para permitir que o Assistente de Novo Grupo de Disponibilidade execute a sincronização de dados para você. No entanto, não recomendamos sincronização para os grandes bancos de dados que podem ser encontrados em algumas empresas.  
11.	Na página **Validação**, clique em **Avançar**. Há um aviso de configuração de ouvinte ausente porque um ouvinte do grupo de disponibilidade não foi configurado.
12.	Na página **Resumo**, clique em **Concluir**. Quando o assistente for concluído, inspecione a página **Resultados** para verificar se o grupo de disponibilidade foi criado com êxito. Nesse caso, clique em **Fechar** para sair do assistente.
13.	Na tela inicial, digite **Failover** e clique em **Gerenciador de Cluster de Failover**. No painel esquerdo, abra o nome do cluster e clique em **Funções**. Uma nova função com o nome do seu grupo de disponibilidade deve estar presente.  

Você configurou com êxito um grupo de disponibilidade AlwaysOn do SQL Server para os seus bancos de dados do SharePoint.

## Configurar um ouvinte para o grupo de disponibilidade AlwaysOn

Você também pode criar uma configuração de ouvinte para o grupo de disponibilidade AlwaysOn. Para obter as etapas necessárias, consulte [Tutorial: configuração de ouvinte para grupos de disponibilidade AlwaysOn](https://msdn.microsoft.com/library/dn425027.aspx). Você deve criar apenas um ouvinte e configurá-lo para usar um endereço IP virtual de uma instância de balanceamento de carga interna.

Depois de criar o ouvinte, você precisa configurar todas as máquinas virtuais do SharePoint para usar o ouvinte, em vez do nome do primeiro computador do SQL Server no cluster. Em vez de usar nomes, configure as máquinas virtuais do SharePoint para usar um alias SQL. Para obter detalhes e as etapas necessárias, consulte [Alias SQL para SharePoint](http://blogs.msdn.com/b/priyo/archive/2013/09/13/sql-alias-for-sharepoint.aspx).

Para saber mais sobre o SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server, consulte [Configurar Grupos de Disponibilidade AlwaysOn do SQL Server 2012 para o SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).


## Recursos adicionais

[Implantação do SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Farms do SharePoint hospedados nos serviços de infraestrutura do Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Infográfico do SharePoint com SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Arquiteturas do Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Diretrizes de implementação dos Serviços de Infraestrutura do Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carga de trabalho dos Serviços de Infraestrutura do Azure: aplicativo de linha de negócios de alta disponibilidade](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=Sept15_HO3-->