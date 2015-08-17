<properties
	pageTitle="Introdução ao Banco de Dados SQL"
	description="Crie em questão de minutos seu primeiro banco de dados de nuvem com o Banco de Dados SQL do Azure, o serviço de gerenciamento de bando de dados relacional da Microsoft (RDBMS) na nuvem, usando o Portal do Azure e o exemplo de banco de dados AdventureWorks."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/15/2015"
	ms.author="genemi"/>


# Criar seu primeiro Banco de Dados SQL do Azure


Este artigo mostra como criar um exemplo de Banco de Dados SQL do Azure em menos de cinco minutos. Você aprenderá a:


- Provisionar um servidor lógico usando o [Portal do Azure](http://portal.azure.com/).
- Criar um banco de dados preenchido com dados de exemplo.
- Definir uma regra de firewall para o banco de dados a fim de configurar quais endereços IP poderão acessar o banco de dados.


Este tutorial presume que você tem uma Assinatura do Azure. Se não tiver, inscreva-se em uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).


## Etapa 1: Entrar


1. Entre no [Portal do Azure](http://portal.azure.com/).
2. Clique em **Novo** > **Dados + Armazenamento** > **Banco de Dados SQL**.


![Novo banco de dados SQL][1]


## Etapa 2: Criar o servidor lógico



1. Na folha Banco de Dados SQL, escolha um **Nome** para seu banco de dados; neste exemplo **AdventureWorks**.
2. Para criar o servidor lógico do banco de dados, clique em **Servidor** e, em seguida, em **Criar um novo servidor**.


## Etapa 3: Configurar o servidor


1. Na folha **Servidor**, insira o **Nome do Servidor** como um nome exclusivo e fácil de lembrar.
2. Insira o **Logon de Administração do Servidor** como **AdventureAdmin**.
3. Insira o valor correto para **Senha** e **Confirmar Senha**.
4. Selecione o **Local** geográfico preferido. Normalmente, o local deve ser próximo de onde o aplicativo será executado.
5. Clique em **OK**.


![Criar servidor][2]


## Etapa 4: Criar o banco de dados


1. Na folha Banco de Dados SQL, especifique a origem do banco de dados clicando em **Selecionar Origem**.
 - Se você ignorar esta etapa, um banco de dados vazio será criado.
2. Selecione **Amostra**.
 - Isso cria um banco de dados que é uma cópia do banco de dados de exemplo padrão chamado **AdventureWorks**.
 - No Banco de Dados SQL do Azure, a edição de *esquema leve* do AdventureWorks é usada.
3. Clique em **Criar** na parte inferior da folha.


## Etapa 5: Configurar o firewall


As etapas a seguir demonstram como especificar quais intervalos de endereços IP têm permissão para acessar o banco de dados.


![Procurar servidor][3]


1. Na faixa de opções à esquerda da tela, clique em **Procurar** e em **Servidores SQL**.
2. Nas opções disponíveis, clique no servidor SQL criado anteriormente.
3. Clique em **Configurações** e, em seguida, em **Firewall**.
4. Clique neste link para obter seu endereço IP atual no [Bing](http://www.bing.com/search?q=my%20ip%20address).
5. Nas Configurações do Firewall, insira um **Nome de Regra** e cole o endereço IP público da etapa anterior nos campos **IP Inicial** e **IP Final**.
6. Após a conclusão, clique em **Salvar** na parte superior da página.


![Firewall][4]


## Próximas etapas


Agora você está pronto para escrever um pequeno programa cliente que pode se conectar ao banco de dados. Para obter um exemplo de código para início rápido, clique em um dos links a seguir:


- [Conectar-se e consultar o Banco de Dados SQL com C#](sql-database-connect-query.md)
- *Em breve:* Desenvolvimento do cliente e exemplos de código de início rápido para o Banco de Dados SQL


<!-- Media references. -->
[1]: ./media/sql-database-get-started/GettingStarted_NewDB.PNG
[2]: ./media/sql-database-get-started/GettingStarted_CreateServer.png
[3]: ./media/sql-database-get-started/GettingStarted_BrowseServer.png
[4]: ./media/sql-database-get-started/GettingStarted_FireWall.png
 

<!---HONumber=August15_HO6-->