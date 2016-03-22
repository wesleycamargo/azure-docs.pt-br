<properties
   pageTitle="Conceitos de desenvolvedor do Catálogo de Dados do Azure | Microsoft Azure"
   description="Introdução aos conceitos principais no modelo conceitual do Catálogo de Dados do Azure, conforme exposto pela API REST do Catálogo."
   services="data-catalog"
   documentationCenter=""
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
   ms.date="03/10/2016"
   ms.author="derrickv"/>

# Conceitos de desenvolvedor do Catálogo de Dados do Azure

O **Catálogo de Dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que fornece recursos para descoberta de fonte de dados e metadados de fonte de dados de crowdsourcing. Os desenvolvedores podem usar o serviço por meio de suas APIs REST. Entender os conceitos implementados no serviço é importante para os desenvolvedores integrarem com êxito o **Catálogo de Dados do Azure**.

## Principais conceitos

O modelo conceitual do **Catálogo de Dados do Azure** baseia-se em quatro conceitos principais: o **Catálogo**, **Usuários**, **Ativos** e **Anotações**.

![conceito][1]

*Figura 1 - Modelo conceitual simplificado do Catálogo de Dados do Azure*

### Catálogo

Um **Catálogo** é o contêiner de nível superior para todos os metadados armazenados em uma organização. Há um **Catálogo** alocado por conta do Azure. Catálogos estão vinculados a uma assinatura do Azure, mas somente um **Catálogo** pode ser criado para uma conta do Azure, mesmo que uma conta tenha várias assinaturas.

Um catálogo contém **Usuários** e **Ativos**.

### Usuários

Os usuários são entidades de segurança que têm permissões para executar ações (pesquisar no catálogo, adicionar, editar ou remover itens, etc) no Catálogo.

Há várias funções de que um usuário pode ter. Para obter mais informações sobre funções, consulte a seção Funções e a Autorização.

Usuários individuais e grupos de segurança podem ser adicionados.

O Catálogo de Dados do Azure usa o Active Directory do Azure para gerenciar identidades e acesso. Cada usuário do catálogo deve ser um membro do Active Directory para a conta.

### Ativos

Um **Catálogo** contém ativos de dados. **Ativos** são a unidade de granularidade gerenciada pelo catálogo.

A granularidade de um ativo varia de acordo com a fonte de dados. Para SQL Server ou Banco de Dados Oracle, um ativo pode ser uma Tabela ou Exibição. Para o SQL Server Analysis Services, um ativo pode ser uma Medida, uma Dimensão ou um KPI (Indicador de Chave de Desempenho). Para SQL Server Reporting Services, um ativo é um Relatório.

Um **Ativo** é o item que você adiciona ou remove de um Catálogo. Ele é a unidade do resultado obtido pela **Pesquisa**.

Um **Ativo** é composto de seu nome, local e tipo, bem como anotações que o descrevem melhor.

### Anotações

As anotações são itens que representam os metadados sobre os Ativos.

Exemplos de anotações são descrição, marcas, esquema, documentação, etc... Uma lista completa dos tipos de ativos e de anotações está na seção de modelo de Objeto de Ativo.

## Anotações de crowdsourcing e perspectiva do usuário (multiplicidade de opinião)

Um aspecto importante do Catálogo de Dados do Azure é como ele dá suporte ao crowdsourcing de metadados no sistema. Em vez de uma abordagem de wiki, em que há apenas uma opinião e o último editor vence, o modelo do Catálogo de Dados do Azure permite que várias opiniões coexistam no sistema.

Essa abordagem reflete o mundo real dos dados da empresa onde diferentes usuários podem ter diferentes perspectivas sobre um determinado ativo:

-	Um administrador de banco de dados pode fornecer informações sobre contratos de nível de serviço ou a janela de processamento disponível para operações de ETL em massa
-	Um administrador de dados pode fornecer informações sobre os processos de negócios ao qual se aplica o ativo ou as classificações que a empresa tenha aplicado a ele
-	Um analista financeiro pode fornecer informações sobre como os dados são usados durante tarefas de relatórios do fim do período

Para dar suporte a esse exemplo, cada usuário (como o DBA, o administrador de dados e o analista) pode adicionar uma descrição para uma única tabela que foi registrada no Catálogo. Todas as descrições são mantidas no sistema e exibidas no portal do Catálogo de Dados do Azure.

Esse padrão é aplicado à maioria dos itens no modelo de objeto. Esse é o motivo pelo qual tipos de objetos na carga JSON normalmente são matrizes para as propriedades em que você esperava um singleton.

