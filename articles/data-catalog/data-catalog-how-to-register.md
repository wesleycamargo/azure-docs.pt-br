<properties
   pageTitle="Como registrar fontes de dados"
   description="Artigo de instruções destacando como registrar fontes de dados ao Catálogo de Dados do Azure, incluindo os campos de metadados extraídos e as fontes de dados com suporte durante a visualização."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/27/2015"
   ms.author="maroche"/>


# Como registrar fontes de dados

## Introdução
O**Catálogo de Dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que atua como um sistema de registro e sistema de descoberta em fontes de dados da empresa. Em outras palavras, o **Catálogo de Dados do Azure** ajuda as pessoas a descobrir, entender e usar fontes de dados, ajudando as empresas a obter mais valor de seus dados existentes. E a primeira etapa para tornar uma fonte de passível de ser descoberta no **Catálogo de Dados do Azure** é registrar essa fonte de dados.
## Registrar fontes de dados
Registro é o ato de extrair metadados da fonte de dados e copiá-los no serviço de **Catálogo de Dados do Azure**. Os dados permanecem onde atualmente residem e permanecem sob o controle dos administradores e políticas do sistema atual.

Para registrar uma fonte de dados, basta iniciar a ferramenta de registro de fonte de dados do **Catálogo de Dados do Azure** no portal do **Catálogo de Dados do Azure**. Faça logon com sua conta escolar ou corporativa (as mesmas credenciais do Active Directory do Azure que você usa para fazer logon no portal) e, em seguida, selecione a fonte de dados que você deseja registrar. Consulte o tutorial [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md) para obter mais detalhes passo a passo.

Depois que a fonte de dados tiver sido registrada, o catálogo rastreia o local e classifica os metadados para que os usuários possam pesquisar, navegar, descobrir a fonte de dados e usar seu local para se conectar a ele usando o aplicativo ou a ferramenta de sua preferência.
## Fontes com suporte
Na visualização atual, o **Catálogo de Dados do Azure** dá suporte ao registro dessas fontes de dados e tipos de objeto:

* Exibições e tabelas do mecanismo do banco de dados do SQL Server
* Exibições e tabelas de banco de dados Oracle
* KPIs, medidas e dimensões multidimensionais do SQL Server Analysis Services
* Tabelas tabulares do SQL Server Analysis Services
* Relatórios do SQL Server Reporting Services

> [AZURE.NOTE]Suporte do SQL Server também inclui o Banco de Dados SQL do Microsoft Azure

<br/>

> [AZURE.NOTE]O suporte do SQL Server Reporting Services destina-se somente a servidores de modo nativo – o modo do SharePoint ainda não tem suporte

<br/>

## Metadados estruturais
Quando você estiver registrando uma fonte de dados, a ferramenta de registro vai extrair informações sobre a estrutura dos objetos selecionados — isso é referido como metadados estruturais. Para todos os objetos, esses metadados estruturais vão incluir o local do objeto, para que as ferramentas do cliente descubram os dados. Outros metadados estruturais incluem o tipo e o nome do objeto e o nome de coluna/atributo e tipo de dados.
## Metadados descritivos
Além dos principais metadados estruturais extraídos da fonte de dados, a ferramenta de registro da fonte de dados também vai extrair metadados descritivos. Para SQL Server Analysis Services e SQL Server Reporting Services, isso é retirado das propriedades expostas por esses serviços. Para o SQL Server, os valores fornecidos usando a propriedade estendida de ms\_description serão extraídos. Para o Banco de dados Oracle, a ferramenta de registro da fonte de dados vai extrair a coluna COMMENTS da exibição ALL\_TAB\_COMMENTS.

Além dos metadados descritivos extraídos da fonte de dados, os usuários também podem inserir metadados descritivos usando a ferramenta de registro de fonte de dados. Os usuários podem adicionar marcas e podem identificar especialistas para os objetos que estão sendo registrados. Todos esses metadados descritivos são copiados no serviço **Catálogo de Dados do Azure** junto com os metadados estruturais.
## Incluindo visualizações
Por padrão, somente metadados são extraídos de fontes de dados e copiados para o serviço **Catálogo de Dados do Azure**, mas entender uma fonte de dados muitas vezes se torna mais fácil vendo uma amostra dos dados que ela contém.

A ferramenta de registro de fonte de dados do **Catálogo de Dados do Azure** permite que os usuários incluam uma visualização de instantâneo dos dados em cada tabela e modo de exibição que está registrado. Se o usuário aceita incluir visualizações durante o registro, a ferramenta de registro incluirá até 20 registros de cada tabela e exibição. Esse instantâneo é, então, copiado para o catálogo junto com os metadados estruturais e descritivos. Observação: Tabelas largas com um grande número de colunas podem ter menos de 20 registros incluídos na sua visualização.
## Atualização de registros
Registrar uma fonte de dados fará com que ela seja detectável no **Catálogo de Dados do Azure** usando os metadados e visualização opcionais extraídos durante o registro. Se a fonte de dados precisa ser atualizada no catálogo (por exemplo, se o esquema de um objeto foi alterado, ou tabelas originalmente excluídas devem ser incluídas ou um usuário quiser atualizar os dados incluídos nas visualizações), a ferramenta de registro da fonte de dados pode ser executada novamente.

O novo registro de uma fonte de dados já registrada executa uma operação de mesclagem "upsert": objetos existentes serão atualizados, enquanto os novos objetos serão criados. Todos os metadados fornecidos por usuários por meio do portal do **Catálogo de Dados do Azure** serão mantidos.

## Resumo
Registrar uma fonte de dados com o **Catálogo de Dados do Azure** facilita a descoberta e o entendimento da fonte de dados, copiando os metadados estruturais e descritivos da fonte de dados para o serviço de catálogo. Depois que uma fonte de dados tiver sido registrada, ela poderá, então, ser anotada gerenciada e descoberta usando o portal do **Catálogo de Dados do Azure**.

<!---HONumber=July15_HO5-->