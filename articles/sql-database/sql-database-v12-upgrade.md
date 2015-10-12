<properties 
	pageTitle="Atualizar para o Banco de Dados SQL V12" 
	description="Explica como atualizar para o Banco de Dados SQL V12 do Azure, de uma versão anterior do Banco de Dados SQL do Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="09/30/2015" 
	ms.author="sstein"/>


# Atualizar para o Banco de Dados SQL V12


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


O Banco de Dados SQL V12 é a versão mais recente do Banco de Dados SQL e oferece [várias vantagens em relação à versão anterior do V2](sql-database-v12-whats-new.md). Este artigo mostra como atualizar os servidores do V2 para o V12 usando o portal de visualização do Azure.

Durante o processo de atualização para o Banco de Dados SQL V12, você também deverá [atualizar todos os bancos de dados da Web e Empresariais para uma nova camada de serviço](sql-database-upgrade-new-service-tiers.md). As instruções a seguir incluem as etapas para atualizar seus bancos de dados da Web e Empresariais com recomendações de tipo de preço com base no uso histórico do seu banco de dados.



1. No [Portal de Visualização do Azure](http://portal.azure.com/), navegue até o servidor que deseja atualizar selecionando **PROCURAR TUDO** > **SQL Servers** e selecionando o servidor desejado.
2. Selecione **atualização mais recente do banco de dados SQL** e, em seguida, selecione **Atualizar este servidor**.

      ![atualizar servidor][1]

## Atualizar seus bancos de dados da Web e Empresariais

2. Atualize todos os bancos de dados da Web e Empresariais. Se o seu servidor tiver qualquer banco de dados da Web ou Empresarial, é necessário atualizá-los. Para ajudá-lo com a atualização, o serviço do Banco de Dados SQL recomenda uma camada de serviço e nível de desempenho (tipo de preço) adequados para cada banco de dados. Ao analisar o uso histórico de cada banco de dados, o serviço recomenda uma camada que é mais adequada para a execução da carga de trabalho existente do banco de dados. 
    
    Selecione cada banco de dados para examinar e selecionar para qual tipo de preço recomendado ele será atualizado. Também é possível procurar vários tipos de preço e selecionar aquele que se adapte melhor ao seu ambiente.

     ![databases][2]



7. Depois de clicar na camada sugerida, será exibida a folha **Escolha sua camada de preços**, em que é possível clicar em uma camada e clicar no botão **Selecionar** para alterar para a camada em questão. Selecione uma nova camada para cada banco de dados da Web ou Empresarial

    ![filmes][6]


Depois que todos os bancos de dados no servidor forem qualificados, você estará pronto para iniciar a atualização

## Inicie a atualização

3. Quando todos os bancos de dados no servidor forem qualificados para a atualização, você precisará **DIGITAR O NOME DO SERVIDOR** para verificar que deseja executar a atualização e, em seguida, clique em **OK**. 

    ![verificar atualização][3]


4. A atualização é iniciada e exibe a notificação em andamento. O processo de atualização é iniciado. Dependendo dos detalhes de seus bancos de dados específicos, a atualização para o V12 pode levar algum tempo. Durante esse tempo, todos os bancos de dados no servidor permanecerão online, mas as ações de gerenciamento de servidor e de banco de dados serão restringidas.

    ![atualização em andamento][4]

    No momento da transição real para o novo nível de desempenho temporário podem ocorrer quedas das conexões com duração muito curtas (em segundos) com o banco de dados. Se um aplicativo tiver um tratamento de falha transitória (lógica de repetição) para encerramentos de conexão, isto será suficiente para proteger contra a queda de conexões ao final da atualização.

5. Depois de concluída a operação de atualização, os recursos do servidor do Banco de Dados SQL V12 estarão habilitados.

    ![V12 habilitado][5]


## Links relacionados

- [Novidades no Banco de Dados SQL V12](sql-database-v12-whats-new.md)
- [Planejar e se preparar para atualizar para o Banco de Dados SQL V12](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-v12-upgrade/latest-sql-database-update.png
[2]: ./media/sql-database-v12-upgrade/upgrade-server2.png
[3]: ./media/sql-database-v12-upgrade/upgrade-server3.png
[4]: ./media/sql-database-v12-upgrade/online-during-upgrade.png
[5]: ./media/sql-database-v12-upgrade/enabled.png
[6]: ./media/sql-database-v12-upgrade/recommendations.png

<!---HONumber=Oct15_HO1-->