Por exemplo, na raiz do ativo fica uma matriz dos objetos de descrição. A propriedade de matriz é denominada "Descrições". Um objeto de descrição tem três propriedades: descrição, marcas e friendlyName. O padrão é que cada usuário que digita uma ou mais dessas propriedades obtém um objeto de descrição criado para os valores fornecidos pelo usuário.

A UX pode escolher como exibir a combinação. Existem três padrões diferentes para exibição.

-	O padrão mais simples é "Mostrar tudo". Neste padrão de todos os objetos são mostrados em algum tipo de exibição de lista. Isso é que o UX do portal de Catálogo de Dados do Azure faz para descrição.
-	Outro padrão é "Mesclar". Neste padrão, todos os valores dos diferentes usuários são mesclados, removendo valores duplicados. Exemplos desse padrão no UX do portal do Catálogo de Dados do Azure são as propriedades de marcas e especialistas.
-	Um terceiro padrão é "último editor vence". Nesse padrão é mostrado apenas o valor digitado mais recente. friendlyName é um exemplo desse padrão.

## Modelo de objeto de ativo

Como apresentado na seção Conceitos Principais, o modelo de objeto **Catálogo de Dados do Azure** inclui itens que podem ser ativos ou anotações. Itens têm propriedades que podem ser obrigatórias ou opcionais. Algumas propriedades se aplicam a todos os itens. Algumas propriedades se aplicam a todos os ativos. Algumas propriedades se aplicam somente alguns tipos específicos de ativo.

### Propriedades comuns

Essas propriedades se aplicam a todos os tipos de ativos de raiz e todos os tipos de anotação.

> [AZURE.NOTE] As propriedades cujos nomes começam com um sublinhado duplo são tipos do sistema.

|**Nome da propriedade**|**Tipo de dados**|**Comentários**
|---|---|---
|modifiedTime|DateTime|A hora da última modificação na raiz. Isso é definido pelo cliente. (O servidor não mantém esse valor).
|__id|Cadeia de caracteres|id do item (somente leitura). Essa id garante a exclusividade do ativo em um catálogo. |__type|Cadeia de caracteres|O tipo de ativo (somente leitura).
|__\_\_creatorId|Cadeia de caracteres|Uma cadeia de caracteres usada pelo criador do ativo para identificá-lo com exclusividade.

### Propriedades comuns de raiz

Essas propriedades se aplicam a todos os tipos de ativos de raiz.

|**Nome da propriedade**|**Tipo de dados**|**Comentários**
|---|---|---
|name|Cadeia de caracteres|Um nome derivado das informações de local da fonte de dados
|dsl|Local da fonte de dados|Descreve com exclusividade a fonte de dados e é um dos identificadores do ativo. (Consulte a seção de identidade dupla). A estrutura da dsl varia de acordo com o tipo de origem.
|dataSource|DataSourceInfo|Mais detalhes sobre o tipo de ativo.
|lastRegisteredBy|SecurityPrincipal|Descreve o usuário que registrou esse ativo por último. Contém a ID exclusiva do usuário (o upn), bem como um nome de exibição (lastName e firstName).
|lastRegisteredTime|dateTime|A última vez que este ativo foi registrado no catálogo.
|containerId|Cadeia de caracteres|ID do ativo de contêiner da fonte de dados. Não há suporte para essa propriedade para o tipo Contêiner.

### Tipos de ativos de raiz

Tipos de ativos de raiz são os tipos que representam os vários tipos de ativos de dados que podem ser registrados no catálogo.

|**Tipo de Ativo**|**Propriedades adicionais**|**Tipo de dados**|**Comentários**
|---|---|---|---
|Tabela|||Uma Tabela representa quaisquer dados tabulares. Isso inclui uma Tabela SQL, uma Exibição SQL, uma Tabela Tabular do Analysis Services, uma dimensão multidimensional do Analysis Services, uma Tabela do Oracle etc.
|Medida|||Esse tipo representa uma medida do Analysis Services.
||Medida|Coluna|Metadados que descrevem a medida
||isCalculated|Booliano|Especifica se a medida é calculada ou não.
||measureGroup|Cadeia de caracteres|Contêiner físico para medidas
||goalExpression|Cadeia de caracteres|Uma expressão numérica MDX ou um cálculo que retorna o valor de destino do KPI.
||valueExpression|Cadeia de caracteres|Uma expressão numérica MDX que retorna o valor real do KPI.
||statusExpression|Cadeia de caracteres|Uma expressão MDX que representa o estado do KPI em um ponto específico no tempo.
||trendExpression|Cadeia de caracteres|Uma expressão MDX que avalia o valor do KPI com o passar do tempo. A tendência pode ser qualquer critério com base no tempo que seja útil em um contexto de negócios específico.
||measureGroup|Cadeia de caracteres|contêiner físico para medidas
|Relatório|||Esse tipo representa um relatório do SQL Server Reporting Services
||CreatedBy|Cadeia de caracteres| |
||CreatedDate|Cadeia de caracteres| |
|Contêiner|||Esse tipo representa um contêiner de outros ativos, como um banco de dados SQL, um contêiner de Blobs do Azure ou um modelo do Analysis Services.

