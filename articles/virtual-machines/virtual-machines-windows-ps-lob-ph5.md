<properties 
	pageTitle="Aplicativo de linha de negócios Fase 5 | Microsoft Azure" 
	description="Crie um grupo de disponibilidade e adicione seus bancos de dados do aplicativo a ele na fase 5 do aplicativo de linha de negócios do Azure." 
	documentationCenter=""
	services="virtual-machines-windows" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2016" 
	ms.author="josephd"/>

# Carga de trabalho de aplicativo de linha de negócios fase 5: criar o grupo de disponibilidade e adicionar os bancos de dados do aplicativo

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

Nessa fase final da implantação de um aplicativo de linha de negócios de alta disponibilidade nos Serviços de Infraestrutura do Azure, você criará um novo Grupo de Disponibilidade AlwaysOn do SQL Server e adicionará os bancos de dados do aplicativo.

Consulte [Implantar um aplicativo de linha de negócios de alta disponibilidade no Azure](virtual-machines-windows-lob-overview.md) para todas as fases.

## Criar o Grupo de Disponibilidade e adicionar bancos de dados

Para cada banco de dados do aplicativo de linha de negócios:

1.	Faça um backup completo e um backup de log de transações do banco de dados na máquina virtual primária do SQL Server.
2.	Restaure os backups completo e de registro em log na máquina virtual do SQL Server de backup.

Depois que os bancos de dados tiverem sido armazenados em backup e restaurados, eles poderão ser adicionados ao grupo de disponibilidade. O SQL Server só permite no grupo bancos de dados que tenham sido armazenados em backup (pelo menos uma vez) e restaurados em outra máquina.

### Compartilhar as pastas de backup

Para habilitar o backup e a restauração, os arquivos de backup (.bak) devem poder ser acessados pelo servidor de banco de dados secundário. Use este procedimento:

1.	Faça logon no servidor de banco de dados primário como **[domínio]\\sqladmin**. 
2.	Navegue até o disco F:\\. 
3.	Com o botão direito na pasta **Backup** e clique em **Compartilhar com** e em **pessoas específicas**.
4.	Na caixa de diálogo **Compartilhamento de arquivo**, digite **[domain]\\sqlservice** e clique em **Adicionar**.
5.	Clique na coluna **Nível de permissão** para o nome da conta **sqlservice** e, em seguida, clique em **Leitura/Gravação**. 
6.	Clique em **Compartilhar** duas e em **Concluído**.

Execute o procedimento acima no servidor de banco de dados secundário, porém conceda à conta sqlservice a permissão de **Leitura** para a pasta F:\\Backup na etapa 5.

### Backup e restauração de um banco de dados

Os procedimentos a seguir devem ser repetidos para cada banco de dados que precise ser adicionado ao grupo de disponibilidade.

Use estas etapas para fazer backup de um banco de dados.

1.	Na Tela Inicial do servidor de banco de dados primário, digite **SQL Studio** e clique em **SQL Server Management Studio**.
2.	Clique em **Conectar**.
3.	No painel esquerdo, expanda o nó **Banco de Dados**.
4.	Clique com o botão direito no banco de dados que deseja armazenar em backup, aponte para **Tarefas**, e, em seguida, clique em **Fazer backup**.
5.	Na seção **Destino**, clique em **Remover** para remover o caminho de arquivo padrão para o arquivo de backup.
6.	Clique em **Adicionar**. Em **Nome do arquivo**, digite **\\[nomeDaMáquina]\\backup[nomeDoBancoDeDados].bak**, em que **nomeDaMáquina** é o nome do **computador do SQL Server** primário e **nomeDoBancoDeDados** é o nome do banco de dados. Clique em **OK** e em **OK** novamente após a mensagem avisando que o backup foi bem-sucedido.
7.	No painel esquerdo, clique com o botão direito do mouse em **[nomeDoBancoDeDados]**, aponte para **Tarefas** e clique em **Fazer Backup**.
8.	Em **Tipo de backup**, selecione **Log de Transações** e clique em **OK** duas vezes.
9.	Mantenha essa sessão de área de trabalho remota aberta.

Use estas etapas para restaurar um banco de dados.

1.	Faça logon no servidor de banco de dados secundário como [nome\_do\_domínio]\\sp\_farm\_db.
2.	Na tela inicial, digite **SQL Studio** e clique em **SQL Server Management Studio**.
3.	Clique em **Conectar**.
4.	No painel esquerdo, clique com botão direito em **Bancos de Dados** e clique em **Restaurar Banco de Dados**.
5.	Na seção **Fonte**, selecione **Dispositivo** e clique no botão de reticências (...)
6.	Em **Selecionar dispositivos de backup**, clique em **Adicionar**.
7.	Em **Local do arquivo de backup**, digite **\\[nomeDaMáquina]\\backup**, pressione **Enter**, selecione **[nomeDoBancoDeDados].bak** e clique em **OK** duas vezes. Agora você deverá ver o backup completo e o backup do log na seção **Conjuntos de backup a serem restaurados**.
8.	Em **Selecionar uma página**, clique em **Opções**. Na seção **Opções de restauração**, em **Estado de recuperação**, selecione **RESTAURAR COM NORECOVERY** e, em seguida, clique em **OK**. 
9.	Quando solicitado, clique em **OK**.

