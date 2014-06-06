<properties linkid="manage-services-sql-databases-datasync" urlDisplayName="Como sincronizar dados" pageTitle="Introdução à Sincronização de Dados de bancos de dados SQL" metaKeywords="" description="" metaCanonical="" services="sql-database" documentationCenter="" title="Introdução à Sincronização de Dados do SQL do Azure" authors=""  solutions="" writer="" manager="" editor=""  />





#Introdução à Sincronização de Dados do SQL do Azure
Neste tutorial, você aprenderá os conceitos básicos da Sincronização de Dados do SQL do Azure usando o portal do Azure (Visualização). 

Este tutorial pressupõe uma experiência mínima anterior com SQL Server e com o Banco de Dados SQL do Azure. Neste tutorial, você criará um grupo de sincronização híbrido (instâncias do Banco de Dados SQL e do SQL Server) totalmente configurado e sincronizando no agendamento definido.

##Sumário##

* [Etapa 1: Conectar-se ao Banco de Dados SQL do Azure](#Connect)
* [Etapa 2: Adicionar um Agente Cliente ()](#AddAgent)
* [Etapa 3: Registrar um banco de dados do SQL Server com o Agente Cliente](#RegisterSSDB)
* [Etapa 4: Criar um grupo de sincronização](#CreateSG)
* [Etapa 5: Definir os dados a serem sincronizados](#SyncRules)
* [Etapa 6: Configurar seu grupo de sincronização](#Configure)

<h2><a id="Connect"></a>Etapa 1: Conectar-se ao Banco de Dados SQL do Azure</h2>

1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com).

2. Clique em **BANCOS DE DADOS SQL** no painel esquerdo.

3. Clique em **SINCRONIZAR** na parte inferior da página. Quando você clica em SINCRONIZAR, uma lista é exibida mostrando os itens que você pode adicionar - **Novo Grupo de Sincronização** e **Novo Agente de Sincronização**

4. Para iniciar o assistente Novo Agente de Sincronização de Dados SQL, clique em **Novo Agente de Sincronização**.

5. Se você ainda não tiver adicionado um agente, **clique para baixá-lo aqui**.

	![Image1](./media/sql-database-get-started-data-sync/SQLDatabaseScreen-Figure1.PNG)


<h2><a id="AddAgent"></a>Etapa 2: Adicionar um Agente Cliente</h2>
Essa etapa somente será necessária se você pretender ter um banco de dados local do SQL Server incluído no seu grupo de sincronização. Você poderá pular para a etapa 4: Criar um grupo de sincronização se o seu grupo de sincronização tiver apenas instâncias do Banco de Dados SQL.

<h3><a id="InstallRequiredSoftware"></a>Etapa 2a: Instalar o software necessário</h3>
Certifique-se de que você tenha os itens a seguir no computador que você instalar o Agente Cliente.

- **.NET Framework 4.0** 

 Você pode instalar o .NET Framework 4.0 [daqui](http://go.microsoft.com/fwlink/?linkid=205836).

- **Tipos de CLR do sistema Microsoft SQL Server 2008 R2 SP1 (x86)**

 Você pode instalar os tipos de CLR do sistema Microsoft SQL Server 2008 R2 SP1 (x86) [daqui](http://www.microsoft.com/download/en/details.aspx?id=26728)

- **Objetos de gerenciamento compartilhado do Microsoft SQL Server 2008 R2 SP1 (x86)**

 Você pode instalar os objetos de gerenciamento compartilhado do Microsoft SQL Server 2008 R2 SP1 (x86) [daqui](http://www.microsoft.com/download/en/details.aspx?id=26728)


<h3><a id="InstallClient"></a>Etapa 2b: Instalar um novo Agente Cliente</h3>

Siga a instrução em [Instalar um Agente Cliente (Sincronização de Dados do SQL)](http://msdn.microsoft.com/pt-br/library/jj823137.aspx) para instalar o agente. 


<h3><a id="RegisterSSDb"></a>Etapa 2c: Concluir o assistente Novo Agente de Sincronização de Dados SQL</h3> 

1. 	Retorne ao o assistente Novo Agente de Sincronização de Dados SQL.
2.	Atribua um nome significativo ao agente.
3.	No menu suspenso, selecione a **REGIÃO** (data center) para hospedar esse agente.
4.	No menu suspenso, selecione a **ASSINATURA** para hospedar esse agente.
5.	Clique na seta para a direita.



<h2><a id="RegisterSSDB"></a>Etapa 3: Registrar um banco de dados do SQL Server com o Agente Cliente</h2>

Depois que o Agente Cliente for instalado, registre cada banco de dados do SQL Server local que você pretende incluir em um grupo de sincronização com o agente.
Para registrar um banco de dados com o agente, siga as instruções em [Registrar um banco de dados do SQL Server com um Agente Cliente](http://msdn.microsoft.com/pt-br/library/jj823138.aspx)



<h2><a id="CreateSG"></a>Etapa 4: Criar um grupo de sincronização</h2>

<h3><a id="StartNewSGWizard"></a>Etapa 4a: Iniciar o assistente Novo Grupo de Sincronização</h3>
1.	Retorne ao [Portal de Gerenciamento](http://manage.windowsazure.com).
2.	Clique em **BANCOS DE DADOS SQL**.
3.	Clique em **ADICIONAR SINCRONIZAÇÃO** na parte inferior da página e selecione Novo Grupo de Sincronização na gaveta.

	![Image2](./media/sql-database-get-started-data-sync/NewSyncGroup-Figure2.png)


<h3><a id=""></a>Etapa 4b: Inserir as configurações básicas</h3>	
1.	Insira um nome significativo para o grupo de sincronização.
2.	No menu suspenso, selecione a **REGIÃO** (Data Center) para hospedar esse grupo de sincronização.
3. Clique na seta para a direita.

	![Image3](./media/sql-database-get-started-data-sync/NewSyncGroupName-Figure3.PNG)
 
<h3><a id="DefineHubDB"></a>Etapa 4c: Definir o hub de sincronização</h3>
1. No menu suspenso, selecione a instância do Banco de Dados SQL para servir como hub do grupo de sincronização.
2. Insira as credenciais dessa instância do Banco de Dados SQL - **NOME DE USUÁRIO DO HUB** e **SENHA DO HUB**.
3. Aguarde a Sincronização de Dados do SQL para confirmar o NOME DE USUÁRIO e a SENHA. Uma marca de seleção verde é exibida à direita da SENHA quando as credenciais são confirmadas.
4. No menu suspenso, selecione a política **RESOLUÇÃO DE CONFLITOS**.

 **Hub Vence** - qualquer alteração gravada no banco de dados hub será gravada nos bancos de dados de referência, substituindo as alterações no mesmo registro de banco de dados de referência. Funcionalmente, isso significa que a primeira alteração gravada no hub é propagada para os outros bancos de dados.


 **Cliente Vence** - as alterações gravadas no hub são substituídas pelas alterações nos bancos de dados de referência. Funcionalmente, isso significa que a última alteração gravada no hub é a mantida e propagada para os outros bancos de dados.

5.	Clique na seta para a direita.

	![Image4](./media/sql-database-get-started-data-sync/NewSyncGroupHub-Figure4.PNG)

<h3><a id="AddRefDB"></a>Etapa 4d: Adicionar um banco de dados de referência</h3>

Repita essa etapa para cada banco de dados adicional que você quiser adicionar ao grupo de sincronização.

1. No menu suspenso, selecione o banco de dados a ser adicionado.

	Os bancos de dados no menu suspenso incluem os bancos de dados do SQL Server registrados com o agente e as instâncias do Banco de Dados SQL.
2.	Insira as credenciais desse banco de dados - **NOME DE USUÁRIO** e **SENHA**.
3.	No menu suspenso, selecione a **DIREÇÃO DE SINCRONIZAÇÃO** para esse banco de dados.

	**Bidirecional** - as alterações no banco de dados de referência são gravadas nos bancos de dados hub, e as alterações no banco de dados hub são gravadas no banco de dados de referência.

	**Sincronizar do Hub** - o banco de dados recebe atualizações do Hub. Ele não envia alterações para o Hub.

	**Sincronizar com o Hub** - o banco de dados envia atualizações para o Hub. As alterações no Hub não são gravadas nesse banco de dados.

4.	Para concluir a criação do grupo de sincronização, clique na marca de seleção localizada no canto inferior direito do assistente. Aguarde até que a Sincronização de Dados do SQL confirme as credenciais. Uma marca de seleção verde indica que as credenciais foram confirmadas.

5.	Clique na marca de seleção uma segunda vez. Isso o retornará à página **SINCRONIZAR** em Bancos de Dados SQL. Esse grupo de sincronização agora está listado com seus outros grupos de sincronização e agentes.

	![Image5](./media/sql-database-get-started-data-sync/NewSyncGroupReference-Figure5.PNG)


<h2><a id="SyncRules"></a>Etapa 5: Definir os dados a serem sincronizados</h2>

A Sincronização de Dados do SQL do Azure permite que você selecione tabelas e colunas para sincronização. Se desejar filtrar uma coluna de modo que apenas as linhas com valores específicos (como Idade > = 65) sejam sincronizadas, use o portal Sincronização de Dados do SQL no Azure e a documentação em Selecione as tabelas, colunas e linhas para sincronizar para definir os dados para sincronização.

1.	Retorne ao [Portal de Gerenciamento](http://manage.windowsazure.com).
2.	Clique em **BANCOS DE DADOS SQL**.
3.	Clique na guia **SINCRONIZAR**.
4.	Clique no nome desse grupo de sincronização.
5.	Clique na guia **REGRAS DE SINCRONIZAÇÃO**.
6.	Selecione o banco de dados que você deseja fornecer ao esquema de grupo de sincronização.
7.	Clique na seta para a direita.
8.	Clique em **ATUALIZAR ESQUEMA**.
9.	Para cada tabela no banco de dados, selecione as colunas a serem incluídas nas sincronizações. 
	- Não é possível selecionar colunas com tipos de dados sem suporte. 
	- Se nenhuma coluna de uma tabela estiver selecionadas, a tabela não será incluída no grupo de sincronização. 
	- Para selecionar/cancelar a seleção de todas as tabelas, clique em SELECIONAR na parte inferior da tela.
10.	Clique em **SALVAR** e aguarde até que o grupo de sincronização conclua o provisionamento.
11.	Para retornar à página de aterrissagem Sincronização de Dados, clique na seta para voltar localizada na parte superior esquerda da tela (acima do nome do grupo de sincronização).

	![Image6](./media/sql-database-get-started-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

<h2><a id="Configure"></a>Etapa 6: Configurar seu grupo de sincronização</h2>

Você poderá sempre sincronizar um grupo de sincronização, clicando em SINCRONIZAR na parte inferior da página de aterrissagem Sincronização de Dados.
Se desejar que um grupo de sincronização seja sincronizado em um agendamento, configure o grupo de sincronização.

1.	Retorne ao [Portal de Gerenciamento](http://manage.windowsazure.com).
2.	Clique em **BANCOS DE DADOS SQL**.
3.	Clique na guia **SINCRONIZAR**.
4.	Clique no nome desse grupo de sincronização.
5.	Clique na guia **CONFIGURAR**.
6.	**SINCRONIZAÇÃO AUTOMÁTICA**
	- Para configurar o grupo de sincronização para sincronizar em uma frequência definida, clique em **ATIVAR**. Você ainda poderá sincronizar sob demanda, clicando em SINCRONIZAR.
	- Clique em **DESATIVAR** para configurar o grupo de sincronização para sincronizar somente quando você clicar em SINCRONIZAR.
7.	**FREQUÊNCIA DE SINCRONIZAÇÃO**
	- Se SINCRONIZAÇÃO AUTOMÁTICA estiver definida como ATIVAR, defina a frequência de sincronização. A frequência deve ser um valor entre 5 Minutos e 1 Mês.
8.	Clique em **SALVAR**.

![Image7](./media/sql-database-get-started-data-sync/NewSyncGroupConfigure-Figure7.PNG)

Parabéns. Você criou um grupo de sincronização que inclui uma instância do Banco de Dados SQL e um banco de dados do SQL Server.

<h2><a id="NextSteps"></a>Próximas etapas</h2>
Para obter informações adicionais sobre banco de dados SQL e Sincronização de Dados do SQL, consulte:

* [Inscreva-se para a oferta Premium do banco de dados SQL] (../sign-up-for-sql-database-premium/)
* [Sincronização de Dados do SQL (Portal do WA)](http://msdn.microsoft.com/pt-br/library/windowsazure/jj856263.aspx)
* [Introdução ao banco de dados SQL do Azure](../getting-started-w-sql-databases/)
* [Ciclo de vida de banco de dados do SQL Server ](http://go.microsoft.com/fwlink/?LinkId=275193)