### Tipos de anotação

Tipos de anotação representam tipos de metadados que podem ser atribuídos a outros tipos no catálogo.

|**Tipo de Anotação**|**Propriedades adicionais**|**Tipo de dados**|**Comentários**
|---|---|---|---
|Descrição|||Cada usuário do sistema pode adicionar suas próprias marcas e descrição. Somente o usuário pode editar o Objeto de descrição. (Os proprietários de Administradores e Ativos podem excluir o objeto de descrição, mas não editá-lo). O sistema os mantém separadamente. Desta maneira, há uma matriz de descrições de cada ativo (uma para cada usuário que contribuiu com seus conhecimentos sobre o ativo, além de possivelmente uma contendo informações derivadas da fonte de dados).
||friendlyName|string|Um nome amigável que pode ser usado em vez do nome derivado da fonte de dados. Isso é útil para exibição e pesquisa.
||marcas|string|Uma matriz de marcas para o ativo
||description|string|uma breve descrição (duas ou três linhas) do ativo
|Esquema|||O Esquema descreve a estrutura dos dados. Ela lista os nomes de atributo (ou seja, coluna, atributo, campo etc.), os tipos e também outros metadados. Essa informação é derivada da fonte de dados. Geralmente há um item de esquema em um ativo.
||colunas|Coluna|Uma matriz de objetos de coluna. Eles descrevem a coluna com informações derivadas da fonte de dados.
|SchemaDescription|||Contém uma descrição e um conjunto de marcas para cada atributo definido no esquema. Cada usuário do sistema pode adicionar suas próprias marcas e descrição. Somente o usuário pode editar o objeto de descrição. (Os proprietários de Administradores e Ativos podem excluir o objeto SchemaDescription, mas não editá-lo). O sistema os mantém separadamente. Desta maneira, há uma matriz de objetos SchemaDescription de cada ativo (uma para cada usuário que contribuiu com seus conhecimentos sobre os atributos, além de possivelmente uma contendo informações derivadas da fonte de dados). O SchemaAttributes está associado livremente ao esquema, por isso ele pode ficar fora de sincronia. Por exemplo, o SchemaDescription pode descrever colunas que não existem mais no esquema ou falhar ao fazer referência a uma nova coluna que foi adicionada recentemente. É responsabilidade do gravador mantê-los em sincronia. A fonte de dados também pode conter informações de descrição. Isso seria um objeto schemaDescription adicional que pode ser criado ao executar a ferramenta.
||columnDescriptions|ColumnDescription|Uma matriz de ColumnDescriptions que descreve as colunas no esquema.
|Especialista|||Contém uma lista de usuários considerados especialistas no conjunto de dados. As opiniões de especialistas (ou seja, descrições) surgirão na parte superior da experiência do usuário ao serem listadas as descrições. Cada usuário pode especificar sua própria lista de especialistas. Somente o usuário pode editar o objeto de especialistas. (Proprietários de Ativos e Administradores podem excluir o objeto Especialistas, mas não editá-lo).
||especialistas|string|Matriz de endereços de email.
|Visualização|||A visualização contém um instantâneo das primeiras 20 linhas de dados para o ativo. A Visualização só faz sentido para alguns tipos de ativos (ou seja, faz sentido para Tabela, mas não para Medida).
||preview|objeto|A matriz de objetos que representa uma coluna. Cada objeto tem um mapeamento de propriedade para uma coluna com um valor para a linha da coluna.
|AccessInstruction|||Contém informações sobre como solicitar acesso à fonte de dados. Essa informação é o que é mostrado no campo “Solicitar Acesso” no Portal do Catálogo.
||Tipo MIME|cadeia de caracteres|O tipo mime do conteúdo.
||conteúdo|cadeia de caracteres|As instruções para obter acesso a esse ativo de dados. Pode ser uma URL, um endereço de email ou um conjunto de instruções.
|TableDataProfile|||
||numberOfRows|int|O número de linhas no conjunto de dados
||tamanho|longo|O tamanho em bytes do conjunto de dados.
||schemaModifiedTime|string|A hora da última modificação do esquema
||dataModifiedTime|string|A hora da última modificação do conjunto de dados (dados foram adicionados, modificados ou excluídos)
|ColumnsDataProfile|||
||colunas|ColumnDataProfile|O número de linhas no conjunto de dados
|Documentação|||Um determinado ativo pode ter apenas uma documentação associada a ele.
||Tipo MIME|cadeia de caracteres|O tipo mime do conteúdo.
||conteúdo|cadeia de caracteres|O conteúdo da documentação.