### Criar um Grupo de Disponibilidade

Depois que pelo menos um banco de dados estiver preparado (usando o método de backup e restauração), você poderá criar um Grupo de Disponibilidade.

1.	Retorne para a sessão de área de trabalho remota para o servidor de banco de dados primário.
2.	No **SQL Server Management Studio**, no painel esquerdo, clique com o botão direito do mouse em **Alta Disponibilidade AlwaysOn** e em **Assistente de Novo Grupo de Disponibilidade**.
3.	Na página **Introdução**, clique em **Avançar**. 
4.	Na página **Especificar o Nome do Grupo de Disponibilidade**, digite o nome do seu grupo de disponibilidade em **Nome do grupo de disponibilidade** (exemplo: AG1) e, em seguida, clique em **Avançar**.
5.	Na página **Selecionar Bancos de Dados**, selecione os bancos de dados do aplicativo que foram armazenados em backup e clique em **Avançar**. Esses bancos de dados atendem aos pré-requisitos para um grupo de disponibilidade, pois você fez pelo menos um backup completo na réplica primária pretendida.
6.	Na página **Especificar Réplicas**, clique em **Adicionar Réplica**.
7.	Em **Conectar ao Servidor**, digite o nome do servidor de banco de dados secundário e clique em **Conectar**. 
8.	Na página **Especificar Réplicas**, o servidor de banco de dados secundário estará listado em **Réplicas de Disponibilidade**. Para ambas as instâncias, defina os seguintes valores de opção: 

Função Inicial | Opção | Valor 
--- | --- | ---
Primário | Failover Automático (até 2) | Selecionado
Secundário | Failover Automático (até 2) | Selecionado
Primário | Confirmação Síncrona (até 3) | Selecionado
Secundário | Confirmação Síncrona (até 3) | Selecionado
Primário | Secundário Legível | Sim
Secundário | Secundário Legível | Sim
		
9.	Clique em **Próximo**.
10.	Na página **Selecionar Sincronização de Dados Inicial**, clique em **Somente união** e clique em **Avançar**. A sincronização de dados é executada manualmente ao restaurar os backups completo e de transações no servidor primário. Você também pode optar por selecionar **Completo** para permitir que o Assistente de Novo Grupo de Disponibilidade execute a sincronização de dados para você. No entanto, a sincronização não é recomendável para os grandes bancos de dados que podem ser encontrados em algumas empresas.
11.	Na página **Validação**, clique em **Avançar**. Há um aviso de configuração de ouvinte ausente porque um ouvinte do grupo de disponibilidade não foi configurado. 
12.	Na página **Resumo**, clique em **Concluir**. Quando o assistente for concluído, inspecione a página **Resultados** para verificar se o grupo de disponibilidade foi criado com êxito. Nesse caso, clique em **Fechar** para sair do assistente. 
13.	Na tela inicial, digite **Failover** e clique em **Gerenciador de Cluster de Failover**. No painel esquerdo, abra o nome do cluster e clique em **Funções**. Uma nova função com o nome do seu grupo de disponibilidade deve estar presente.

Você configurou com êxito um grupo de disponibilidade AlwaysOn do SQL Server para os seus bancos de dados do aplicativo.

![](./media/virtual-machines-windows-ps-lob-ph5/workload-lobapp-phase4.png)

Agora você está pronto para começar a distribuir esse novo aplicativo para os usuários da intranet.

## Configurar um ouvinte para o Grupo de Disponibilidade AlwaysOn

Você também pode criar uma configuração de ouvinte para o Grupo de Disponibilidade AlwaysOn. Para obter as etapas necessárias, consulte [Tutorial: Configuração de Ouvinte para Grupos de Disponibilidade AlwaysOn](https://msdn.microsoft.com/library/dn425027.aspx). Essas instruções guiarão você pela criação de apenas um único ouvinte (recomendado) e pelo uso de um endereço IP estático de uma instância de balanceamento de carga interno.

Depois de configurar o ouvinte, você precisa configurar todas as máquinas virtuais do servidor Web para usar o ouvinte, em vez do nome do primeiro SQL Server no cluster. Em vez de usar um novo nome e registro DNS mapeado para o endereço IP virtual da instância de balanceamento de carga interno, configure as máquinas virtuais do servidor Web para usar um Alias do SQL. Para obter detalhes e as etapas necessárias, consulte [Alias SQL para SharePoint](http://blogs.msdn.com/b/priyo/archive/2013/09/13/sql-alias-for-sharepoint.aspx).

## Próxima etapa

- Consulte estes [diretrizes](virtual-machines-linux-infrastructure-service-guidelines.md) se você estiver implantando sua própria carga de trabalho de TI no Azure.

<!---HONumber=AcomDC_0511_2016-->