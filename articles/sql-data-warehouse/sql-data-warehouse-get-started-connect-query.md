<properties
   pageTitle="Introdução: conectar-se ao SQL Data Warehouse do Azure| Microsoft Azure"
   description="Introdução ao SQL Data Warehouse e execução de algumas consultas."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Introdução: conectar-se ao SQL Data Warehouse do Azure
Este artigo de início rápido mostra como se conectar e executar consultas a uma instância de provisionamento do Data Warehouse do SQL com duas ferramentas diferentes:

- **Visual Studio** – editor de código e depurador integrado ao Visual Studio, o SQL Server Data Tools (SSDT) é totalmente compatível com o SQL Data Warehouse, permitindo que você se conecte, execute consultas e gerencie facilmente o SQL Data Warehouse.  

> [AZURE.NOTE]O SQL Data Warehouse requer pelo menos a versão de visualização do SSDT 12.0.50623 ou posterior.

- **sqlcmd** – sqlcmd é uma ferramenta de linha de comando que oferece recursos simples de conexão e de consulta.  

Depois de concluir este artigo, você terá:

1. Instalado e atualizado o Visual Studio 2013.
2. Criado uma conexão com o SQL Data Warehouse no SSDT.
3. Executado consultas no banco de dados do SQL Data Warehouse.

>[AZURE.NOTE]Supõe-se que você tenha concluído o guia de provisionamento ou tenha um SQL Data Warehouse disponível. Se você não tiver provisionado o SQL Data Warehouse, consulte novamente o [guia de início rápido do provisionamento](sql-data-warehouse-get-started-provision.md).

## Configurar o Visual Studio para desenvolvimento##
Para o desenvolvimento, a equipe do SQL Data Warehouse recomenda usar o Visual Studio 2013 ou posterior combinado ao SQL Server Data Tools. Veja a seguir como baixar e atualizar o Visual Studio 2013, caso você ainda não tenha uma versão viável do Visual Studio instalada.

