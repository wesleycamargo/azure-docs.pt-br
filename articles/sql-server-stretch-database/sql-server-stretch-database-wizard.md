<properties
	pageTitle="Começar pela execução do assistente Habilitar o Banco de Dados para Ampliação | Microsoft Azure"
	description="Saiba como configurar um banco de dados para o Stretch Database executando assistente Habilitar Banco de Dados para Stretch."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# Comece ao executar o assistente Habilitar o Banco de Dados de Ampliação

Para configurar um banco de dados para Banco de Dados de Stretch, execute o assistente Habilitar Banco de Dados para Stretch. Este tópico descreve as informações que você precisa inserir e as escolhas que você deve fazer no assistente.

Para saber mais sobre o Banco de Dados de Stretch, confira [Banco de Dados de Stretch](sql-server-stretch-database-overview.md).

## Iniciar o assistente

1.  No SQL Server Management Studio, no Pesquisador de Objetos, escolha o banco de dados no qual você deseja habilitar o Stretch.

2.  Clique com botão direito do mouse e escolha **Tarefas** e, em seguida, escolha **Stretch** e **Habilitar** para iniciar o assistente.

## <a name="Intro"></a>Introdução
Examine o objetivo do assistente e os pré-requisitos.

Entre os pré-requisitos importantes estão os seguintes.
-   Você precisa ser administrador para alterar as configurações de banco de dados.
-   Você precisa ter uma assinatura do Microsoft Azure.
-   O SQL Server deve ser capaz de se comunicar com o servidor remoto do Azure.

![Página de introdução do assistente do Banco de Dados de Ampliação][StretchWizardImage1]

## <a name="Tables"></a>Selecionar tabelas
Selecione as tabelas que você deseja habilitar para o Stretch.

Tabelas com muitas linhas aparecem na parte superior da lista classificada. Antes de o assistente exibir a lista de tabelas, ele analisa se há tipos de dados que não têm suporte do Stretch Database no momento.

![Página Selecionar tabelas do assistente de Banco de Dados de Ampliação][StretchWizardImage2]

