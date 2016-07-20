<properties
	pageTitle="Introdução ao Catálogo de Dados | Microsoft Azure"
	description="Tutorial completo apresentando os cenários e os recursos do Catálogo de Dados do Azure."
	documentationCenter=""
	services="data-catalog"
	authors="steelanddata"
	manager=""
	editor=""
	tags=""/>
<tags
	ms.service="data-catalog"
	ms.devlang="NA"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-catalog"
	ms.date="07/06/2016"
	ms.author="spelluru"/>

# Tutorial: introdução ao Catálogo de Dados do Azure
O Catálogo de Dados do Azure é um serviço de nuvem totalmente gerenciado que atua como um sistema de registro e sistema de descoberta em ativos de dados da empresa. Para obter uma visão detalhada, confira [O que é o Catálogo de Dados do Azure](data-catalog-what-is-data-catalog.md).

Este tutorial ajudará você a começar a usar o Catálogo de Dados do Azure. Neste tutorial, você realizará as seguintes etapas:

| Etapa | Descrição |
| :--- | :---------- |
| [Provisionar catálogo de dados](#provision-data-catalog) | Nesta etapa, você irá provisionar/configurar o Catálogo de Dados do Azure. Siga esta etapa somente se o catálogo não foi configurado anteriormente. Você só pode ter um catálogo de dados por organização (Domínio do Azure Active Directory), mesmo que haja várias assinaturas associadas à sua conta do Azure. | 
| [Registrar ativos de dados](#register-data-assets) | Nesta etapa, você registrará os ativos de dados do banco de dados de amostra do AdventureWorks2014 com o catálogo de dados. O registro é o processo de extração de metadados estruturais chave, como nomes, tipos e locais, da fonte de dados, copiando os metadados para o catálogo. As fontes de dados e os ativos de dados permanecem onde estão, mas os metadados são usados pelo catálogo para torná-los mais facilmente identificáveis e compreensíveis. |
| [Descobrir ativos de dados](#discover-data-assets) | Nesta etapa, você usará o portal do Catálogo de Dados do Azure para descobrir os ativos de dados que foram registrados na etapa anterior. Depois que uma fonte de dados é registrada com o Catálogo de Dados do Azure, seus metadados são indexados pelo serviço para que os usuários possam pesquisar facilmente e descobrir os dados de que precisam. |
| [Anotar ativos de dados](#annotate-data-assets) | Nesta etapa, você fornecerá anotações (descrições, marcas, documentação, especialistas, etc.) para os ativos de dados a fim de complementar os metadados extraídos da fonte de dados e para tornar a fonte de dados mais compreensível para mais pessoas. | 
| [Conectar aos ativos de dados](#connect-to-data-assets) | Nesta etapa, você abrirá os ativos de dados em ferramentas de cliente integradas, como Excel e ferramentas de dados do SQL Server, e em ferramentas não integradas (SQL Server Management Studio) usando informações de conexão. |
| [Gerenciar ativos de dados](#manage-data-assets) | Nesta etapa, você verá como configurar a segurança para seus ativos de dados. O Catálogo de Dados não dá aos usuários acesso aos dados em si. O acesso aos dados é controlado pelo proprietário da fonte de dados. <br/><br/>O Catálogo de Dados permite que os usuários descubram fontes de dados e exibam os **metadados** relacionados às fontes registradas no catálogo. Porém, pode haver situações em que as fontes de dados só devam estar visíveis para usuários específicos ou membros de grupos. Nesses cenários, o Catálogo de Dados permite que os usuários se apropriem de ativos de dados registrados no catálogo e, em seguida, controlem a visibilidade dos ativos de propriedade sua. | 
| [Remover ativos de dados](#remove-data-assets) | Nesta etapa, você aprenderá a remover ativos de dados de catálogo de dados. |  
 
## Pré-requisitos do tutorial

Antes de começar este tutorial, você deve ter o seguinte:

### Assinatura do Azure
Para configurar o Catálogo de Dados do Azure, você precisa ser **proprietário ou coproprietário** de uma assinatura do Azure.

As assinaturas do Azure ajudam a organizar o acesso aos recursos de serviço de nuvem como o Catálogo de Dados do Azure. Eles também ajudam a controlar como o uso de recursos é reportado, cobrado e pago. Cada assinatura pode ter uma configuração diferente de cobrança e pagamento, assim você pode ter diferentes assinaturas e planos diferentes por departamento, projeto, escritório regional, etc. Cada serviço de nuvem pertence a uma assinatura, e você precisa ter uma assinatura antes de configurar o Catálogo de Dados do Azure. Para saber mais, consulte [Gerenciar contas, assinaturas e funções administrativas](../active-directory/active-directory-how-subscriptions-associated-directory.md).

Se você não tiver uma assinatura, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Confira [Avaliação Gratuita](https://azure.microsoft.com/pricing/free-trial/) para obter detalhes.

### Active Directory do Azure
Para configurar o Catálogo de Dados do Azure, você deve estar conectado usando uma **conta de usuário do Azure Active Directory** e o usuário deve ser o proprietário ou coproprietário de uma assinatura do Azure.

O Active Directory do Azure (AD do Azure) fornece uma maneira fácil para a sua empresa gerenciar identidades e acesso, tanto na nuvem quanto local. Os usuários podem usar uma única conta de trabalho ou da escola para logon único em qualquer nuvem e aplicativo Web local. O Catálogo de Dados do Azure usa o AD do Azure para autenticar o logon. Para saber mais, confira [O que é o Azure Active Directory](../active-directory/active-directory-whatis.md).

### Configuração de política do Active Directory

Em algumas situações, os usuários podem encontrar uma situação em que podem acessar o portal do Catálogo de Dados do Azure, mas quando tentam fazer logon na ferramenta de registro da fonte de dados encontram uma mensagem de erro que impede o logon. Esse comportamento de problema pode ocorrer apenas quando o usuário está na rede da empresa, ou quando está se conectando de fora da rede da empresa.

A ferramenta de registro usa a **Autenticação de Formulários** para validar logons de usuário no Active Directory. Para um logon bem-sucedido, a autenticação de formulários deve ser habilitada na **Política de Autenticação Global** por um administrador do Active Directory.

A Política de Autenticação Global permite que os métodos de autenticação sejam habilitados separadamente para conexões intranet e extranet, conforme ilustrado abaixo. Erros de logon poderão ocorrer se a autenticação de formulários não estiver habilitada para a rede por meio da qual o usuário está se conectando.

 ![Política de Autenticação Global do Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Para obter mais informações, consulte [Configurando políticas de autenticação](https://technet.microsoft.com/library/dn486781.aspx).

## Provisionar catálogo de dados
Você somente pode provisionar um catálogo de dados por organização (domínio do Azure Active Directory). Portanto, se o proprietário ou coproprietário de uma assinatura do Azure que pertence a esse domínio do Active Directory já tiver criado um catálogo, você não poderá criar um catálogo novamente, mesmo se tiver várias assinaturas do Azure. Para testar se um catálogo de dados foi criado por um usuário no domínio do Active Directory, navegue até http://azuredatacatalog.com e verifique se você vê o catálogo. Ignore o procedimento a seguir e vá para a próxima seção se um catálogo já foi criado para você.

1. Navegue até [https://azure.microsoft.com/services/data-catalog](https://azure.microsoft.com/services/data-catalog).

	![Catálogo de Dados do Azure - página inicial de marketing](media/data-catalog-get-started/data-catalog-marketing-landing-page.png) e clique em **Introdução**.
2. Faça logon usando uma conta de usuário que seja **proprietário ou coproprietário** de uma assinatura do Azure. Você verá a página a seguir depois de fazer logon com êxito.

	![Catálogo de Dados do Azure - provisionar catálogo de dados](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)
2. Especifique um **nome** para o catálogo de dados, a **assinatura** você deseja usar e o **local** do catálogo.
3. Expanda **Preços** e especifique a **edição** (gratuita e standard) do Catálogo de Dados do Azure. ![Catálogo de Dados do Azure - selecionar edição](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)
4. Expanda **Usuários do Catálogo** e clique em **Adicionar** para adicionar usuários ao catálogo de dados. Você é adicionado automaticamente a esse grupo. ![Catálogo de Dados do Azure - usuários](media/data-catalog-get-started/data-catalog-add-catalog-user.png)
5. Expanda **Administradores do Catálogo** e clique em **Adicionar** para adicionar outros administradores ao catálogo de dados. Você é adicionado automaticamente a esse grupo. ![Catálogo de Dados do Azure - administradores](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)
6. Clique no botão **Criar Catálogo** para criar o catálogo de dados da sua organização. Você verá a página inicial do catálogo de dados depois que ele for criado com êxito. ![Catálogo de Dados do Azure - criado](media/data-catalog-get-started/data-catalog-created.png)

### Encontrar um catálogo de dados no Portal do Azure
1. Em uma guia separada no navegador da Web ou em uma janela de navegador da Web separada, navegue até [https://portal.azure.com](https://portal.azure.com) e faça logon usando a mesma conta que você usou para criar o catálogo de dados na etapa anterior.
2. Clique em **Procurar** e clique em **Catálogo de Dados**.

	![Catálogo de Dados do Azure - procurar no portal do azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)
3. Você deve ver o catálogo de dados que criou.

	![Catálogo de Dados do Azure - exibir catálogo em lista](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)
4.  Clique no catálogo que você criou e provavelmente verá a folha **Catálogo de Dados** no portal.

	![Catálogo de Dados do Azure - folha no portal](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)
5. Você pode exibir as propriedades do catálogo de dados e atualizá-las. Por exemplo, clique em **Tipo de preço** e altere a edição.

	![Catálogo de Dados do Azure - tipo de preço](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

### Banco de dados de exemplo do Adventure Works 
Neste tutorial, você registrará os ativos de dados (tabelas) do banco de dados de exemplo do AdventureWorks2014 para o mecanismo de banco de dados do SQL Server, mas poderá usar qualquer fonte de dados com suporte se preferir trabalhar com dados que são familiares e relevantes para a sua função. Para obter uma lista das fontes de dados com suporte, confira [Fontes de dados com suporte](data-catalog-dsr.md).

### Instalar o banco de dados do Adventure Works 2014 OLTP
O banco de dados do Adventure Works dá suporte a cenários de processamento de transações online padrão para um fabricante de bicicletas fictício (Adventure Works Cycles) que inclui Produtos, Vendas e Compras. Neste tutorial, você registra informações sobre produtos no **Catálogo de Dados do Azure**.

Veja como instalar o banco de dados de amostra do Adventure Works:

1. Baixe [Adventure Works 2014 Full Database backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) no CodePlex.
2. Siga as instruções neste artigo: [Restaurar um Backup de Banco de Dados usando o SQL Server Management Studio](http://msdn.microsoft.com/library/ms177429.aspx) para restaurar o banco de dados em seu computador. **Etapas rápidas**:
	1. Inicie o SQL Server Management Studio e conecte-se ao mecanismo de banco de dados do SQL Server.
	2. Clique com botão direito do mouse em **Bancos de Dados** e selecione **Restaurar Banco de Dados**.
	3. Na caixa de diálogo **Restaurar Banco de Dados**, selecione a opção de **Dispositivo** como **Fonte** e clique em **Procurar (...)**.
	4. Na caixa de diálogo **Selecione dispositivos de backup**, clique em **Adicionar**.
	5. Navegue até a pasta em que está o arquivo **Adventureworks2014**, selecione o arquivo e clique em **OK** para fechar a caixa de diálogo **Localizar Arquivo de Backup**.
	6. Clique em **OK** para fechar a caixa de diálogo **Selecionar Dispositivos de Backup**.
	7. Clique em **OK** para fechar a caixa de diálogo **Restaurar Banco de Dados**.

Agora, vamos ver como registrar os ativos de dados do banco de dados de exemplo do Adventure Works com o **Catálogo de Dados do Azure**.

## Registrar ativos de dados

Neste exercício, você usará a ferramenta de registro para registrar os ativos de dados do banco de dados do Adventure Works no catálogo. O registro é o processo de extração de metadados estruturais chave, como nomes, tipos e locais, da fonte de dados e dos ativos que ela contém, copiando os metadados para o catálogo. As fontes de dados e seus dados permanecem onde estão, porém os metadados são usados pelo catálogo para torná-los mais facilmente identificáveis e compreensíveis.

### Veja a seguir como registrar uma fonte de dados

1.	Navegue até [https://azuredatacatalog.com](https://azuredatacatlog.com) e clique em **Publicar dados** na home page.

    ![Catálogo de Dados do Azure - botão Publicar Dados](media/data-catalog-get-started/data-catalog-publish-data.png)

3.	Clique em **Iniciar Aplicativo** para baixar, instalar e executar a **ferramenta de registro** em seu computador.

    ![Catálogo de Dados do Azure - botão Iniciar](media/data-catalog-get-started/data-catalog-launch-application.png)

4. Na página de **Boas-vindas**, clique em **Entrar** e insira suas credenciais.

	![Catálogo de Dados do Azure - caixa de diálogo de boas-vindas](media/data-catalog-get-started/data-catalog-welcome-dialog.png)

5. Na página **Catálogo de Dados do Microsoft Azure**, clique duas vezes em **SQL Server** ou clique em **SQL Server** e **Avançar**.

    ![Catálogo de Dados do Azure - fontes de dados](media/data-catalog-get-started/data-catalog-data-sources.png)

6.	Insira as propriedades de conexão do SQL Server para o **AdventureWorks2014** (veja o exemplo abaixo) e clique em **CONECTAR**.

    ![Catálogo de Dados do Azure - configurações de conexão do SQL Server](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

7.	Você poderá registrar os metadados do ativo de dados na próxima página. Nesse exemplo, você registra os objetos **Produção/Produto** do namespace de produção do AdventureWorks. Veja como fazê-lo:
    
	1. Na árvore **Hierarquia de Servidor**, expanda **AdventureWorks2014** e clique em **Produção**.
	2. Ctrl+clique em **Product**, **ProductCategory**, **ProductDescription** e **ProductPhoto**.
	3. Clique na **seta de movimentação selecionada** (**>**). Isso moverá todos os objetos de Produto para a lista **Objetos para registrar**.
			
    	![Tutorial do Catálogo de Dados do Azure - procurar e selecionar objetos](media/data-catalog-get-started/data-catalog-server-hierarchy.png)
	4. Selecione **Incluir uma Visualização** para incluir uma visualização dos dados como instantâneo. O instantâneo inclui até 20 registros de cada tabela e é copiado no catálogo.
	5. Selecione **Incluir Perfil de Dados** para incluir um instantâneo das estatísticas de objeto para o perfil de dados (por exemplo, valores mínimo, máximo e médio de uma coluna, número de linhas, etc.).
	5. Em **Adicionar marcas**, digite **adventure works, ciclos**. Isso vai adicionar marcas de pesquisa para esses ativos de dados. Marcas são uma ótima maneira de ajudar os usuários a localizar uma fonte de dados registrada.
	6. (opcional) Especifique o nome de um **especialista** nesses dados.
	
    	![Tutorial do Catálogo de Dados do Azure - objetos para registrar](media/data-catalog-get-started/data-catalog-objects-register.png)
    
	7. Clique em **REGISTRAR**. O Catálogo de Dados do Azure registra os objetos selecionados. Neste exercício, os objetos selecionados do Adventure Works são registrados. A ferramenta de registro extrai metadados de ativo de dados e copia esses dados no serviço Catálogo de Dados do Azure. Os dados permanecem onde atualmente residem e permanecem sob o controle dos administradores e políticas do sistema atual.
	
		![Catálogo de Dados do Azure - objetos registrados](media/data-catalog-get-started/data-catalog-registered-objects.png)

	8. Para ver seus objetos de fonte de dados registrados, clique em **Exibir Portal**. No portal **Catálogo de Dados do Azure**, confirme se você vê todas as quatro tabelas e o banco de dados no modo de exibição de grade.
 
    	![Objetos no portal do Catálogo de Dados do Azure](media/data-catalog-get-started/data-catalog-view-portal.png)
  
	
Neste exercício, você registrou objetos do banco de dados de exemplo do Adventure Works para que eles possam ser facilmente descobertos por usuários na sua organização. No próximo exercício, você aprenderá como descobrir ativos de dados registrados.

## Descobrir ativos de dados
A descoberta no Catálogo de Dados do Azure usa dois mecanismos principais: pesquisa e filtragem.

A **pesquisa** foi projetada para ser intuitiva e avançada. Por padrão, os termos de pesquisa são comparados a qualquer propriedade do catálogo, inclusive anotações fornecidas pelo usuário.

A **filtragem** foi desenvolvida para complementar a pesquisa. Você pode selecionar características específicas como especialistas, tipo de fonte de dados, tipo de objeto e marcas, para exibir apenas ativos de dados correspondentes e também para restringir os resultados da pesquisa a ativos correspondentes.

Usando uma combinação de pesquisa e filtragem, você pode navegar rapidamente pelas fontes de dados que foram registradas com o Catálogo de Dados do Azure para descobrir os ativos de dados de que precisa.

Neste exercício, você usará o portal do **Catálogo de Dados do Azure** para descobrir os ativos de dados registrados no exercício anterior. Confira [Referência de sintaxe de pesquisa do catálogo de dados](https://msdn.microsoft.com/library/azure/mt267594.aspx) para obter detalhes sobre a sintaxe de pesquisa.

Vejamos alguns exemplos para descobrir ativos de dados no catálogo.

### Pesquisa básica
A pesquisa básica permite que você pesquise o catálogo usando um ou mais termos de pesquisa. Os resultados são quaisquer ativos que correspondam a qualquer propriedade com um ou mais dos termos especificados.

1. Clique no botão **Início** no portal do Catálogo de Dados do Azure. Se você fechou o navegador da Web, navegue até [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com).
2. Na caixa de pesquisa na parte superior, digite **cycles** e clique no ícone **pesquisa** (ou) pressione **ENTER**.
	
	![Catálogo de Dados do Azure - pesquisa de texto básica](media/data-catalog-get-started/data-catalog-basic-text-search.png)
3. Confirme se você vê todas as quatro tabelas e o banco de dados (AdventureWorks2014) nos resultados. Você pode alternar entre **exibição de grade** e **exibição de lista** clicando nos botões na parte superior, como mostrado na imagem a seguir. Observe que a palavra-chave de pesquisa fica realçada nos resultados da pesquisa, já que a opção **Realçar** na parte superior está **ATIVADA**. Você também pode especificar o número de **resultados por página** nos resultados da pesquisa.

	![Catálogo de Dados do Azure - resultados da pesquisa de texto básica](media/data-catalog-get-started/data-catalog-basic-text-search-results.png)
	
	Você verá o painel **Pesquisas** à esquerda e o painel **Propriedades** à direita. O painel Pesquisa permite que você altere os critérios de pesquisa e filtre os resultados. O painel Propriedades exibe as propriedades de um objeto selecionado na grade/lista.

4. Clique em **Produto** nos resultados da pesquisa. Clique nas guias chamadas **Visualização**, **Colunas**, **Perfil de Dados** e **Documentação**, ou use a seta **PARA CIMA** a fim de expandir o painel inferior no meio.
 
	![Catálogo de Dados do Azure - painel inferior](media/data-catalog-get-started/data-catalog-data-asset-preview.png)
	
	Na guia **Visualização**, você deve ver a visualização dos dados na tabela Product.
5. Clique na guia **Colunas** para encontrar detalhes sobre as colunas (como **nome** e **tipo de dados**) no ativo de dados.
6. Clique na guia **Perfil de Dados** para ver a criação de perfil de dados (por exemplo, número de linhas, tamanho dos dados, valor mínimo em uma coluna, etc.) no ativo de dados.
6. Filtrar os resultados usando **Filtros** à esquerda. Por exemplo, clique em **Tabela** para o **Tipo de Objeto** e você verá somente as quatro tabelas, não o banco de dados.

	![Catálogo de Dados do Azure - filtrar resultados da pesquisa](media/data-catalog-get-started/data-catalog-filter-search-results.png)

### Escopo de propriedade
O escopo de propriedade permite que você descubra os ativos de dados onde o termo de pesquisa corresponde à propriedade especificada.

3. Limpe o filtro **Tabela** para o **Tipo de Objeto** em **Filtros**.
4. Na caixa de pesquisa, digite **tags:cycles** e clique no ícone de **pesquisa** (ou) pressione **ENTER**. Confira [Referência de sintaxe de pesquisa do Catálogo de Dados](https://msdn.microsoft.com/library/azure/mt267594.aspx) para ver todas as propriedades que você pode usar para pesquisar o catálogo de dados.
3. Confirme se você vê todas as quatro tabelas e o banco de dados (AdventureWorks2014) nos resultados.

	![Catálogo de Dados - resultados da pesquisa de escopo de propriedade](media/data-catalog-get-started/data-catalog-property-scoping-results.png)

### Salvar a pesquisa 
1. No painel Pesquisas à esquerda, na seção Pesquisa Atual, clique em Salvar para salvar os critérios de pesquisa atuais. Insira um nome para a pesquisa e clique em Salvar.
	
	![Catálogo de Dados do Azure - salvar pesquisa](media/data-catalog-get-started/data-catalog-save-search.png)
2. Confirme se a pesquisa salva aparece em Pesquisas Salvas.

	![Catálogo de Dados do Azure - pesquisas salvas](media/data-catalog-get-started/data-catalog-saved-search.png)
3. Clique na seta para baixo a fim de ver as ações que você pode realizar na pesquisa salva (renomear, excluir, definir como o padrão de pesquisa). ![Catálogo de Dados do Azure - opções de pesquisa salvas](media/data-catalog-get-started/data-catalog-saved-search-options.png)

### Operadores boolianos
Operadores boolianos permitem ampliar ou restringir uma pesquisa.

2. Na caixa de pesquisa, digite **tags:cycles e objectType:table** e clique no ícone **pesquisa** ou pressione **ENTER**.
3. Confirme que você vê somente tabelas, e não o banco de dados, nos resultados.

	![Catálogo de Dados do Azure - operador booliano na pesquisa](media/data-catalog-get-started/data-catalog-search-boolean-operator.png)

### Agrupando com parênteses
O agrupamento com parênteses permite a você usar parênteses para agrupar partes da consulta e obter o isolamento lógico, principalmente em conjunto com operadores boolianos.

1. Na caixa de pesquisa, digite **name:product E objectType:table** e clique no ícone de **pesquisa** ou pressione **ENTER**.
2. Confirme que você vê apenas a tabela **Product** nos resultados da pesquisa.

	![Catálogo de Dados do Azure - pesquisa de agrupamento](media/data-catalog-get-started/data-catalog-grouping-search.png)

### Operadores de comparação
Os operadores de comparação permitem a você usar comparações que não sejam de igualdade para propriedades que tenham tipos de dados numéricos e de data.

1. Na caixa de pesquisa, digite **lastRegisteredTime:>"06/09/2016"**.
2. Desmarque o filtro **Tabela** para o **Tipo de Objeto** à esquerda.
3. Clique no ícone de **pesquisa** ou pressione **ENTER**.
2. Confirme que vê as tabelas Product, ProductCategory, ProductDescription e ProductPhoto e banco de dados do AdventureWorks2014 que você registrou nos resultados da pesquisa.

	![Catálogo de Dados do Azure - resultados da pequisa de comparação](media/data-catalog-get-started/data-catalog-comparison-operator-results.png)

Confira [Como descobrir ativos de dados](data-catalog-how-to-discover.md) para obter informações detalhadas sobre a descoberta de ativos de dados e [Referência de sintaxe de pesquisa do Catálogo de Dados](https://msdn.microsoft.com/library/azure/mt267594.aspx) para obter a sintaxe de pesquisa.

## Anotar ativos de dados
Neste exercício, você deve usar o portal do **Catálogo de Dados do Azure** para anotar (adicionar descrições, marcas, especialistas, etc.) em ativos de dados que você registrou anteriormente no catálogo. As anotações que você fornecer complementarão e aprimorarão os metadados estruturais extraídos da fonte de dados durante o registro, tornando muito mais fácil descobrir e entender os ativos de dados.

### Veja a seguir como anotar os ativos de dados
Nesta etapa, você irá anotar um único conjunto de dados (ProductPhoto). Você adicionará um nome amigável, uma descrição e outros ao ativo de dados ProductPhoto.

1.  Se fechar o navegador, navegue até [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com) e pesquise com **tags:cycles** para encontrar os ativos de dados que você registrou.
2. Clique em **ProductPhoto** nos resultados da pesquisa.
3. Digite **Imagens do produto** como **nome amigável** e **Fotos de produto para materiais de marketing** no campo **Descrição**.

	![Catálogo de Dados do Azure - descrição de Product Photo](media/data-catalog-get-started/data-catalog-productphoto-description.png)

	A **Descrição** ajudará outros usuários a descobrir e entender por que e como usar o ativo de dados selecionado. Você também pode adicionar mais marcas e exibir colunas. Agora você pode tentar pesquisar e filtrar para descobrir os ativos de dados usando os metadados descritivos que você adicionou ao catálogo.

Observe que você também pode fazer o seguinte nesta página:

- Adicione especialistas para o ativo de dados. Clique em **Adicionar...** em **Especialistas:** no painel à direita.
- Adicione marcas no nível do conjunto de dados. Clique em **Adicionar...** em **Marcas:** no painel à direita. Uma marca pode ser uma marca de usuário ou de glossário. A edição Standard do catálogo de dados inclui um glossário de negócios que permite que os administradores de catálogo definam uma taxonomia de negócios central. Os usuários do catálogo podem anotar os ativos de dados com os termos do glossário. Para saber mais, confira [Como configurar o glossário de negócios para Marcação Governada](data-catalog-how-to-business-glossary.md)
- Adicione marcas no nível da coluna. No painel inferior do meio, clique em **Adicionar...** em **Marcas** para a coluna em que você deseja anotar.
- Adicione uma descrição no nível da coluna. No painel inferior do meio, insira a **Descrição** para uma coluna. Você também pode exibir os metadados de descrição extraídos da fonte de dados.
- Adicione informações de **Solicitar acesso** que especificam aos usuários como solicitar acesso ao ativo de dados.

	![Catálogo de Dados do Azure - adicionar marcas, descrições](media/data-catalog-get-started/data-catalog-add-tags-experts-descriptions.png)
  
- Clique na guia **Documentação** no painel inferior do meio e forneça a documentação para o ativo de dados. A documentação do Catálogo de Dados do Azure permite que você use seu Catálogo de Dados como um repositório de conteúdo para criar uma narrativa completa de seus ativos de dados.

	![Catálogo de Dados do Azure - guia Documentação](media/data-catalog-get-started/data-catalog-documentation.png)


Você também pode selecionar vários ou todos e adicionar uma anotação a vários/todos os ativos de dados. Por exemplo, você pode selecionar todos os ativos de dados que registrou e especificar um especialista para eles.

![Catálogo de Dados do Azure - anotar vários ativos de dados](media/data-catalog-get-started/data-catalog-multi-select-annotate.png)

A abordagem de crowdsourcing do Catálogo de Dados do Azure quanto a anotações permite que qualquer usuário do Catálogo de Dados adicione marcas (usuário ou glossário), descrições e outros metadados, para que qualquer usuário com conhecimento sobre um ativo de dados e seu uso possa capturá-lo e disponibilizá-lo para os demais usuários.

Confira [Como anotar os ativos de dados](data-catalog-how-to-annotate.md) para obter informações detalhadas sobre anotar ativos de dados.
 
## Conectar aos ativos de dados
Neste exercício, você abrirá os ativos de dados em uma ferramenta de cliente integrada (Excel) e em uma ferramenta não integrada (SQL Server Management Studio) usando informações de conexão.

> [AZURE.NOTE] É importante lembrar que o **Catálogo de Dados do Azure** não concede aos usuários acesso à fonte de dados real – ele simplesmente facilita sua descoberta e sua compreensão. Quando os usuários se conectam a uma fonte de dados, o aplicativo cliente escolhido usará as credenciais do Windows ou solicitará as credenciais conforme necessário. Se o usuário não tiver recebido acesso à fonte de dados previamente, ele precisará desse acesso para poder conectar.

### Veja como se conectar a um ativo de dados do Excel

1. Selecione **Produto** nos resultados da pesquisa. Clique em **Abrir Em** na barra de ferramentas e selecione **Excel**.
 
    ![Catálogo de Dados do Azure - conectar-se a ativos de dados](media/data-catalog-get-started/data-catalog-connect1.png)
5. Clique em **Abrir** na janela pop-up de download na parte inferior (essa experiência pode variar dependendo do navegador).

	![Catálogo de Dados do Azure - arquivo de conexão do excel baixado](media/data-catalog-get-started/data-catalog-download-open.png)
6. Na janela **Aviso de Segurança do Microsoft Excel**, clique em **Habilitar**.

	![Catálogo de Dados do Azure - pop-up de segurança do excel](media/data-catalog-get-started/data-catalog-excel-security-popup.png)
7. Na caixa de diálogo **Importar Dados**, mantenha os padrões e clique em **OK**.

	![Catálogo de Dados do Azure - importar dados do Excel](media/data-catalog-get-started/data-catalog-excel-import-data.png)
8. A fonte de dados é aberta no Excel.

    ![Catálogo de Dados do Azure - tabela de produto no Excel](media/data-catalog-get-started/data-catalog-connect2.png)

Neste exercício, você se conectou aos ativos de dados descobertos usando o **Catálogo de Dados do Azure**. O portal do **Catálogo de Dados do Azure** permite que os usuários se conectem diretamente aos aplicativos cliente integrados ao menu **Abrir no...** e que se conectem usando qualquer aplicativo que desejarem usando as informações do local de conexão incluídas nos metadados do ativo. Por exemplo: você pode usar o SQL Server Management Studio para se conectar ao banco de dados do AdventureWorks2014 e acessar os dados nos ativos de dados registrados neste tutorial.

1. Inicie o **SQL Server Management Studio**.
2. Na caixa de diálogo **Conectar ao servidor**, digite o **nome do servidor** no painel **Propriedades** no portal do Catálogo de Dados do Azure.
3. Use as devidas **credenciais** e **autenticação** para acessar o ativo de dados se você já tiver acesso ao ativo de dados. Se não tiver o acesso, use as informações no campo Solicitar Acesso para obtê-lo.

	![Catálogo de Dados do Azure - solicitar acesso](media/data-catalog-get-started/data-catalog-request-access.png)

Clique em **Exibir Cadeias de Conexão** para exibir e copiar as cadeias de conexão OLEDB, ODBC e ADF.NET na área de transferência para usar em seu aplicativo.

## Gerenciar ativos de dados
Nesta etapa, você verá como configurar a segurança para seus ativos de dados. O Catálogo de Dados não dá aos usuários acesso aos dados em si. O acesso aos dados é controlado pelo proprietário da fonte de dados.

O Catálogo de Dados permite que os usuários descubram fontes de dados e exibam os **metadados** relacionados às fontes registradas no catálogo. Porém, pode haver situações em que as fontes de dados só devam estar visíveis para usuários específicos ou membros de grupos. Nesses cenários, o Catálogo de Dados permite que os usuários se apropriem de ativos de dados registrados no catálogo e, em seguida, controlem a visibilidade dos ativos de propriedade sua.

> [AZURE.NOTE] Os recursos de gerenciamento descritos neste exercício estão disponíveis apenas na **Edição Standard do Catálogo de Dados do Azure**, não na **Edição Gratuita**. No **Catálogo de Dados do Azure**, você pode apropriar-se dos ativos de dados, adicionar co-proprietários e definir a visibilidade dos ativos de dados.

### Veja aqui como apropriar-se dos ativos de dados e restringir a visibilidade

1. Se você fechou o navegador da Web, navegue até [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com). Na caixa de texto Pesquisa, digite **tags:cycles** e pressione **ENTER**.
3. Selecione um item na lista de resultados, por exemplo, **Produto**, clicando na caixa de seleção no canto superior direito e clicando em **Assumir** na barra de ferramentas, conforme mostrado na imagem a seguir.
4. No painel **Propriedades**, seção **Gerenciamento**, clique em **Assumir** na seção **Gerenciamento** no painel à direita.

	![Catálogo de Dados do Azure - assumir](media/data-catalog-get-started/data-catalog-take-ownership.png)
5. Para restringir a visibilidade, clique em **Proprietários e Esses Usuários** na seção **Visibilidade** e clique em **Adicionar**. Insira o endereço de email do usuário na caixa de texto e pressione ENTER.

    ![Catálogo de Dados do Azure - restringir acesso](media/data-catalog-get-started/data-catalog-ownership.png)

## Remover ativos de dados

Neste exercício, você usará o portal do **Catálogo de Dados do Azure** para remover dados de visualização de ativos de dados registrados e excluir ativos de dados do catálogo.

No **Catálogo de Dados do Azure**, você pode eliminar um ativo individual ou excluir vários ativos.

### Veja como excluir ativos de dados

1. Se você fechou o navegador da Web, navegue até [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com).
2. Na caixa de texto Pesquisa, digite **tags:cycles** e pressione **ENTER**.
3. Selecione um item na lista de resultados, por exemplo, **ProductDescription**, clicando na caixa de seleção no canto superior direito e clicando em **Excluir** na barra de ferramentas, conforme mostrado na imagem a seguir.

	![Catálogo de Dados do Azure - excluir o item de grade](media/data-catalog-get-started/data-catalog-delete-grid-item.png)
	
	Se você estiver usando o modo de exibição de lista (em vez do modo de exibição de grade), a caixa de seleção estará à esquerda do item, conforme mostrado na imagem a seguir.

	![Catálogo de Dados do Azure - excluir o item de lista](media/data-catalog-get-started/data-catalog-delete-list-item.png)

	Você também pode selecionar vários ativos de dados e excluí-los, conforme mostrado abaixo:

	![Catálogo de Dados do Azure - excluir vários ativos de dados](media/data-catalog-get-started/data-catalog-delete-assets.png)


> [AZURE.NOTE] O comportamento padrão do catálogo é permitir que qualquer usuário registre qualquer fonte de dados, bem como permitir que qualquer usuário exclua qualquer ativo de dados que foi registrado. Os recursos de gerenciamento incluídos na **Edição Standard do Catálogo de Dados do Azure** fornecem opções adicionais para a apropriação dos ativos, a restrição de quem pode descobri-los e a restrição de quem pode excluí-los.


## Resumo

Neste tutorial, você explorou recursos essenciais do **Catálogo de Dados do Azure**, incluindo o registro, a anotação, a descoberta e o gerenciamento de ativos de dados corporativos. Agora que você concluiu o tutorial, é hora de começar. Você pode começar hoje mesmo registrando as fontes de dados que você e sua equipe precisam e convidando colegas para usar o catálogo.

## Referências

- [Como registrar ativos de dados](data-catalog-how-to-register.md)
- [Como descobrir ativos de dados](data-catalog-how-to-discover.md)
- [Como anotar ativos de dados](data-catalog-how-to-annotate.md)
- [Como documentar ativos de dados](data-catalog-how-to-documentation.md)
- [Como se conectar a ativos de dados](data-catalog-how-to-connect.md)
- [Como gerenciar ativos de dados](data-catalog-how-to-manage.md)

<!---HONumber=AcomDC_0713_2016-->