Se você estiver procurando mais informações, perguntas gerais do SSDT podem ser resolvidas usando a [documentação completa do SSDT ](https://msdn.microsoft.com/library/azure/hh272686.aspx).

### Obtenha o Visual Studio 2013: ###
vá até o site do Visual Studio 2013 para baixar e instalar uma cópia do Visual Studio. Lembre-se, para o SQL Data Warehouse, qualquer uma das edições gratuitas é mais do que adequada. Fique à vontade para escolher uma que atenda às suas necessidades

<a href="https://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5" target="blank">Obtenha o Visual Studio 2013</a>

### Atualize o Visual Studio 2013 ###
Já tem o Visual Studio 2013 instalado? Ótimo! Para verificar se ele é atualizado, apenas execute as seguintes etapas. Você pode seguir para a próxima etapa.

1. Abra o Visual Studio 2013.
2. Escolha o menu **Ferramentas** e selecione **Extensões e Atualizações**.
3. Navegue pelo controle em árvore até **Atualizações** e **Atualizações do Produto**.
4. Se não houver uma atualização disponível, feche a janela **Extensões e Atualizações** e siga para a próxima tarefa deste guia de início rápido.

No entanto, se houver uma atualização para o Visual Studio, clique no botão **Atualizar**. Isso iniciará um download da atualização mais recente do Visual Studio 2013.

Feche a janela **Extensões e Atualizações** e também o Visual Studio 2013 antes de continuar.

5. Clique no botão **Executar** para instalar a atualização mais recente do Visual Studio 2013.

6. Concorde com os termos de licença e clique no botão **Instalar**, aceitando qualquer solicitação do Controle de Conta de Usuário (UAC).

7. Clique no botão **Iniciar** para concluir a instalação.

Isso conclui a atualização do Visual Studio 2013.

### Atualize o SSDT
> [AZURE.IMPORTANT]O SQL Data Warehouse requer pelo menos a versão de visualização do SSDT 12.0.50623 ou posterior.

Os engenheiros do SSDT atualizam o plug-in com muita frequência a fim de incluir novos recursos. Portanto, você perceberá que precisa atualizá-lo periodicamente. Novamente, é um processo bem simples. Para verificar se você precisa atualizar o SSDT, execute as seguintes etapas:

1. Abra o Visual Studio 2013.  
2. Escolha o menu **Ferramentas** e então selecione **Extensões e Atualizações**.
3. Navegue pelo controle em árvore até **Atualizações** e **Atualizações do Produto**.
4. Se não houver uma atualização disponível, feche a janela **Extensões e Atualizações** e siga para a próxima tarefa deste guia de início rápido.

No entanto, se existir uma atualização chamada "Atualização do Microsoft SQL Server para ferramentas de banco de dados", clique no botão **Atualizar**. Isso iniciará um download da versão mais recente do SSDT.

5. Clique no botão **Executar** para instalar a versão mais recente do SSDT.

6. Concorde com os termos de licença e clique no botão **Instalar**.

7. Clique no botão **Fechar** para concluir a atualização do SSDT.

8. Feche a janela **Extensões e Atualizações**.

Agora você tem uma versão atualizada do Visual Studio 2013 na área de trabalho com uma extensão do SSDT atualizada.

> [AZURE.NOTE]No momento, recomendamos a [Visualização do SSDT para Visual Studio 2013 versão 12.0.50623 ou posterior](http://go.microsoft.com/fwlink/?LinkID=616714&clcid=0x409).

## Conecte-se com o Visual Studio 2013
Se você estiver executando a versão desejada do Visual Studio, poderá se conectar à instância do SQL Data Warehouse de duas maneiras diferentes.

### Conectar-se ao Visual Studio
Para fazer a conexão, tudo o que precisamos fazer é abrir o Pesquisador de Objetos do SQL Server e passar as informações de conexão.

1. Abra o Visual Studio.
2. Escolha o menu **Exibir** e selecione **Pesquisador de Objetos do SQL Server** na lista suspensa.

> [AZURE.NOTE]Escolha o Pesquisador de Objetos do SQL Server e *não* o Gerenciador de Servidores. Os nomes são semelhantes, mas eles são controles muito diferentes. Eles estão localizados próximos um do outro, portanto, tenha cuidado e selecione corretamente.

Agora você pode ver o Pesquisador de objetos do SQL Server:


3. Clique no botão **Adicionar Servidor** no Pesquisador de Objetos do SQL Server.

4. Preencha a caixa de diálogo **Conectar-se ao Servidor** com os valores que você escolheu ao criar o servidor lógico. Além disso, clique no botão de opções e especifique o banco de dados ao qual você está se conectando (sua instância do SQL Data Warehouse) antes de se conectar.

Fique à vontade para selecionar a caixa **Lembrar Senha**, se quiser. Isso economiza algum tempo, mas lembre-se de que isso também permite que qualquer pessoa com acesso físico ao seu perfil execute consultas usando essa conta.

> [AZURE.NOTE]Lembre-se de que o nome do servidor deve ser totalmente qualificado. Portanto, o valor de nome do seu servidor deve seguir esta convenção: *NomeDoServidor*.database.windows.net, em que *NomeDoServidor* é o nome que você atribuiu ao seu servidor lógico.

Clique em **Conectar** depois de preencher completamente as credenciais.

Você acabou de concluir uma conexão e de registrar seu servidor lógico do SQL Data Warehouse no Pesquisador de Objetos do SQL Server.

### Conectar-se do portal do Azure
Acesse o Visual Studio diretamente no portal do Azure.

1. Entre no [Portal do Azure](http://manage.windowsazure.com/).
2. Selecione a instância desejada do SQL Data Warehouse na folha **Procurar**.
3. Na parte superior da folha de seu SQL Data Warehouse, selecione **Abrir no Visual**.
4. Se você não tiver configurado o firewall com o IP do computador cliente, selecione **Configurar seu firewall**.

	a. Insira um **Nome da Regra**: **IP Inicial** e **IP Final**.

	b. Clique em **Salvar** na parte superior da folha.
5. Clique em **Abrir no Visual Studio**.
6. O Visual Studio abrirá e suas credenciais serão solicitadas.
7. Depois de inserir suas credenciais e de conectar-se, seu servidor e as instâncias de Data Warehouse aparecerão no painel Pesquisador de Objetos do SQL Server.  

## Conecte-se ao SQL Data Warehouse com sqlcmd

Você também pode se conectar ao SQL Data Warehouse com o utilitário de prompt de comando [sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx) incluído no SQL Server ou [Utilitários de linha de comando 11 da Microsoft para SQL Server](http://go.microsoft.com/fwlink/?LinkId=321501). O utilitário sqlcmd permite inserir instruções Transact-SQL, procedimentos do sistema e arquivos de script no prompt de comando.

Para se conectar a uma instância específica do SQL Data Warehouse usando o sqlcmd, você precisará abrir o prompt de comando e inserir **sqlcmd** seguido pela cadeia de conexão do banco de dados do SQL Data Warehouse. A cadeia de conexão deverá conter os seguintes parâmetros:

+ **Usuário (-U):** usuário do servidor no formato `<`Usuário`>`@`<`Nome do Servidor`>`.database.windows.net.
+ **Senha (-P):** senha associada ao usuário.
+ **Servidor (-S):** servidor no formato `<`Nome do Servidor`>`.database.windows.net
+ **Banco de dados (-D):** nome do banco de dados.
+ **Habilitar Identificadores entre Aspas (-I):** os identificadores entre aspas devem ser habilitados para se conectarem a uma instância do SQL Data Warehouse.

Portanto, para se conectar a uma instância do SQL Data Warehouse, insira o seguinte:

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

Após a conexão, você pode executar quaisquer instruções Transact-SQL compatíveis com a instância. Por exemplo, as instruções abaixo aproveitam a instrução [CREATE TABLE](https://msdn.microsoft.com/library/azure/dn268335.aspx) para criar uma nova tabela.

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> CREATE TABLE table1 (Col1 int, Col2 varchar(20));
2> GO
3> QUIT
```

Para saber mais sobre sqlcmd, consulte a [documentação do sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx).

<!--NOTE: SQL DB does not support the -z/-Z parameters for changing users password with SQLCMD.  Do we support this? -->

## Executar consultas de exemplo ##

Agora que registramos o nosso servidor, vamos prosseguir e criar uma consulta.

1. Clique no banco de dados de usuário no SSDT.

2. Clique no botão **Nova Consulta**. Uma nova janela é aberta.

3. Escreva uma consulta. Digite o seguinte código na janela de consulta:

	```
	SELECT  COUNT(*)
	FROM    sys.tables;
	```

4. Execute a consulta.

	Para executar a consulta, clique na seta verde ou use este atalho: `CTRL`+`SHIFT`+`F5`:

## Próximas etapas ##
Agora que é possível conectar-se e consultar, tente [carregar dados do exemplo][] ou [desenvolver código][].

[carregar dados do exemplo]: ./sql-data-warehouse-get-started-load-samples.md
[desenvolver código]: ./articles/sql-data-warehouse-overview-develop/

<!---HONumber=Oct15_HO1-->