|Coluna|Descrição|
|----------|---------------|
|(sem título)|Marque a caixa de seleção nessa coluna para habilitar a tabela selecionada para Stretch.|
|**Nome**|Especifica o nome da coluna na tabela.|
|(sem título)|Um símbolo nessa coluna pode representar um aviso que não impede a ativação da tabela selecionada para o Stretch. Também pode representar um problema de bloqueio que impede a ativação da tabela selecionada para o Stretch; por exemplo, porque a tabela usa um tipo de dados sem suporte. Passe o cursor do mouse sobre o símbolo para exibir mais informações em uma dica de ferramenta. Para saber mais, confira [Limitações do Stretch Database](sql-server-stretch-database-limitations.md).|
|**Ampliado**|Indica se a tabela já está habilitada para o Stretch.|
|**Migrar**|Você pode migrar uma tabela inteira (**Tabela Inteira**) ou pode especificar um filtro em uma coluna existente na tabela. Se você quiser usar um predicado de filtro diferente para selecionar linhas para migrar, execute a instrução ALTER TABLE para especificar o predicado de filtro depois que você sair do assistente. Para saber mais sobre o predicado de filtro, confira [Selecionar linhas para migrar usando um predicado de filtro](sql-server-stretch-database-predicate-function.md). Para saber mais sobre como aplicar o predicado, confira [Habilitar o Stretch Database para uma tabela](sql-server-stretch-database-enable-table.md) ou [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**Linhas**|Especifica o número de linhas na tabela.|
|**Tamanho (KB)**|Especifica o tamanho da tabela em KB.|

## <a name="Filter"></a>Opcionalmente, forneça um filtro de linha

Se você quiser fornecer um predicado de filtro para selecionar linhas para migrar, faça o seguinte na página **Selecionar tabelas**.

1.  Na lista **Selecionar as tabelas que você deseja alongar**, clique em **Tabela Inteira** na linha da tabela. A caixa de diálogo **Selecionar linhas para alongar** é aberta.

    ![Definir um predicado de filtro][StretchWizardImage2a]

2.  Na caixa de diálogo **Selecionar linhas para alongar**, selecione **Escolher linhas**.

3.  No campo **Nome**, forneça um nome para o predicado do filtro.

4.  Na cláusula **Where**, escolha uma coluna na tabela, escolha um operador e forneça um valor.

5. Clique em **Verificar** para testar o predicado. Se o predicado retornar resultados da tabela, ou seja, se houver linhas para migrar que satisfaçam à condição, o teste mostrará**Sucesso**.

    >   [AZURE.NOTE] A caixa de texto que exibe a consulta de filtro é somente leitura. Você não pode editar a consulta na caixa de texto.

6.  Clique em Concluído para retornar à página **Selecionar tabelas**.

A função de filtro é criada no SQL Server somente após a conclusão do assistente. Até lá, você pode retornar para a página **Selecionar tabelas** para alterar ou renomear a função de filtro.

![Selecione a página Tabelas depois de definir um predicado de filtro][StretchWizardImage2b]

Se você quiser usar um tipo diferente de predicado de filtro para selecionar as linhas a serem migradas, siga um destes procedimentos.

-   Saia do assistente e execute a instrução ALTER TABLE para habilitar o Stretch para tabela e especificar um predicado. Para saber mais, confira [Habilitar o Stretch Database para uma tabela](sql-server-stretch-database-enable-table.md).  

-   Execute a instrução ALTER TABLE para especificar um predicado depois que você sair do assistente. Para saber as etapas necessárias, confira [Adicionar um predicado de filtro após executar o assistente](sql-server-stretch-database-predicate-function.md#addafterwiz).

## <a name="Configure"></a>Configurar a implantação do Azure

1.  Entre no Microsoft Azure com uma conta da Microsoft.

    ![Entrar no Azure - assistente do Banco de Dados de Ampliação][StretchWizardImage3]

2.  Escolha a assinatura do Azure existente para usar o Stretch Database.

3.  Selecione uma região do Azure.
    -   Se você criar um novo servidor, ele será criado nessa região.  
    -   Se você tiver servidores na região selecionada, o assistente os listará quando você escolher **Servidor existente**.

    Para minimizar a latência, selecione a região do Azure na qual o SQL Server está localizado. Para saber mais sobre regiões, confira [Regiões do Azure](https://azure.microsoft.com/regions/).

4.  Especifique se você quer usar um servidor existente ou criar um novo servidor do Azure.

    Se o Active Directory no SQL Server for federado com o Azure Active Directory, você poderá optar por usar uma conta de serviço federado para o SQL Server para se comunicar com o servidor remoto do Azure. Para saber mais sobre os requisitos dessa opção, confira [Opções de ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

	-   **Criar um novo servidor**

        1.  Crie um logon e senha para o administrador do servidor.

        2.  Como opção, use uma conta de serviço federado para o SQL Server se comunicar com o servidor remoto do Azure.

		![Criar um novo servidor do Azure - assistente do Banco de Dados de Ampliação][StretchWizardImage4]

    -   **Servidor existente**

        1.  Selecione o servidor existente do Azure.

        2.  Selecione o método de autenticação

            -   Se você escolher **Autenticação do SQL Server**, forneça o logon e senha de administrador.

            -   Escolha **Autenticação Integrada do Active Directory** para usar uma conta de serviço federado para o SQL Server se comunicar com o servidor remoto do Azure. Se o servidor selecionado não estiver integrado ao Azure Active Directory, essa opção não aparecerá.

		![Selecionar servidor existente do Azure - assistente do Banco de Dados de Ampliação][StretchWizardImage5]

## <a name="Credentials"></a>Proteger as credenciais
Você precisa ter uma chave mestra de banco de dados para proteger as credenciais usadas pelo Banco de Dados de Stretch para se conectar ao banco de dados remoto.

Se já houver uma chave mestra de banco de dados, digite a senha para ela.

![Página Proteger credenciais do assistente do Banco de Dados de Ampliação][StretchWizardImage6b]

Se o banco de dados não tiver uma chave mestra existente, digite uma senha forte para criar uma chave mestra de banco de dados.

![Página Proteger credenciais do assistente do Banco de Dados de Ampliação][StretchWizardImage6]

Para saber mais sobre a chave mestra de banco de dados, confira [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) e [Criar uma chave mestra de banco de dados](https://msdn.microsoft.com/library/aa337551.aspx). Para saber mais sobre as credenciais criadas pelo assistente, confira [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Selecionar o endereço IP
Use o intervalo de endereço IP de sub-rede (recomendado) ou o endereço IP público de seu SQL Server para criar uma regra de firewall no Azure que permita ao SQL Server se comunicar com o servidor remoto do Azure.

O endereço, ou endereços, IP que você fornece nesta página informa ao servidor do Azure para permitir a passagem pelo firewall do Azure de dados recebidos, consultas e operações de gerenciamento iniciadas pelo SQL Server. O assistente não altera nada nas configurações do firewall no SQL Server.

![Página Selecionar endereço IP do assistente do Banco de Dados de Ampliação][StretchWizardImage7]

## <a name="Summary"></a>Resumo
Examine os valores inseridos e as opções selecionadas no assistente e as estimativas de custo no Azure. Em seguida, escolha **Concluir** para habilitar o Stretch.

![Página de resumo do assistente do Banco de Dados de Ampliação][StretchWizardImage8]

## <a name="Results"></a>Resultados
Revise os resultados.

Para monitorar o status da migração de dados, confira [Monitorar e solucionar problemas de migração de dados (Stretch Database)](sql-server-stretch-database-monitor.md).

![Página de resultados do assistente do Stretch Database][StretchWizardImage9]

## <a name="KnownIssues"></a>Solução de problemas com o assistente
**O assistente do Banco de Dados de Stretch falhou.** Se o Banco de Dados de Stretch ainda não estiver habilitado no nível do servidor, e você executar o assistente sem as permissões de administrador do sistema para habilitá-lo, o assistente falhará. Peça ao administrador do sistema para habilitar o Banco de Dados de Stretch na instância do servidor local e, em seguida, execute o assistente novamente. Para saber mais, confira [Pré-requisito: permissão para habilitar o Stretch Database no servidor](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## Próximas etapas
Habilitar outras tabelas para o Banco de Dados de Stretch. Monitorar a migração de dados e gerenciar as tabelas e bancos de dados habilitados para o Stretch.

-   [Habilitar o Stretch Database para uma tabela](sql-server-stretch-database-enable-table.md) para habilitar outras tabelas.

-   [Monitorar e solucionar problemas de migração de dados](sql-server-stretch-database-monitor.md) para ver o status da migração de dados.

-   [Pausar e retomar o Banco de Dados de Stretch](sql-server-stretch-database-pause.md)

-   [Gerenciar e solucionar problemas do Banco de Dados de Stretch](sql-server-stretch-database-manage.md)

-   [Fazer backup de bancos de dados habilitados para o Stretch](sql-server-stretch-database-backup.md)

## Consulte também

[Habilitar o Banco de Dados de Stretch para um banco de dados](sql-server-stretch-database-enable-database.md)

[Habilitar o Banco de Dados de Stretch para uma tabela](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage6b]: ./media/sql-server-stretch-database-wizard/stretchwiz6b.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png
[StretchWizardImage9]: ./media/sql-server-stretch-database-wizard/stretchwiz9.png

<!---HONumber=AcomDC_0608_2016-->