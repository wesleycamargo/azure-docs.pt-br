<properties
   pageTitle="Introdução: conecte-se ao SQL Data Warehouse | Microsoft Azure"
   description="Introdução ao SQL Data Warehouse e execução de algumas consultas."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/23/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>
   
# Introdução: conecte-se ao SQL Data Warehouse
Este artigo de início rápido mostra como se conectar e executar consultas a uma instância de provisionamento do Data Warehouse do SQL com duas ferramentas diferentes:

**Visual Studio** – Editor de código e depurador integrado ao Visual Studio, o SQL Server Data Tools (SSDT) é totalmente compatível com o SQL Data Warehouse, permitindo que você se conecte, execute consultas e gerencie facilmente o SQL Data Warehouse.

**sqlcmd** – sqlcmd é uma ferramenta de linha de comando que oferece recursos simples de conexão e consulta.

Depois de concluir este artigo, você terá:

1. Instalado e atualizado o Visual Studio 2013
2. Criado uma conexão com o SQL Data Warehouse no SSDT
3. Executado consultas no banco de dados do SQL Data Warehouse

>[AZURE.NOTE]Supõe-se que você tenha concluído o guia de provisionamento ou tenha um SQL Data Warehouse disponível. Se você não provisionou o Data Warehouse do SQL, consulte novamente o [provisionamento rápido].

## Configurando o Visual Studio para desenvolvimento##
Para o desenvolvimento, a equipe do SQL Data Warehouse recomenda usar o Visual Studio 2013 ou posterior em combinação com o SQL Server Data Tools. A seguir, será descrito como baixar e atualizar o Visual Studio 2013 caso você ainda não tenha uma versão viável do Visual Studio instalada.

Se você estiver procurando mais informações, perguntas gerais do SSDT podem ser resolvidas usando a [documentação completa do SSDT ](https://msdn.microsoft.com/library/azure/hh272686.aspx).

### Obtenha o Visual Studio 2013: ###
vá até o site do Visual Studio 2013 para baixar e instalar uma cópia do Visual Studio. Lembre-se, para o SQL Data Warehouse, qualquer uma das edições gratuitas é mais do que adequada. Fique à vontade para escolher uma que atenda às suas necessidades

<a href="https://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5" target="blank">Obtenha o Visual Studio 2013</a>

### Atualize o Visual Studio 2013 ###
Já tem o Visual Studio 2013 instalado? Ótimo! Para verificar se ele é atualizado, apenas execute as seguintes etapas. Você pode seguir para a próxima etapa.

1. Abra o Visual Studio 2013
2. Escolha o menu "Ferramentas" e selecione "Extensões e atualizações..."
3. Navegue pelo controle em árvore "Atualizações" e "Atualizações do produto"
4. Se não houver uma atualização disponível, você pode fechar a janela "Extensões e atualizações" e seguir para a próxima tarefa deste guia rápido.

No entanto, se houver uma atualização para o Visual Studio, clique no botão Atualizar. Isso iniciará um download da atualização mais recente do Visual Studio 2013.

Feche a janela "Extensões e atualizações" e também o Visual Studio 2013 antes de continuar.

5. Clique no botão "Executar" para instalar a atualização mais recente do Visual Studio 2013.

6. Concorde com os termos de licença e clique no botão Instalar, aceitando qualquer solicitação do Controle de Conta de Usuário (UAC)

7. Clique no botão "Iniciar" para concluir a instalação

Isso conclui a atualização do Visual Studio 2013.

### Atualize o SSDT 
Pode ser que você também precise atualizar o SSDT. Isso é comum. Os engenheiros do SSDT atualizam seu plug-in muito frequentemente para incluir novos recursos, então você notará que é preciso atualizá-lo periodicamente. Novamente, esse é um processo bem simples. Para verificar se você precisa atualizar o SSDT, execute as seguintes etapas:

1. Abra o Visual Studio 2013.  
2. Escolha o menu "Ferramentas" e selecione "Extensões e atualizações..."
3. Navegue pelo controle em árvore "Atualizações" e "Atualizações do produto"
4. Se não houver uma atualização disponível, você pode fechar a janela "Extensões e atualizações" e seguir para a próxima tarefa deste guia rápido.

No entanto, se existir uma atualização chamada "Atualização do Microsoft SQL Server para ferramentas de banco de dados", clique no botão Atualizar.

Isso iniciará um download da versão mais recente do SSDT. A imagem abaixo mostra o SSDTSetup.exe no Gerenciador de downloads do Internet Explorer.

5. Clique no botão "Executar" para instalar a versão mais recente do SSDT.

6. Concorde com os termos de licença e clique no botão Instalar

7. Clique no botão "Fechar" para concluir a atualização do SSDT

8. Feche a janela "Extensões e atualizações"

Agora você tem uma versão atualizada do Visual Studio 2013 na sua área de trabalho com uma extensão SSDT atualizada.

> [AZURE.NOTE]Atualmente, recomendamos o uso do [SSDT Preview para Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=616714&clcid=0x409)

## Conecte-se com o Visual Studio 2013
Se você estiver executando a versão requisitada do Visual Studio, será capaz de se conectar à instância do SQL Data Warehouse de duas maneiras diferentes:

### No Visual Studio
Para fazer a conexão, tudo do que se precisa é abrir o Pesquisador de objetos do SQL Server e passar as informações de conexão

1. Abra o Visual Studio
2. Escolha o menu "Exibir" e selecione "Pesquisador de objetos do SQL Server" na lista suspensa

> [AZURE.NOTE]Certifique-se de escolher o Pesquisador de objetos do SQL Server e ***não*** o Gerenciador de servidores. Os nomes são semelhantes, mas eles são controles muito diferentes. Eles estão localizados próximos um do outro, então tenha cuidado e certifique-se de que você selecionou o correto.

Agora você pode ver o Pesquisador de objetos do SQL Server:


3. Clique no botão "Adicionar servidor" no Pesquisador de objetos do SQL Server. Isso está realçado na imagem abaixo:

4. Preencha a caixa de diálogo Conectar ao servidor

Use os valores que você escolheu ao criar o servidor lógico.

Fique à vontade para marcar a caixa de verificação "Lembrar senha" se desejar. É uma boa economia de tempo, mas lembre-se de que isso permite que qualquer pessoa com acesso físico ao seu perfil realize consultas usando essa conta.

> [AZURE.NOTE]Lembre-se de que o nome do servidor deve ser totalmente qualificado. Portanto, seu valor de Nome de servidor deve seguir este modelo: ***ServerName***.database.windows.net, em que ***ServerName*** é o nome que você atribuiu ao seu servidor lógico.

Clique em Conectar depois de preencher completamente as credenciais

Você acabou de concluir uma conexão e registrar seu servidor lógico SQLDW no Pesquisador de objetos do SQL Server.
    
### No Portal do Azure
Obtenha o Visual Studio diretamente no Portal do Azure.

1. Entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/)
2. Selecione a instância de SQL DW desejada na folha “Procurar”. 
3. Na parte superior da folha de seu SQL DW, selecione “Abrir no Visual...”
4. Se você não tiver configurado o firewall com o IP do computador cliente, selecione “Configurar seu firewall”.
	1. Digite um “Nome da regra”, “IP inicial” e “IP final”. 
	2. Clique em “Salvar”, na parte superior da página.   