### Tipos comuns

Tipos comuns podem ser usados como os tipos de propriedades, mas não são Itens.

|**Tipo Comum**|**Propriedades**|**Tipo de dados**|**Comentários**
|---|---|---|---
|DataSourceInfo||||
||sourceType|string|Descreve o tipo de fonte de dados como, por exemplo, SQL Server, Oracle Database etc.
||objectType|string|Descreve o tipo de objeto da fonte de dados como, por exemplo, Tabela, Exibição do SQL Server.
||formatType|string|Descreve a estrutura dos dados. Os valores atuais são estruturados ou não estruturados.
|SecurityPrincipal||||
||upn|string|Endereço de email exclusivo do usuário.
||firstName|string|Nome do usuário (para fins de exibição).
||lastName|string|Sobrenome do usuário (para fins de exibição).
|Coluna||||
||name|string|Nome da coluna ou do atributo.
||type|string|Tipo de dados da coluna ou do atributo. Os tipos permitidos dependem sourceType de dados do ativo. Há suporte para apenas um subconjunto de tipos.
||maxLength|int|O comprimento máximo permitido para a coluna ou atributo. Derivado da fonte de dados. Aplicável somente a alguns tipos de fontes.
||Precisão|byte|A precisão para a coluna ou atributo. Derivado da fonte de dados. Aplicável somente a alguns tipos de fontes.
||isNullable|Booliano|Se a coluna tem permissão para ter um valor nulo ou não. Derivado da fonte de dados. Aplicável somente a alguns tipos de fontes.
||expressão|string|Se o valor for uma coluna calculada, esse campo contém a expressão que expresse este valor. Derivado da fonte de dados. Aplicável somente a alguns tipos de fontes.
||defaultValue|objeto|O valor padrão inserido se não for especificado na instrução insert do objeto. Derivado da fonte de dados. Aplicável somente a alguns tipos de fontes.
|ColumnDescription||||
||marcas|string|Uma matriz de marcas que descreve a coluna.
||description|string|Uma descrição que descreve a coluna.
||columnName|string|O nome da coluna à qual essas informações se referem.
|ColumnDataProfile||||
||columnName|string|O nome da coluna
||type|string|O tipo da coluna
||min|string|O valor mínimo no conjunto de dados
||max|string|O valor máximo do conjunto de dados
||avg|double|O valor médio do conjunto de dados
||stdev|double|O desvio padrão para o conjunto de dados
||nullCount|int|A contagem de valores nulos no conjunto de dados
||distinctCount|int|A contagem de valores distintos no conjunto de dados

## Funções e autorização

O Catálogo de Dados do Microsoft Azure fornece recursos de autorização para operações CRUD de ativos e anotações.

## Principais conceitos

O Catálogo de Dados do Azure usa dois mecanismos de autorização:

- Autorização baseada em função
- Autorização baseada em permissão

### Funções

Há três funções: **Administrador**, **Proprietário** e **Colaborador**. Cada função tem seu escopo e direitos que são resumidos na tabela a seguir.

|**Função**|**Escopo**|**Direitos**
|---|---|---
|Administrador|Catálogo (ou seja, todos os ativos/anotações no catálogo)|Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions
|Proprietário|Cada ativo (ou seja, também conhecido como item de raiz)|Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions
|Colaborador|Cada ativo e anotação individuais|Read Update Delete ViewRoles Note: todos os direitos são revogados se Read à direita do item for revogado do Colaborador

