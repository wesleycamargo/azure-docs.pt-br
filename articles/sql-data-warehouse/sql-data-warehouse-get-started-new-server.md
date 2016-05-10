<properties
   pageTitle="Criar um banco de dados do SQL Data Warehouse no Portal do Azure | Microsoft Azure"
   description="Saiba como criar um Azure SQL Data Warehouse no Portal do Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/03/2016"
   ms.author="lodipalm;"/>

# Configurar um novo servidor lógico

No Banco de Dados SQL e no SQL Data Warehouse, cada banco de dados é atribuído a um servidor, e cada servidor é atribuído a uma localização geográfica. O servidor é chamado de servidor SQL lógico.

> [AZURE.NOTE] <a name="note"></a>Um servidor SQL lógico:
  >
  > + Fornece uma maneira consistente de configurar vários bancos de dados no mesmo local geográfico.
  > + Não é o hardware físico, como um servidor local. Faz parte do software de serviço. É por isso que o chamamos de *servidor lógico*.
  > + Pode hospedar vários bancos de dados sem afetar o desempenho deles.
  > + Usa um *s* minúsculo em seu nome. O **s**ervidor SQL é um servidor lógico do Azure, enquanto o SQL **S**erver é o produto de banco de dados local da Microsoft.

1. Clique em **Servidor** > **Criar um novo servidor**. Não há cobrança para o servidor. Se você já tiver um servidor SQL lógico V12 que deseje usar, escolha o servidor existente e vá para a próxima seção.

    ![Criar um novo servidor](./media/sql-data-warehouse-get-started-provision/create-server.png)

2. Preencha as informações do **Novo servidor**.

	- **Nome do servidor**: insira um nome para o servidor lógico. Isso é exclusivo para cada localização geográfica.
	- **Nome do administrador do servidor**: insira um nome de usuário para a conta do administrador do servidor.
	- **Senha**: insira a senha do administrador do servidor.
	- **Local**: escolha uma localização geográfica para o servidor. Para reduzir o tempo de transferência de dados, é melhor colocar o servidor em uma localização geográfica próxima de outros recursos de dados que esse banco de dados acessará.
	- **Criar servidor V12**: SIM é a única opção para o SQL Data Warehouse.
	- **Permitir que os serviços do Azure acessem o servidor**: essa opção é sempre marcada para o SQL Data Warehouse

    >[AZURE.NOTE] Lembre-se de armazenar o nome do servidor, o nome do administrador do servidor e a senha em algum lugar. Você precisará dessas informações para fazer logon no servidor.

3. Clique em **OK** para salvar as definições de configuração de servidor SQL lógico e retornar à folha SQL Data Warehouse.

    ![Configurar novo servidor](./media/sql-data-warehouse-get-started-provision/configure-server.png)

<!---HONumber=AcomDC_0504_2016-->