5. Clique em “Abrir no Visual Studio”. 
6. O Visual Studio abrirá, e suas credenciais serão solicitadas 
7. Depois de inserir suas credenciais e conectar-se, seu servidor e suas instâncias de DW aparecerão no painel Pesquisador de objetos do SQL Server.  

## Conecte-se ao SQL Data Warehouse com sqlcmd

Você também pode se conectar ao SQL DW com o [sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx) utilitário de prompt de comando que está incluído no SQL Server ou [Utilitários de Linha de Comando 11 da Microsoft para SQL Server](http://go.microsoft.com/fwlink/?LinkId=321501). O utilitário sqlcmd permite inserir instruções Transact-SQL, procedimentos do sistema e arquivos de script no prompt de comando.

Para se conectar a uma instância específica do SQL DW usando o sqlcmd, você precisará abrir o prompt de comando e digitar *sqlcmd* seguido pela cadeia de conexão do banco de dados do SQL DW. A cadeia de conexão deverá conter os seguintes parâmetros:

+ **Usuário (-U):** Usuário do servidor no formato `<`usuário`>`@`<`Nome do servidor`>`.net
+ **Senha (-P):** Senha associada ao usuário
+ **Servidor (-S):** Servidor no formato `<`Nome do servidor`>`.database.windows.net
+ **Banco de dados (-D):** Nome do banco de dados 
+ **Habilitar identificadores entre aspas (-I):** Identificadores entre aspas devem ser habilitados para se conectar a uma instância de SQL DW. 

Portanto, para se conectar a uma instância de SQL DW, digite o seguinte:

```
C:>sqlcmd -U <User>@<Server Name>.database.windows.net -P <Password> -S <Server Name>.database.windows.net -d <Database> -I
```

Após a conexão, você pode executar quaisquer instruções Transact-SQL compatíveis com a instância. Por exemplo, as instruções abaixo aproveitam a instrução [CRIAR TABELA](https://msdn.microsoft.com/library/azure/dn268335.aspx) para criar uma nova tabela

```
C:>sqlcmd -U <User>@<Server Name>.database.windows.net -P <Password> -S <Server Name>.database.windows.net -d <Database> -I
1> CREATE TABLE table1 (Col1 int, Col2 varchar(20));
2> GO
3> QUIT
```
	
Para obter informações adicionais sobre sqlcmd, consulte a [documentação do sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx).

<!--NOTE: SQL DB does not support the -z/-Z parameters for changing users password with SQLCMD.  Do we support this? -->

## Executando consultas de exemplo ##

Agora que registramos o nosso servidor, vamos prosseguir e criar uma consulta.

1. Clique no banco de dados de usuário no SSDT

2. Clique no botão Nova consulta

   Uma nova janela foi aberta

3. Escreva uma consulta

	Digite o seguinte código na janela de consulta:

	```
	SELECT  COUNT(*)
	FROM    sys.tables;
	```

4. Execute a consulta

	Para executar a consulta, clique na seta verde abaixo ou use o atalho a seguir `CTRL`+`SHIFT`+`F5`:

## Próximas etapas ##
[Start developing code]: ./articles/sql-data-warehouse-overview-develop/

<!---HONumber=July15_HO3-->