> [AZURE.NOTE] Os direitos **Read**, **Update**, **Delete** e **ViewRoles** são aplicáveis a qualquer item (ativo ou anotação) enquanto **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility** e **ViewPermissions** só são aplicáveis ao ativo de raiz.
>
>O direito **Delete** aplica-se a um item, bem como qualquer subitem ou item único abaixo dele. Por exemplo, excluir um ativo também excluirá todas as anotações desse ativo.

### Permissões

A permissão funciona como uma lista de entradas de controle de acesso. Cada entrada de controle de acesso atribui o conjunto de direitos a uma entidade de segurança. Permissões só podem ser especificadas em um ativo (ou seja, item de raiz) e aplicam-se ao ativo e a todos os subelementos.

Durante a visualização do **Catálogo de Dados do Azure**, somente o direito **Read** tem suporte na lista de permissões para permitir que o cenário restrinja a visibilidade de um ativo.

Por padrão qualquer usuário autenticado tem o direito **Read** para qualquer item do catálogo, a menos que a visibilidade esteja restrita ao conjunto de entidades nas permissões.

## API REST

As solicitações de exibição de item **PUT** e **POST** podem ser usadas para controlar funções e permissões. Além da carga do item, é possível especificar duas propriedades do sistema, **\_\_roles** e **\_\_permissions**.

> [AZURE.NOTE]
>
> **__**\_\_permissions** aplica-se somente a um item raiz.
>
> A função **Proprietário** só é aplicável a um item raiz.
>
> Por padrão, quando um item é criado no catálogo, seu **Colaborador** é definido como o usuário autenticado no momento. Se o item tiver de ser atualizado por todos os usuários, o **Colaborador** deverá ser definido como a entidade de segurança especial <Everyone> na propriedade **\_\_roles** quando o item for publicado pela primeira vez (consulte o exemplo abaixo). O **Colaborador** não pode ser alterado e permanece o mesmo durante o tempo de vida de um item (ou seja, até mesmo o **Administrador** ou o **Proprietário** não têm o direito de alterar o **Colaborador**). O único valor com suporte para a configuração explícita do **Colaborador** é <Everyone>, ou seja, o **Colaborador** só pode ser um usuário que criou um item ou <Everyone>.

### Exemplos
**Defina o Colaborador como <Everyone> ao publicar um item.**

A entidade de segurança especial <Everyone> tem a objectId "00000000-0000-0000-0000-000000000201".

**POST** https://api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2015-07.1.0-Preview

Solicitações ao **ADC (Catálogo de dados do Azure)** podem retornar uma resposta HTTP 302 para indicar o redirecionamento para um ponto de extremidade diferente. Em resposta a uma 302, o chamador deve emitir novamente a solicitação para a URL especificada no cabeçalho de resposta Local.


> [AZURE.NOTE] Algumas implementações de cliente HTTP podem reemitir automaticamente solicitações em resposta a uma 302 do servidor, mas normalmente eliminam **Cabeçalhos de autorização** da solicitação. Como o cabeçalho de Autorização precisa fazer solicitações ao ADC, você deve garantir que o cabeçalho de Autorização ainda seja fornecido ao reemitir uma solicitação a um local de redirecionamento especificado pelo ADC. A seguir está o código de exemplo que demonstra isso usando o objeto HttpWebRequest .NET.


**Corpo**

	{
	    "__roles": [
	        {
	            "role": "Contributor",
	            "members": [
	                {
	                    "objectId": "00000000-0000-0000-0000-000000000201"
	                }
	            ]
	        }
	    ],
	    … other table properties
	}

**Atribuir proprietários e restringir a visibilidade para um item raiz existente**

**PUT** https://api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2015-07.1.0-Preview

	{
	    "__roles": [
	        {
	            "role": "Owner",
	            "members": [
	                {
	                    "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
	                    "upn": "user1@contoso.com"
	                },
	                {
	                    "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
	                    "upn": "user2@contoso.com"
	                }
	            ]
	        }
	    ],
	    "__permissions": [
	        {
	            "principal": {
	                "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
	                "upn": "user3@contoso.com"
	            },
	            "rights": [
	                {
	                    "right": "Read"
	                }
	            ]
	        },
	        {
	            "principal": {
	                "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
	                "upn": "user4@contoso.com"
	            },
	            "rights": [
	                {
	                    "right": "Read"
	                }
	            ]
	        }
	    ]
	}

> [AZURE.NOTE] Em PUT, não é obrigatório especificar uma carga de item no corpo. PUT pode ser usado para atualizar apenas funções e/ou permissões.

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png

<!---HONumber=AcomDC_0316_2016-->