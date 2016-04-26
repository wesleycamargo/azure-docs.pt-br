<properties
   pageTitle="Catálogo de Dados do Azure introduzido com o catálogo de dados | Microsoft Azure"
   description="Tutorial completo apresentando os cenários e os recursos do Catálogo de Dados do Azure."
   documentationCenter=""
   services="data-catalog"
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="04/15/2016"
   ms.author="derrickv"/>

# Introdução ao Catálogo de Dados do Azure

Este artigo é uma visão geral de ponta a ponta dos cenários e recursos do **Catálogo de Dados do Azure**. Depois que você se inscrever para o serviço, siga estas etapas para criar um Catálogo de Dados, registrar, anotar e descobrir fontes de dados.

## Pré-requisitos do tutorial

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure** - Se você não tiver uma, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Consulte [Avaliação Gratuita](https://azure.microsoft.com/pricing/free-trial/) para obter detalhes.
- **Active Directory do Azure** - O Catálogo de Dados do Azure usa o [Active Directory do Azure](https://azure.microsoft.com/services/active-directory/) para gerenciar identidades e acesso.
- **Fontes de dados** - o Catálogo de Dados do Azure tem recursos para descobrir fontes de dados. Este tutorial usa o banco de dados de exemplo do Adventure Works, porém você pode usar qualquer fonte de dados com suporte se preferir trabalhar com dados familiares e relevantes para sua função. Para obter uma lista das fontes de dados com suporte, confira [Fontes de Dados Com Suporte](data-catalog-dsr.md).

> [AZURE.NOTE] Para saber mais sobre as assinaturas do Azure e o Azure Active Directory, confira [Pré-requisitos do Catálogo de Dados do Azure](data-catalog-prerequisites.md).

Vamos começar a instalar o banco de dados de exemplo do Adventure Works.

## Exercício 1: instalar o banco de dados de exemplo do Adventure Works

Nesse exercício, você instalará a amostra do Adventure Works para o Mecanismo de Banco de Dados do SQL Server que é usado nos exercícios seguintes.

### Instalar o banco de dados do Adventure Works 2014 OLTP

O banco de dados do Adventure Works dá suporte a cenários de processamento de transações online padrão para um fabricante de bicicletas fictício (Adventure Works Cycles) que inclui Produtos, Vendas e Compras. Neste tutorial, você registra informações sobre produtos no **Catálogo de Dados do Azure**.

Aqui está como instalar o banco de dados de amostra do Adventure Works.

Para instalar o banco de dados de amostra do Adventure Works, você pode restaurar um backup de AdventureWorks2014 localizado em [Adventure Works 2014 Full Database backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) no CodePlex. Uma maneira de restaurar o banco de dados é executar um script T-SQL no SQL Server Management Studio.

**Instalar o banco de dados de amostra do Adventure Works**

1.	Crie uma pasta chamada C:\\DataCatalog\_GetStarted. Se você usar outro nome de pasta, verifique que você também altera o caminho no script T-SQL abaixo.
2.	Baixe [Adventure Works 2014 Full Database backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661).
3.	Extraia o Adventure Works 2014 Full Database backup.zip para C:\\DataCatalog\_GetStarted. O script abaixo pressupõe que o arquivo de backup está localizado em C:\\DataCatalog\_GetStarted\\Adventure Works 2014 Full Database Backup\\AdventureWorks2014.bak.
4.	No **SQL Server Management Studio**, na barra de ferramentas **Standard**, clique em **Nova Consulta**.
5.	Execute o seguinte código T-SQL na janela de consultas.

**Execute este script para instalar o banco de dados do Adventure Works 2014**

    USE [master]
    GO
    -- NOTE: This script is for sample purposes only. The default backup file path for this script is C:\DataCatalog_GetStarted. To run this script, create the default file path or change the file path, and copy AdventureWorks2014.bak into the path.

    -- IMPORTANT: In a production application, restore a SQL database to the data folder for your SQL Server instance.

    RESTORE DATABASE AdventureWorks2014
    	-- AdventureWorks2014.bak file location
    	FROM disk = 'C:\DataCatalog_GetStarted\Adventure Works 2014 Full Database Backup\AdventureWorks2014.bak'

    	-- AdventureWorks2014.mdf database location
    	WITH MOVE 'AdventureWorks2014_data' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.mdf',

    	-- AdventureWorks2014.ldf log location
    	MOVE 'AdventureWorks2014_Log' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.ldf'
    ,REPLACE
    GO

Como alternativa à execução de um script T-SQL, você pode restaurar o banco de dados usando o SQL Server Management Studio. Confira [Restaurar um Backup de Banco de Dados (SQL Server Management Studio)](http://msdn.microsoft.com/library/ms177429.aspx).

Nesse exercício, você instalou o banco de dados de amostra do Adventure Works que é usado nos exercícios restantes. No exercício, você aprende a registrar fontes de dados do **Catálogo de Dados do Azure** de tabelas no banco de dados de amostra do Adventure Works.

## Exercício 2: registrar fontes de dados

Nesse exercício, você usa a ferramenta de registro do **Catálogo de Dados do Azure** para registrar suas fontes de dados do Adventure Works com o catálogo. O registro é o processo de extração de metadados estruturais chave, como nomes, tipos e locais, da fonte de dados e dos ativos que ela contém, copiando os metadados para o catálogo. As fontes de dados e seus dados permanecem onde estão, porém os metadados são usados pelo catálogo para torná-los mais facilmente identificáveis e compreensíveis.

### Veja a seguir como registrar uma fonte de dados

1.	Vá para https://azure.microsoft.com/services/data-catalog e clique em **Introdução**.
2.	Faça logon no portal do **Catálogo de Dados do Azure** e clique em **Publicar dados**.

    ![](media/data-catalog-get-started/data-catalog-publish-data.png)

3.	Clique em **Abrir aplicativo**.

    ![](media/data-catalog-get-started/data-catalog-launch-application.png)

4. Na página de **Boas-vindas**, clique em **Entrar** e insira suas credenciais.
5. Na página **Catálogo de Dados do Microsoft Azure**, clique duas vezes em **SQL Server** ou clique em **SQL Server** e **Próximo**.

    ![](media/data-catalog-get-started/data-catalog-data-sources.png)

6.	Insira as propriedades de conexão do SQL Server para o AdventureWorks2014 (veja o exemplo abaixo) e clique em **CONECTAR**.

    ![](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

7.	Você poderá registrar os metadados da fonte de dados na próxima página. Nesse exemplo, você registra os objetos **Produção/Produto** do namespace de produção do AdventureWorks. Veja como fazê-lo:

    a. Na árvore de **Hierarquia de Servidores**, clique em **Produção**.

    ![](media/data-catalog-get-started/data-catalog-server-hierarchy.png)

    b. Ctrl+clique em Produto, ProductCategory, ProductDescription e ProductPhoto.

    c. Clique na seta de movimentação selecionada (**>**). Isso moverá todos os objetos de Produto para a lista de **Objetos para registrar**.

    ![](media/data-catalog-get-started/data-catalog-available-objects.png)

    d. Em **Adicionar marcas**, insira uma descrição, uma foto. Isso vai adicionar marcas de pesquisa para esses ativos de dados. Marcas são uma ótima maneira de ajudar os usuários a localizar uma fonte de dados registrada.

    ![](media/data-catalog-get-started/data-catalog-objects-register.png)

    e. **Opcional**: você pode **Incluir uma Visualização** e **Adicionar um especialista em fonte de dados**.

    f. Clique em **REGISTRAR**. O **Catálogo de Dados do Azure** registra os objetos selecionados. Neste exercício, os objetos selecionados do Adventure Works são registrados.

    g. Para ver seus objetos de fonte de dados registrados, clique em **Exibir Portal**. No portal do **Catálogo de Dados do Azure**, você pode exibir objetos de fontes de dados em **Blocos** ou em uma **Lista**.

    ![](media/data-catalog-get-started/data-catalog-view-portal.png)

Neste exercício, você registrou objetos do banco de dados de exemplo do Adventure Works para que eles possam ser facilmente descobertos por usuários na sua organização. No próximo exercício, você aprenderá como descobrir ativos de dados registrados.

## Exercício 3: descobrir ativos de dados registrados

Neste exercício, você usará o portal do **Catálogo de Dados do Azure** para descobrir os ativos de dados registrados e exibir seus metadados. O **Catálogo de Dados do Azure** oferece várias ferramentas para descobrir ativos de dados, inclusive pesquisa por palavra-chave simples, filtros interativos e uma sintaxe de pesquisa avançada para usuários "avançados".

### Veja a seguir como descobrir ativos de dados registrados

O **Catálogo de Dados do Azure** tem uma sintaxe de pesquisa simples, mas avançada, que permite criar com facilidade consultas que retornam os dados de que os usuários precisam. Para obter detalhes sobre o **Catálogo de Dados do Azure**, confira a [Referência de sintaxe de pesquisa](https://msdn.microsoft.com/library/azure/mt267594.aspx).

O **Catálogo de Dados do Azure** tem as seguintes opções de pesquisa:

- Pesquisa por palavra-chave
- Filtro
- Pesquisa avançada

Você também pode refinar quais ativos de dados devem ser vistos. O **Catálogo de Dados do Azure** tem as seguintes opções de exibição:

- Exibir propriedades
- Exibir colunas
- Exibir visualização

Para este exemplo, você usará uma pesquisa por palavra-chave. A pesquisa do **Catálogo de Dados do Azure** tem várias técnicas de consulta. Este exemplo usará uma consulta de pesquisa de **Agrupamento**.

**Técnicas de consulta**

|Técnica|Uso|Exemplo
|---|---|---
|Escopo de propriedade|Retornar apenas as fontes de dados em que o termo de pesquisa corresponda à propriedade especificada|name:product
|Operadores lógicos|Ampliar ou restringir uma pesquisa usando operações boolianas, como descrito na seção Operadores Boolianos nesta página|finanças NOT corporativo
|Agrupando com parênteses|Usar parênteses para agrupar partes da consulta para obter o isolamento lógico, principalmente em conjunto com operadores boolianos|name:product AND (tags:illustration OR tags:photo)
|Operadores de comparação|Usar comparações que não sejam de igualdade para propriedades que tenham tipos de dados numéricos e de data|creationTime:>11/05/14

Neste exemplo, você fará uma pesquisa de **Agrupamento** para ativos de dados nos quais o nome é igual a produtos e as marcas são iguais à ilustração ou as marcas são iguais à foto.

1. Vá para https://azure.microsoft.com/services/data-catalog, clique em **Introdução** e faça logon no portal do **Catálogo de Dados do Azure**.
2. Na caixa **Pesquisar no Catálogo de Dados**, insira uma consulta de **Agrupamento**: (tags:description OR tags:photo).
3. Clique no ícone de pesquisa ou pressione Enter. O **Catálogo de Dados do Azure** exibirá os ativos de dados para esta consulta de pesquisa.

    ![](media/data-catalog-get-started/data-catalog-search-box.png)

Neste exercício, você usou o portal do **Catálogo de Dados do Azure** para descobrir e exibir ativos de dados do Adventure Works registrados no catálogo.

<a name="annotating"/>
## Exercício 4: anotar fontes de dados registradas

Neste exercício, você usa o portal do **Catálogo de Dados do Azure** para anotar os ativos de dados registrados anteriormente no catálogo. As anotações que você fornecer complementarão e aprimorarão os metadados estruturais extraídos da fonte de dados durante o registro, tornando muito mais fácil descobrir e entender os ativos de dados. Como cada usuário do **Catálogo de Dados** pode fornecer suas próprias anotações, será muito fácil para todos os usuários com conhecimento dos dados compartilhá-los com os demais.

### Veja a seguir como anotar os ativos de dados

1. Vá para https://azure.microsoft.com/services/data-catalog, clique em **Introdução** e faça logon no portal do **Catálogo de Dados do Azure**.
2. Clique em **Descobrir**.
3. Escolha um ou mais **Ativos de Dados**. Neste exemplo, escolha **ProductPhoto** e insira "Fotos de produto para materiais de marketing".
4. Insira uma **Descrição** que ajudará outros usuários a descobrir e entender por que e como usar o ativo de dados selecionado. Digite, por exemplo: "Imagens do produto". Você também pode adicionar mais marcas e exibir colunas.
5. Agora você pode tentar pesquisar e filtrar para descobrir os ativos de dados usando os metadados descritivos que você adicionou ao catálogo.

    ![](media/data-catalog-get-started/data-catalog-annotate.png)

Neste exercício, você adicionou informações descritivas aos ativos de dados registrados para que os usuários do catálogo possam descobrir a fonte de dados usando termos que eles entendem.

## Exercício 5: metadados de Crowdsourcing

Neste exercício, você trabalha com outro usuário adicionar metadados a ativos de dados no catálogo. A abordagem de crowdsourcing do **Catálogo de Dados do Azure** quanto a anotações permite que qualquer usuário adicione marcas, descrições e outros metadados, para que qualquer usuário com conhecimento sobre um ativo de dados e seu uso possa capturá-lo e disponibilizá-lo para os demais usuários.

> [AZURE.NOTE] Se você não tiver outro usuário com quem trabalhar neste tutorial, não se preocupe! Qualquer usuário que acessa o catálogo de dados pode adicionar sua própria perspectiva sempre que desejar fazê-lo. Essa abordagem de crowdsourcing dos metadados permite que o conteúdo do catálogo e a riqueza dos metadados do catálogo cresçam com o tempo.

### Veja aqui como fazer crowdsourcing dos metadados dos ativos de dados

Peça a um colega para repetir o exercício [Anotando fontes de dados registradas](#annotating) acima. Depois que seus colegas adicionem descrições a um ativo de dados, como em ProductPhoto, você verá várias anotações.

![](media/data-catalog-get-started/data-catalog-crowdsource.png)

Neste exercício, você explorou os recursos do **Catálogo de Dados do Azure** para crowdsourcing de metadados, nos quais qualquer usuário do catálogo pode anotar os ativos de dados descobertos.

## Exercício 6: conectando-se às fontes de dados

Neste exercício, você usará o portal do **Catálogo de Dados do Azure** para se conectar a uma fonte de dados usando o Microsoft Excel.

> [AZURE.NOTE] É importante lembrar que o **Catálogo de Dados do Azure** não concede aos usuários acesso à fonte de dados real – ele simplesmente facilita sua descoberta e sua compreensão. Quando os usuários se conectam a uma fonte de dados, o aplicativo cliente escolhido usará as credenciais do Windows ou solicitará as credenciais conforme necessário. Se o usuário não tiver recebido acesso à fonte de dados previamente, ele precisará desse acesso para poder conectar.

### Veja como se conectar a uma fonte de dados do Excel

1. Vá para https://azure.microsoft.com/services/data-catalog, clique em **Introdução** e faça logon no portal do **Catálogo de Dados do Azure**.
2. Clique em **Descobrir**.
3. Escolha um ativo de dados. Neste exemplo, escolha ProductCategory.
4. Escolha **Abrir no** > **Excel**.

    ![](media/data-catalog-get-started/data-catalog-connect1.png)

5. Na janela **Aviso de Segurança do Microsoft Excel**, clique em **Habilitar**.
6. Abra o arquivo **ProductCategory.odc**.
7. A fonte de dados é aberta no Excel.

    ![](media/data-catalog-get-started/data-catalog-connect2.png)

Neste exercício, você se conectou às fontes de dados descobertas usando o **Catálogo de Dados do Azure**. O portal do **Catálogo de Dados do Azure** permite que os usuários se conectem diretamente aos aplicativos cliente integrados ao menu **Abrir no...** e que se conectem usando qualquer aplicativo que desejarem usando as informações do local de conexão incluídas nos metadados do ativo.

## Exercício 7: removendo metadados da fonte de dados

Neste exercício, você usará o portal do **Catálogo de Dados do Azure** para remover dados de visualização de ativos de dados registrados e excluir ativos de dados do catálogo.

> [AZURE.NOTE] O comportamento padrão do catálogo é permitir que qualquer usuário registre qualquer fonte de dados, bem como permitir que qualquer usuário exclua qualquer ativo de dados que foi registrado. Os recursos de gerenciamento incluídos na **Edição Standard do Catálogo de Dados do Azure** fornecem opções adicionais para a apropriação dos ativos, a restrição de quem pode descobri-los e a restrição de quem pode excluí-los.

No **Catálogo de Dados do Azure**, você pode eliminar um ativo individual ou excluir vários ativos.

### Veja a seguir como excluir vários ativos de dados

1. Vá para https://azure.microsoft.com/services/data-catalog, clique em **Introdução** e faça logon no portal do **Catálogo de Dados do Azure**.
2. Clique em **Descobrir**.
3. Escolha um ou mais ativos de dados.
4. Clique em **Excluir**.

Neste exercício, você removeu ativos de dados registrado do catálogo.

## Exercício 8: gerenciar fontes de dados registradas

Neste exercício, você usará os recursos de gerenciamento do **Catálogo de Dados do Azure** para se apropriar de ativos de dados e para controlar quais usuários poderão descobrir e como poderão gerenciar os ativos.

> [AZURE.NOTE] Os recursos de gerenciamento descritos neste exercício estão disponíveis apenas na **Edição Standard do Catálogo de Dados do Azure**, não na **Edição Gratuita**. No **Catálogo de Dados do Azure**, você pode apropriar-se dos ativos de dados, adicionar co-proprietários e definir a visibilidade dos ativos de dados.

### Veja aqui como apropriar-se dos ativos de dados e restringir a visibilidade

1. Vá para https://azure.microsoft.com/services/data-catalog, clique em **Introdução** e faça logon no portal do **Catálogo de Dados do Azure**.
2. Clique em **Descobrir**.
3. Escolha um ou mais ativos de dados.
4. No painel **Propriedades**, seção **Gerenciamento**, clique em **Apropriar-se**.
5. Para restringir a visibilidade, clique em **Proprietários e Estes Usuários**.

    ![](media/data-catalog-get-started/data-catalog-ownership.png)

Neste exercício, você explorou os recursos de gerenciamento do **Catálogo de Dados do Azure** e a visibilidade restrita em ativos de dados selecionados.

## Resumo

Neste tutorial, você explorou recursos essenciais do **Catálogo de Dados do Azure**, incluindo o registro, a anotação, a descoberta e o gerenciamento de fontes de dados corporativas. Agora que você concluiu o tutorial, é hora de começar. Você pode começar hoje mesmo registrando as fontes de dados que você e sua equipe precisam e convidando colegas para usar o catálogo.

<!---HONumber=AcomDC_0420_2016-->