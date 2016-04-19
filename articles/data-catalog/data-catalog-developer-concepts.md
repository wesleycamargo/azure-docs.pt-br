<properties
   pageTitle="Conceitos de desenvolvedor do Catálogo de Dados do Azure | Microsoft Azure"
   description="Este artigo fornece uma introdução aos conceitos principais no modelo conceitual do Catálogo de Dados do Azure, conforme exposto pela API REST do Catálogo de Dados."
   services="data-catalog"
   documentationCenter=""
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
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# Conceitos de desenvolvedor do Catálogo de Dados do Azure

O **Catálogo de Dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que fornece recursos para descoberta de fonte de dados e metadados de fonte de dados de crowdsourcing. Os desenvolvedores podem usar o serviço por meio de suas APIs REST. Entender os conceitos implementados no serviço é importante para os desenvolvedores integrarem com êxito o **Catálogo de Dados do Azure**.

## Principais conceitos

O modelo conceitual do **Catálogo de Dados do Azure** baseia-se em quatro conceitos principais: o **Catálogo**, **Usuários**, **Ativos** e **Anotações**.

![](media/data-catalog-developer-concepts/concept2.png)

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

Descrições, marcas, esquema e documentação são exemplos de anotações. Uma lista completa de tipos de ativo e tipos de anotação estão na seção [Modelo de Objeto de Ativo](#asset-model) abaixo.

## Anotações de crowdsourcing e perspectiva do usuário (multiplicidade de opinião)

Um aspecto importante do Catálogo de Dados do Azure é como ele dá suporte ao crowdsourcing de metadados no sistema. Em vez de uma abordagem de wiki, em que há apenas uma opinião e o último editor vence, o modelo do Catálogo de Dados do Azure permite que várias opiniões coexistam no sistema.

Essa abordagem reflete o mundo real dos dados da empresa onde diferentes usuários podem ter diferentes perspectivas sobre um determinado ativo:

-	Um administrador de banco de dados pode fornecer informações sobre contratos de nível de serviço ou a janela de processamento disponível para operações de ETL em massa
-	Um administrador de dados pode fornecer informações sobre os processos de negócios ao qual se aplica o ativo ou as classificações que a empresa tenha aplicado a ele
-	Um analista financeiro pode fornecer informações sobre como os dados são usados durante tarefas de relatórios do fim do período

Para dar suporte a esse exemplo, cada usuário (como o DBA, o administrador de dados e o analista) pode adicionar uma descrição para uma única tabela que foi registrada no Catálogo. Todas as descrições são mantidas no sistema e exibidas no portal do Catálogo de Dados do Azure.

Esse padrão é aplicado à maioria dos itens no modelo de objeto. Esse é o motivo pelo qual tipos de objetos na carga JSON normalmente são matrizes para as propriedades em que você esperava um singleton.

Por exemplo, na raiz do ativo fica uma matriz dos objetos de descrição. A propriedade de matriz é denominada "Descrições". Um objeto de descrição tem uma propriedade - descrição. O padrão é que cada usuário que digita descrição obtém um objeto de descrição criado para o valor fornecido pelo usuário.

A UX pode escolher como exibir a combinação. Existem três padrões diferentes para exibição.

-	O padrão mais simples é "Mostrar tudo". Neste padrão de todos os objetos são mostrados em algum tipo de exibição de lista. Isso é que o UX do portal de Catálogo de Dados do Azure faz para descrição.
-	Outro padrão é "Mesclar". Neste padrão, todos os valores dos diferentes usuários são mesclados, removendo valores duplicados. Exemplos desse padrão no UX do portal do Catálogo de Dados do Azure são as propriedades de marcas e especialistas.
-	Um terceiro padrão é "último editor vence". Nesse padrão é mostrado apenas o valor digitado mais recente. friendlyName é um exemplo desse padrão.

<a name="asset-model"/>
## Modelo de objeto de ativo

Como apresentado na seção Conceitos Principais, o modelo de objeto **Catálogo de Dados do Azure** inclui itens que podem ser ativos ou anotações. Itens têm propriedades que podem ser obrigatórias ou opcionais. Algumas propriedades se aplicam a todos os itens. Algumas propriedades se aplicam a todos os ativos. Algumas propriedades se aplicam somente alguns tipos específicos de ativo.

### Propriedades do sistema

<table><tr><td><b>Nome da propriedade</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr><tr><td>timestamp</td><td>DateTime</td><td>A hora da última modificação do item. Isso é gerado pelo servidor quando um item é inserido e sempre que um item é atualizado. O valor dessa propriedade é ignorado na entrada de operações de publicação.</td></tr><tr><td>ID</td><td>Uri</td><td>URL absoluta do item (somente leitura). É o URI endereçável exclusivo do item. O valor dessa propriedade é ignorado na entrada de operações de publicação.</td></tr><tr><td>type</td><td>Cadeia de caracteres</td><td>O tipo de ativo (somente leitura).</td></tr><tr><td>etag</td><td>Cadeia de caracteres</td><td>Uma cadeia de caracteres correspondente à versão do item que pode ser usado para controle de simultaneidade otimista ao executar operações que atualizam itens no catálogo. "*" pode ser usado para corresponder a qualquer valor.</td></tr></table>

### Propriedades comuns

Essas propriedades se aplicam a todos os tipos de ativos de raiz e todos os tipos de anotação.

<table>
<tr><td><b>Nome da propriedade</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>fromSourceSystem</td><td>Booliano</td><td>Indica se os dados do item são derivados de um sistema de origem (como o Banco de Dados do SQL Server, Oracle Database) ou criados por um usuário.</td></tr>
</table>

### Propriedades comuns de raiz
<p>
Essas propriedades se aplicam a todos os tipos de ativos de raiz.
<table><tr><td><b>Nome da propriedade</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr><tr><td>name</td><td>Cadeia de caracteres</td><td>Um nome derivado das informações de local da fonte de dados</td></tr><tr><td>dsl</td><td>Local da fonte de dados</td><td>Descreve com exclusividade a fonte de dados e é um dos identificadores do ativo. (Consulte a seção de identidade dupla). A estrutura da dsl varia de acordo com o tipo de origem.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>Mais detalhes sobre o tipo de ativo.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Descreve o usuário que registrou esse ativo por último. Contém a ID exclusiva do usuário (o upn), bem como um nome de exibição (lastName e firstName).</td></tr><tr><td>containerId</td><td>Cadeia de caracteres</td><td>ID do ativo de contêiner da fonte de dados. Não há suporte para essa propriedade para o tipo Contêiner.</td></tr></table>

### Propriedades comuns de anotação não singleton

Essas propriedades se aplicam a todos os tipos de anotação não singleton (ou seja, anotações com permissão para existir em número superior a uma por ativo).

<table>
<tr><td><b>Nome da propriedade</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>chave</td><td>Cadeia de caracteres</td><td>Um usuário especificado chave que identifica exclusivamente a anotação na coleção atual. O comprimento da chave não pode exceder 256 caracteres.</td></tr>
</table>

### Tipos de ativos de raiz

Tipos de ativos de raiz são os tipos que representam os vários tipos de ativos de dados que podem ser registrados no catálogo.

<table><tr><td><b>Tipo de Ativo</b></td><td><b>Propriedades adicionais</b></td><td><b>Tipo de dados</b></td><td><b>Anotações Permitidas</b></td><td><b>Comentários</b></td></tr><tr><td>Tabela</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Marca<p>Esquema<p>ColumnDescription<p>ColumnTag<p> Especialista<p>Visualização<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>Documentação<p></td><td>Uma Tabela representa quaisquer dados tabulares. Isso inclui uma Tabela SQL, Exibição SQL, Tabela Tabular do Analysis Services, dimensão do multidimensional do Analysis Services, Tabela do Oracle, etc...   </td></tr><tr><td>Medida</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Marca<p>Especialista<p>AccessInstruction<p>Documentação<p></td><td>Esse tipo representa uma medida do Analysis Services.</td></tr><tr><td></td><td>medida</td><td>Coluna</td><td></td><td>Metadados que descrevem a medida</td></tr><tr><td></td><td>isCalculated </td><td>Booliano</td><td></td><td>Especifica se a medida é calculada ou não.</td></tr><tr><td></td><td>measureGroup</td><td>Cadeia de caracteres</td><td></td><td>Contêiner físico para medidas</td></tr><td>KPI</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Marca<p>Especialista<p>AccessInstruction<p>Documentação</td><td></td></tr><tr><td></td><td>measureGroup</td><td>Cadeia de caracteres</td><td></td><td>Contêiner físico para medidas</td></tr><tr><td></td><td>goalExpression</td><td>Cadeia de caracteres</td><td></td><td>Uma expressão numérica MDX ou um cálculo que retorna o valor de destino do KPI.</td></tr><tr><td></td><td>valueExpression</td><td>Cadeia de caracteres</td><td></td><td>Uma expressão numérica MDX que retorna o valor real do KPI.</td></tr><tr><td></td><td>statusExpression</td><td>Cadeia de caracteres</td><td></td><td>Uma expressão MDX que representa o estado do KPI em um ponto específico no tempo.</td></tr><tr><td></td><td>trendExpression</td><td>Cadeia de caracteres</td><td></td><td>Uma expressão MDX que avalia o valor do KPI com o passar do tempo. A tendência pode ser qualquer critério com base no tempo que seja útil em um contexto de negócios específico.</td>
<tr><td>Relatório</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Marca<p>Especialista<p>AccessInstruction<p>Documentação<p></td><td>Esse tipo representa um relatório do SQL Server Reporting Services </td></tr><tr><td></td><td>assetCreatedDate</td><td>Cadeia de caracteres</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Cadeia de caracteres</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Cadeia de caracteres</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Cadeia de caracteres</td><td></td><td></td></tr><tr><td>Contêiner</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Marca<p>Especialista<p>AccessInstruction<p>Documentação<p></td><td>Esse tipo representa um contêiner de outros ativos, como um banco de dados SQL, um contêiner de Blobs do Azure ou um modelo do Analysis Services.</td></tr></table>

### Tipos de anotação

Tipos de anotação representam tipos de metadados que podem ser atribuídos a outros tipos no catálogo.

<table>
<tr><td><b>Tipo de Anotação</b></td><td><b>Propriedades adicionais</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>

<tr><td>Descrição</td><td></td><td></td><td>Contém uma descrição para um ativo. Cada usuário do sistema pode adicionar sua própria descrição. Somente o usuário pode editar o Objeto de descrição. (Os Administradores e Proprietários de ativos podem excluir o objeto Descrição, mas não editá-lo). O sistema os mantém separadamente. Desta maneira, há uma matriz de descrições de cada ativo (uma para cada usuário que contribuiu com seus conhecimentos sobre o ativo, além de possivelmente uma contendo informações derivadas da fonte de dados).</td></tr>
<tr><td></td><td>description</td><td>string</td><td>Uma descrição resumida (duas a três linhas) do ativo</td></tr>

<tr><td>Marca</td><td></td><td></td><td>Isso contém uma marca para um ativo. Cada usuário do sistema pode adicionar várias marcas para um ativo. Somente o usuário que criou os objetos Marca pode editá-los. (Administradores e Proprietários de Ativos podem excluir o objeto Marca, mas não editá-lo). O sistema os mantém separadamente. Portanto, há uma matriz de objetos de Marca em cada ativo.</td></tr>
<tr><td></td><td>marca</td><td>string</td><td>Uma marca que descreve o ativo.</td></tr>

<tr><td>FriendlyName</td><td></td><td></td><td>Contém um nome amigável para um ativo. FriendlyName é uma anotação sigleton - apenas um FriendlyName pode ser adicionado a um ativo. Somente o usuário que criou o objeto FriendlyName pode editá-lo. (Proprietários de ativos e Administradores podem excluir o objeto FriendlyName, mas não editá-lo). O sistema os mantém separadamente.</td></tr>
<tr><td></td><td>friendlyName</td><td>string</td><td>Um nome amigável do ativo.</td></tr>

<tr><td>Esquema</td><td></td><td></td><td>O Esquema descreve a estrutura dos dados. Ela lista os nomes de atributo (ou seja, coluna, atributo, campo, etc...), tipos e também outros metadados. Essa informação é derivada da fonte de dados. Esquema é uma anotação singleton - somente um esquema pode ser adicionado a um ativo.</td></tr>
<tr><td></td><td>colunas</td><td>Column[]</td><td>Uma matriz de objetos de coluna. Eles descrevem a coluna com informações derivadas da fonte de dados.</td></tr>

<tr><td>ColumnDescription</td><td></td><td></td><td>Contém uma descrição de uma coluna. Cada usuário do sistema pode adicionar suas próprias descrições para várias colunas (no máximo uma por coluna). Somente o usuário que criou os objetos ColumnDescription pode editá-los. (Os Administradores e Proprietários de ativos podem excluir o objeto ColumnDescription, mas não editá-lo). O sistema os mantém separadamente. Desta maneira, há uma matriz de objetos ColumnDescription de cada ativo (uma por coluna para cada usuário que contribuiu com seus conhecimentos sobre a coluna, além de possivelmente uma contendo informações derivadas da fonte de dados). O ColumnDescription está associado livremente ao Esquema, de modo que pode ficar fora de sincronia. Ou seja, ColumnDescription pode descrever uma coluna que não existe mais no esquema. É responsabilidade do gravador mantê-los em sincronia. A fonte de dados também pode conter informações de descrição de colunas. Estas seriam objetos ColumnDescription adicionais que poderiam ser criados ao executar a ferramenta.</td></tr>
<tr><td></td><td>columnName</td><td>Cadeia de caracteres</td><td>O nome da coluna à qual essa descrição se refere.</td></tr>
<tr><td></td><td>description</td><td>Cadeia de caracteres</td><td>uma descrição resumida (duas a três linhas) da coluna.</td></tr>

<tr><td>ColumnTag</td><td></td><td></td><td>Isso contém uma marca para uma coluna. Cada usuário do sistema pode adicionar várias marcas para uma determinada coluna e pode adicionar marcas a múltiplas colunas. Somente o usuário que criou os objetos ColumnTag pode editá-los. (Administradores e Proprietários de Ativos podem excluir o objeto ColumnTag, mas não editá-lo). O sistema os mantém separadamente. Portanto, há uma matriz de objetos ColumnTag em cada ativo. O ColumnTag está associado livremente ao esquema, de modo que pode ficar fora de sincronia. Ou seja, ColumnTag pode descrever uma coluna que não existe mais no esquema. É responsabilidade do gravador mantê-los em sincronia.</td></tr>
<tr><td></td><td>columnName</td><td>Cadeia de caracteres</td><td>O nome da coluna à qual essa marca se refere.</td></tr>
<tr><td></td><td>marca</td><td>Cadeia de caracteres</td><td>Uma marca que descreve a coluna.</td></tr>

<tr><td>Especialista</td><td></td><td></td><td>Contém um usuário que é considerado um especialista no conjunto de dados. As opiniões de especialistas (ou seja, descrições) surgirão na parte superior da UX ao listar as descrições. Cada usuário pode especificar seus próprios especialistas. Somente o usuário pode editar o objeto de especialistas. (Proprietários de Ativos e Administradores podem excluir o objeto Especialista, mas não editá-lo).</td></tr>
<tr><td></td><td>especialista</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Visualização</td><td></td><td></td><td>A visualização contém um instantâneo das primeiras 20 linhas de dados para o ativo. A Visualização só faz sentido para alguns tipos de ativos (ou seja, faz sentido para Tabela, mas não para Medida).</td></tr>
<tr><td></td><td>preview</td><td>object[]</td><td>A matriz de objetos que representa uma coluna. Cada objeto tem um mapeamento de propriedade para uma coluna com um valor para a linha da coluna.</td></tr>

<tr><td>AccessInstruction</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Tipo MIME</td><td>cadeia de caracteres</td><td>O tipo mime do conteúdo.</td></tr>
<tr><td></td><td>conteúdo</td><td>cadeia de caracteres</td><td>As instruções para obter acesso a esse ativo de dados. Pode ser uma URL, um endereço de email ou um conjunto de instruções.</td></tr>

<tr><td>TableDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>O número de linhas no conjunto de dados</td></tr>
<tr><td></td><td>tamanho</td><td>longo</td><td>O tamanho em bytes do conjunto de dados.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>string</td><td>A hora da última modificação do esquema</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>string</td><td>A hora da última modificação do conjunto de dados (dados foram adicionados, modificados ou excluídos)</td></tr>

<tr><td>ColumnsDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>colunas</td></td><td>ColumnDataProfile[]</td><td>Uma matriz de perfis de dados da coluna.</td></tr>

<tr><td>Documentação</td><td></td><td></td><td>Um determinado ativo pode ter apenas uma documentação associada a ele.</td></tr>
<tr><td></td><td>Tipo MIME</td><td>cadeia de caracteres</td><td>O tipo mime do conteúdo.</td></tr>
<tr><td></td><td>conteúdo</td><td>cadeia de caracteres</td><td>O conteúdo da documentação.</td></tr>

</table>

### Tipos comuns

Tipos comuns podem ser usados como os tipos de propriedades, mas não são Itens.
<table>
<tr><td><b>Tipo Comum</b></td><td><b>Propriedades</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType</td><td>string</td><td>Descreve o tipo de fonte de dados como, por exemplo, SQL Server, Oracle Database, etc...  </td></tr>
<tr><td></td><td>objectType</td><td>string</td><td>Descreve o tipo de objeto da fonte de dados como, por exemplo, Tabela, Exibição do SQL Server.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>Observe que o back-end não executa nenhuma validação de propriedades fornecidas junto ao AAD durante a publicação.</td></tr>
<tr><td></td><td>upn</td><td>string</td><td>Endereço de email exclusivo do usuário. Deverá ser especificado se a objectId não for fornecida ou no contexto da propriedade "lastRegisteredBy"; caso contrário, é opcional.</td></tr>
<tr><td></td><td>ID do objeto</td><td>Guid</td><td>Identidade do AAD do usuário ou grupo de segurança. Opcional. Deverá ser especificado se o upn não for fornecido; caso contrário, será opcional.</td></tr>
<tr><td></td><td>firstName</td><td>string</td><td>Nome do usuário (para fins de exibição). Opcional. Válido somente no contexto da propriedade "lastRegisteredBy". Não pode ser especificado ao fornecer a entidade de segurança para "funções", "permissões" e "especialistas".</td></tr>
<tr><td></td><td>lastName</td><td>string</td><td>Sobrenome do usuário (para fins de exibição). Opcional. Válido somente no contexto da propriedade "lastRegisteredBy". Não pode ser especificado ao fornecer a entidade de segurança para "funções", "permissões" e "especialistas".</td></tr>

<tr><td>Coluna</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>string</td><td>Nome da coluna ou do atributo.</td></tr>
<tr><td></td><td>type</td><td>string</td><td>Tipo de dados da coluna ou do atributo. Os tipos permitidos dependem sourceType de dados do ativo. Há suporte para apenas um subconjunto de tipos.</td></tr>
<tr><td></td><td>maxLength</td><td>int</td><td>O comprimento máximo permitido para a coluna ou atributo. Derivado da fonte de dados. Aplicável somente a alguns tipos de fontes.</td></tr>
<tr><td></td><td>precisão</td><td>byte</td><td>A precisão para a coluna ou atributo. Derivado da fonte de dados. Aplicável somente a alguns tipos de fontes.</td></tr>
<tr><td></td><td>isNullable</td><td>Booliano</td><td>Se a coluna tem permissão para ter um valor nulo ou não. Derivado da fonte de dados. Aplicável somente a alguns tipos de fontes.</td></tr>
<tr><td></td><td>expressão</td><td>string</td><td>Se o valor for uma coluna calculada, esse campo contém a expressão que expresse este valor. Derivado da fonte de dados. Aplicável somente a alguns tipos de fontes.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>string</td><td>O nome da coluna</td></tr>
<tr><td></td><td>type </td><td>string</td><td>O tipo da coluna</td></tr>
<tr><td></td><td>min </td><td>string</td><td>O valor mínimo no conjunto de dados</td></tr>
<tr><td></td><td>max </td><td>string</td><td>O valor máximo do conjunto de dados</td></tr>
<tr><td></td><td>avg </td><td>double</td><td>O valor médio do conjunto de dados</td></tr>
<tr><td></td><td>stdev </td><td>double</td><td>O desvio padrão para o conjunto de dados</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>A contagem de valores nulos no conjunto de dados</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>A contagem de valores distintos no conjunto de dados</td></tr>


</table>

## Funções e autorização

O Catálogo de Dados do Microsoft Azure fornece recursos de autorização para operações CRUD de ativos e anotações.

## Principais conceitos

O Catálogo de Dados do Azure usa dois mecanismos de autorização:

- Autorização baseada em função
- Autorização baseada em permissão

### Funções

Há três funções: **Administrador**, **Proprietário** e **Colaborador**. Cada função tem seu escopo e direitos que são resumidos na tabela a seguir.

<table><tr><td><b>Função</b></td><td><b>Escopo</b></td><td><b>Direitos</b></td></tr><tr><td>Administrador</td><td>Catálogo (ou seja, todos os ativos/anotações no catálogo)</td><td>Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Proprietário</td><td>Cada ativo (ou seja, também conhecido como item de raiz)</td><td>Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Colaborador</td><td>Cada ativo e anotação individuais</td><td>Read Update Delete ViewRoles Note: todos os direitos são revogados se Read à direita do item for revogado do Colaborador</td></tr></table>

> [AZURE.NOTE] Os direitos **Read**, **Update**, **Delete** e **ViewRoles** são aplicáveis a qualquer item (ativo ou anotação) enquanto **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility** e **ViewPermissions** só são aplicáveis ao ativo de raiz.
>
>O direito **Delete** aplica-se a um item, bem como qualquer subitem ou item único abaixo dele. Por exemplo, excluir um ativo também excluirá todas as anotações desse ativo.

### Permissões

A permissão funciona como uma lista de entradas de controle de acesso. Cada entrada de controle de acesso atribui o conjunto de direitos a uma entidade de segurança. Permissões só podem ser especificadas em um ativo (ou seja, item de raiz) e aplicam-se ao ativo e a todos os subelementos.

Durante a visualização do **Catálogo de Dados do Azure**, somente o direito **Read** tem suporte na lista de permissões para permitir que o cenário restrinja a visibilidade de um ativo.

Por padrão qualquer usuário autenticado tem o direito **Read** para qualquer item do catálogo, a menos que a visibilidade esteja restrita ao conjunto de entidades nas permissões.

## API REST

As solicitações de exibição de item **PUT** e **POST** podem ser usadas para controlar funções e permissões. Além da carga do item, é possível especificar duas propriedades do sistema, **roles** e **permissions**.

> [AZURE.NOTE]
>
> **permissions** aplica-se somente a um item raiz.
>
> A função **Proprietário** só é aplicável a um item raiz.
>
> Por padrão, quando um item é criado no catálogo, seu **Colaborador** é definido como o usuário autenticado no momento. Se o item tiver de ser atualizado por todos os usuários, o **Colaborador** deverá ser definido como a entidade de segurança especial <Everyone> na propriedade **roles** quando o item for publicado pela primeira vez (consulte o exemplo abaixo). O **Colaborador** não pode ser alterado e permanece o mesmo durante o tempo de vida de um item (ou seja, até mesmo o **Administrador** ou o **Proprietário** não têm o direito de alterar o **Colaborador**). O único valor com suporte para a configuração explícita do **Colaborador** é <Everyone>, ou seja, o **Colaborador** só pode ser um usuário que criou um item ou <Everyone>.

###Exemplos
**Defina o Colaborador como <Everyone> ao publicar um item.** A entidade de segurança especial <Everyone> tem a objectId "00000000-0000-0000-0000-000000000201". **POST** https://api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [AZURE.NOTE] Algumas implementações de cliente HTTP podem reemitir automaticamente solicitações em resposta a uma 302 do servidor, mas normalmente eliminam **Cabeçalhos de autorização** da solicitação. Como o cabeçalho de Autorização precisa fazer solicitações ao ADC, você deve garantir que o cabeçalho de Autorização ainda seja fornecido ao reemitir uma solicitação a um local de redirecionamento especificado pelo ADC. A seguir está o código de exemplo que demonstra isso usando o objeto HttpWebRequest .NET.

  **Corpo**

    {
      "roles": [
        {
          "role": "Contributor",
          "members": [
            {
              "objectId": "00000000-0000-0000-0000-000000000201"
            }
          ]
        }
      ]
    }

  **Atribua proprietários e restrinja a visibilidade de um item raiz existente** **PUT** https://api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

    {
      "roles": [
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
      "permissions": [
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

<!---HONumber=AcomDC_0406_2016-->