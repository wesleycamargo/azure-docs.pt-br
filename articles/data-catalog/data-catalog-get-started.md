<properties
   pageTitle="Catálogo de Dados do Azure: Introdução ao catálogo de dados"
   description="Tutorial ponta a ponta dos cenários e recursos do Catálogo de Dados do Azure"
   documentationCenter=""
   services="data-catalog"
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="11/20/2015"
   ms.author="derrickv"/>

# Introdução ao Catálogo de Dados do Azure

Este artigo é um tutorial ponta a ponta dos cenários e recursos da visualização pública do Catálogo de Dados do Azure. Depois que você se inscrever para a visualização, poderá seguir estas etapas para criar um Catálogo de Dados, registrar, anotar e descobrir fontes de dados.

## Pré-requisitos do tutorial

Antes de começar este tutorial, você deve ter o seguinte:

-	**Uma assinatura do Azure** - Se você não tiver uma, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Consulte o artigo [Avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/) para obter detalhes.
-	**Active Directory do Azure** - O Catálogo de Dados do Azure usa o [Active Directory do Azure](http://azure.microsoft.com/services/active-directory/) para gerenciar identidades e acesso.
-	**Fontes de dados** - O Catálogo de Dados do Azure fornece recursos para descoberta de fonte de dados, por isso você deve ter acesso a uma ou mais fontes de dados para prosseguir com o tutorial. O tutorial foi escrito usando os bancos de dados de exemplo do Adventure Works, porém você pode usar qualquer fonte de dados com suporte se preferir trabalhar com dados familiares e relevantes para sua função.

## Exercício 1: instalar o banco de dados de exemplo do Adventure Works

Neste exercício, você instalará exemplo do Adventure Works para o mecanismo de Banco de Dados do SQL Server e SQL Server Analysis Services Multidimensional. Esses exemplos são usados nos exercícios a seguir.

> [AZURE.NOTE]Este exercício é opcional. Os exercícios restantes neste tutorial fazem referência os bancos de dados de exemplo do Adventure Works, mas você pode optar por ignorá-los e trabalhar com suas próprias fontes de dados. Estas são as etapas para instalar o Adventure Works.

### Instalar os bancos de dados Adventure Works 2014 OLTP e Data Warehouse

O banco de dados do Adventure Works OLTP dá suporte a cenários de processamento de transações online padrão para um fabricante de bicicletas fictício (Adventure Works Cycles) incluindo produção, vendas e compras. O banco de dados do Adventure Works DW demonstra como criar um data warehouse.

Os bancos de dados estão localizados em [CodePlex.com](http://msftdbprodsamples.codeplex.com/) e podem ser instalado seguindo as etapas no [Leiame para bancos de dados de exemplo do Adventure Works 2014](https://msftdbprodsamples.codeplex.com/downloads/get/880669).

Neste exercício, você instalou os bancos de dados de exemplo do Adventure Works que são usados nos exercícios restantes. Se você escolher ignorar esse exercício e usar suas próprias fontes de dados da empresa, esteja preparado para lembrar de nomes, marcas e outros metadados.

## Exercício 2: registrar fontes de dados

Neste exercício, você usará a ferramenta de registro do Catálogo de Dados do Azure para registrar suas fontes de dados com o catálogo. O registro é o processo de extração de metadados estruturais chave, como nomes, tipos e locais, da fonte de dados e dos ativos que ela contém, copiando os metadados para o catálogo. As fontes de dados e seus dados permanecem onde estão, porém os metadados são usados pelo catálogo para torná-los mais facilmente identificáveis e compreensíveis.

### Veja a seguir como registrar uma fonte de dados

1.	Faça logon no portal do Catálogo de Dados do Azure.

    ![registrar1][1]

2.	Role para baixo e clique em **Publicar dados**.

    ![registrar2][2]
3.	Clique em **Abrir aplicativo**.
4.	Na página de **Boas-vindas**, clique em **Entrar** e insira suas credenciais.
5.	Na página do **Catálogo de Dados do Microsoft Azure**, clique em **SQL Server**.

    ![registrar3][3]
6.	Insira o **Nome do Servidor** e clique em **Conectar**.
7.	Você poderá registrar os metadados da fonte de dados na próxima página. Neste exemplo, você deve registrar os objetos **Produto** do namespace de produção do AdventureWorks. Veja como fazê-lo:

    a. Na árvore de Hierarquia, clique em **Produção**.

    b. Ctrl+clique em Produto, ProductCategory, ProductDescription e ProductPhoto.

    ![registrar4][4]

    c. Clique na seta de movimentação selecionada (**>**). Isso moverá todos os objetos de Produto para a lista **A ser registrado**.

    ![registrar5][5]

    d. **Opcional**: você pode **Incluir uma Visualização** e **Adicionar um especialista em fonte de dados**.

    e. Em **Adicionar marcas**, insira uma descrição, uma foto. Isso vai adicionar marcas de pesquisa para esses ativos de dados. Marcas são uma ótima maneira de ajudar os usuários a localizar uma fonte de dados registrada.

    f. Clique em **REGISTRAR**. O Catálogo de Dados do Azure registra os objetos selecionados. Neste exercício, os objetos selecionados do Adventure Works são registrados.

    ![registrar6][6]

    g. Para ver seus objetos de fonte de dados registrados, clique em **Exibir Portal**. No portal do Catálogo de Dados do Azure, é possível exibir objetos de fonte de dados em **Blocos** ou em uma **Lista**.

    ![registrar7][7]

Neste exercício, você registrou objetos do banco de dados de exemplo do Adventure Works para que eles possam ser facilmente descobertos por usuários na sua organização. No próximo exercício, você aprenderá como descobrir ativos de dados registrados.

## Exercício 3: descobrir ativos de dados registrados

Neste exercício, você usará o portal do Catálogo de Dados do Azure para descobrir os ativos de dados registrados e exibir seus metadados. O Catálogo de Dados do Azure oferece várias ferramentas para descobrir ativos de dados, incluindo pesquisa por palavra-chave simples, filtros interativos e uma sintaxe de pesquisa avançada para usuários "avançados".

### Veja a seguir como descobrir ativos de dados registrados

O **Catálogo de Dados do Azure** tem uma sintaxe de pesquisa simples, mas avançada, que permite criar com facilidade as consultas que retornam os dados de que os usuários precisam. Para obter detalhes sobre o **Catálogo de Dados do Azure**, consulte a referência de sintaxe da Pesquisa.

O **Catálogo de Dados do Azure** tem as seguintes opções de pesquisa:

- Pesquisa por palavra-chave
- Filtro
- Pesquisa avançada

Você também pode refinar quais ativos de dados devem ser vistos. O **Catálogo de Dados do Azure** tem as seguintes opções de exibição:

- Exibir propriedades
- Exibir colunas
- Exibir visualização

Para este exemplo, você usará uma pesquisa por palavra-chave. A pesquisa do **Catálogo de Dados do Azure** tem várias técnicas de consulta. Este exemplo usará uma consulta de pesquisa de **Agrupamento**.

**Técnicas de Consulta** <table><tr><td><b>Técnica</b></td><td><b>Uso</b></td><td><b>Exemplo</b></td></tr><tr><td>Escopo de Propriedades</td><td>Retorna somente fonte de dados nas quais o termo pesquisado corresponde à propriedade especificada</td><td>name:product</td></tr><tr><td>Operadores Lógicos</td><td>Ampliam ou restringem uma pesquisa usando operações boolianas, conforme descrito na seção Operadores Boolianos nesta página</td><td>finance NOT corporate</td></tr><tr><td>Agrupamento com Parênteses</td><td>Use parênteses para agrupar partes da consulta a fim de obter um isolamento lógico, especialmente ao usar operadores boolianos</td><td>name:product AND (tags:illustration OR tags:photo)</td></tr><tr><td>Operadores de Comparação</td><td>Use outras comparações além da igualdade para propriedades com os tipos de dados numérico e data</td><td>creationTime:>11/05/14</td></tr></table>

Neste exemplo, você fará uma pesquisa de **Agrupamento** para ativos de dados nos quais o nome é igual a produtos e as marcas são iguais à ilustração ou as marcas são iguais à foto.

1.	Faça logon no portal do **Catálogo de Dados do Azure**.
2.	Clique em **Descobrir**.
3.	Na caixa **Pesquisar**, insira uma consulta de **Agrupamento**: (tags:description OR tags:photo).
4.	Clique no ícone de pesquisa ou pressione Enter. O **Catálogo de Dados do Azure** exibirá os ativos de dados para esta consulta de pesquisa.

    ![pesquisar][8]

Neste exercício, você usou o portal do **Catálogo de Dados do Azure** para descobrir e exibir os ativos de dados registrados no catálogo.

## Exercício 4: anotar fontes de dados registradas

Neste exercício, você usará o portal do **Catálogo de Dados do Azure** para anotar os ativos de dados registrados anteriormente no catálogo. As anotações que você fornecer complementarão e aprimorarão os metadados estruturais extraídos da fonte de dados durante o registro, tornando ainda mais fácil descobrir e entender os ativos de dados. Como cada usuário do **Catálogo de Dados** pode fornecer suas próprias anotações, será muito fácil para todos os usuários com conhecimento dos dados compartilhá-los com os demais.

### Veja a seguir como anotar os ativos de dados

1.	Faça logon no portal do **Catálogo de Dados do Azure**.
2.	Clique em **Descobrir**.
3.	Escolha um ou mais **ativos de dados**. Neste exemplo, escolha **ProductPhoto** e insira "Fotos de produto para materiais de marketing".
4.	Insira uma **Descrição** que ajudará outras pessoas a descobrirem e entenderem porque e como usar o ativo de dados selecionado. Digite, por exemplo: "Imagens do produto". Você também pode adicionar mais marcas e exibir colunas.
5.	Agora você pode tentar pesquisar e filtrar para descobrir os ativos de dados usando os metadados descritivos que você adicionou ao catálogo.

    ![anotar][9]

Neste exercício, você adicionou informações descritivas aos ativos de dados registrados para que os usuários do catálogo possam descobrir a fonte de dados usando termos que eles entendem.

## Exercício 5: metadados de Crowdsourcing

Neste exercício, você trabalhará com outro usuário adicionar metadados a ativos de dados no catálogo. A abordagem de crowdsourcing do Catálogo de Dados do Azure quanto a anotações permite que qualquer usuário adicione marcas, descrições e outros metadados, para que qualquer usuário com conhecimento sobre um ativo de dados e seu uso possa capturá-la e disponibilizá-la para os demais usuários.

> [AZURE.NOTE]Se você não tiver outro usuário com quem trabalhar neste tutorial, não se preocupe! Qualquer usuário que acessa o catálogo de dados pode adicionar sua própria perspectiva sempre que desejar fazê-lo. Essa abordagem de crowdsourcing dos metadados permite que o conteúdo do catálogo e a riqueza dos metadados do catálogo cresçam com o tempo.

### Veja aqui como fazer crowdsourcing dos metadados dos ativos de dados

Peça a um colega para repetir o exercício **Anotando fontes de dados registradas** acima. Depois que seu colega adicionar uma descrição a um ativo de dados, como em ProductPhoto, você verá várias anotações.


![crowdsource][13]

Neste exercício, você explorou os recursos do Catálogo de Dados do Azure para crowdsourcing de metadados, nos quis qualquer usuário do catálogo pode anotar os ativos de dados descobertos.


## Exercício 6: conectando-se às fontes de dados

Neste exercício, você usará o portal do **Catálogo de Dados do Azure** para se conectar a fontes de dados usando o Microsoft Excel.


> [AZURE.NOTE]É importante lembrar que o **Catálogo de Dados do Azure** não concede aos usuários acesso à fonte de dados real – ele simplesmente facilita sua descoberta e sua compreensão. Quando os usuários se conectam a uma fonte de dados, o aplicativo cliente escolhido usará as credenciais do Windows ou solicitará as credenciais conforme necessário. Se o usuário não tiver recebido acesso à fonte de dados previamente, ele precisará desse acesso para poder conectar.

### Veja como se conectar a uma fonte de dados do Excel

1.	Faça logon no portal do **Catálogo de Dados do Azure**.
2.	Clique em **Descobrir**.
3.	Escolha um ativo de dados. Neste exemplo, escolha ProductCategory.
4.	Escolha **Abrir no** > **Excel**.

    ![conectar1][10]
5.	Na janela **Aviso de Segurança do Microsoft Excel**, clique em **Habilitar**.
6.	Abra o arquivo **ProductCategory.odc**.
7.	A fonte de dados é aberta no Excel.

    ![conectar2][11]

Neste exercício, você se conectou às fontes de dados descobertas usando o Catálogo de Dados do Azure. O portal do **Catálogo de Dados do Azure** permite que os usuários se conectem diretamente aos aplicativos cliente integrados ao menu **Abrir no...** e que se conectem usando qualquer aplicativo que desejarem usando as informações do local de conexão incluídas nos metadados do ativo.

## Exercício 7: removendo metadados da fonte de dados

Neste exercício, você usará o portal do **Catálogo de Dados do Azure** para remover dados de visualização de ativos de dados registrados e excluir ativos de dados do catálogo.

> [AZURE.NOTE]O comportamento padrão do catálogo é permitir que qualquer usuário registre qualquer fonte de dados, bem como permitir que qualquer usuário exclua qualquer ativo de dados que foi registrado. Os recursos de gerenciamento incluídos na **Edição Standard do Catálogo de Dados do Azure** fornecem opções adicionais para a apropriação dos ativos, a restrição de quem pode descobri-los e a restrição de quem pode excluí-los.

No **Catálogo de Dados do Azure**, você pode remover a visualização do ativo individual ou excluir vários ativos.

### Veja a seguir como excluir vários ativos de dados

1.	Faça logon no portal do **Catálogo de Dados do Azure**.
2.	Clique em **Descobrir**.
3.	Escolha um ou mais ativos de dados.
4.	Clique em **Excluir**.

Neste exercício, você removeu ativos de dados registrado do catálogo.

## Exercício 8: gerenciar fontes de dados registradas

Neste exercício, você usará os recursos de gerenciamento do **Catálogo de Dados do Azure** para se apropriar de ativos de dados e para controlar quais usuários poderão descobrir e gerenciar os ativos.

Observação: os recursos de gerenciamento descritos neste exercício estão disponíveis apenas na Edição Standard do Catálogo de Dados do Azure e não na Edição Gratuita. No **Catálogo de Dados do Azure**, você pode se apropriar de ativos de dados, adicionar coproprietários para ativos de dados e definir a visibilidade dos ativos de dados.

### Veja aqui como apropriar-se dos ativos de dados e restringir a visibilidade

1.	Faça logon no portal do **Catálogo de Dados do Azure**.
2.	Clique em **Descobrir**.
3.	Escolha um ou mais ativos de dados.
4.	No painel **Propriedades**, seção **Gerenciamento**, clique em **Apropriar-se**.
5.	Para restringir a visibilidade, clique em **Proprietários e Estes Usuários**.

    ![propriedade][12]

Neste exercício, você explorou os recursos de gerenciamento do catálogo e a visibilidade restrita em ativos de dados selecionados.

## Resumo

Neste tutorial, você explorou recursos essenciais da visualização do **Catálogo de Dados do Azure**, incluindo o registro, a anotação, a descoberta e o gerenciamento de fontes de dados corporativas. Agora que você concluiu o tutorial, é hora de começar. Você pode começar hoje mesmo registrando as fontes de dados que você e sua equipe precisam e convidando colegas para usar o catálogo.


<!--Image references-->
[1]: ./media/data-catalog-get-started/register1.png
[2]: ./media/data-catalog-get-started/register2.png
[3]: ./media/data-catalog-get-started/register3.png
[4]: ./media/data-catalog-get-started/register4.png
[5]: ./media/data-catalog-get-started/register5.png
[6]: ./media/data-catalog-get-started/register6.png
[7]: ./media/data-catalog-get-started/register7.png
[8]: ./media/data-catalog-get-started/search.png
[9]: ./media/data-catalog-get-started/annotate.png
[10]: ./media/data-catalog-get-started/connect1.png
[11]: ./media/data-catalog-get-started/connect2.png
[12]: ./media/data-catalog-get-started/ownership.png
[13]: ./media/data-catalog-get-started/crowdsource.png

<!---HONumber=Nov15_HO4-->