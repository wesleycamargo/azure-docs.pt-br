<properties
   pageTitle="Terminologia do Catálogo de Dados do Azure"
   description="Terminologia do Catálogo de Dados do Azure"
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
   ms.date="07/13/2015"
   ms.author="derrickv"/>

# Terminologia do Catálogo de Dados do Azure

## Catálogo

O Catálogo de Dados do Azure é um repositório de metadados baseado em nuvem no qual dados e fontes de dados pode ser registrados. O catálogo serve como um local de armazenamento central para metadados estruturais extraídos de fontes de dados e metadados descritivos adicionados pelos usuários.

## Fonte de dados

Uma fonte de dados é um sistema ou um contêiner que gerencia ativos de dados. Exemplos incluem bancos de dados do SQL Server, bancos de dados Oracle, bancos de dados do SQL Server Analysis Services (tabela ou multidimensionais) e servidores do SQL Server Reporting Services.

## Ativos de dados

Ativos de dados são objetos contidos em fontes de dados que podem ser registrados com o catálogo. Exemplos incluem tabelas e exibições do SQL Server, tabelas e exibições do Oracle, medidas, dimensões e KPIs do SQL Server Analysis Services e relatórios do SQL Server Reporting Services.

## Local do ativo de dados

O catálogo armazena o local de uma fonte de dados ou ativo de dados, que pode ser usado para se conectar à fonte usando um aplicativo cliente. Os detalhes e o formato do local variam com base no tipo de fonte de dados. Por exemplo, uma tabela do SQL Server pode ser identificada por seu nome de quatro partes – nome do servidor, nome do banco de dados, nome do esquema, nome do objeto – enquanto um relatório do SQL Server Reporting Services pode ser identificado por sua URL.

## Metadados estruturais

Metadados estruturais são metadados extraídos de uma fonte de dados que descrevem a estrutura de um ativo de dados. Isso inclui o local de ativos, o nome do objeto e tipo e as características adicionais específicas de tipo. Por exemplo, os metadados estruturais de tabelas e exibições incluem os nomes e tipos de dados das colunas do objeto.

## Metadados descritivos

Metadados descritivos são metadados que descrevem a finalidade ou a intenção de um ativo de dados. Normalmente os metadados descritivos são adicionados por usuários de catálogo usando o portal do Catálogo de Dados do Azure, mas também podem ser extraídos da fonte de dados durante o registro. A ferramenta de registro do Catálogo de Dados do Azure extrairá as descrições da propriedade Descrição no SQL Server Analysis Services e SQL Server Reporting Services e da propriedade estendida [ms\_description](https://technet.microsoft.com/library/ms190243.aspx) em bancos de dados do SQL Server, se essas propriedades foram preenchidas com valores.

## Visualizações

Uma visualização no Catálogo de Dados do Azure é um instantâneo de até 20 registros que podem ser extraídos da fonte de dados durante o registro e armazenados no catálogo com os metadados de ativos de dados. A visualização pode ajudar usuários que descobrem um ativo de dados a compreender melhor sua função e finalidade. Em outras palavras, a visualização de dados de exemplo pode ser mais valiosa do que ver apenas os nomes de coluna e tipos de dados. Visualizações só têm suporte para tabelas e exibições e devem ser explicitamente selecionadas pelo usuário durante o registro.

## Perspectiva do usuário

No Catálogo de Dados do Azure, qualquer usuário pode fornecer metadados descritivos para um ativo de dados registrado. Cada usuário tem uma perspectiva distinta dos dados e seu uso. Por exemplo, o administrador responsável por um servidor pode fornecer detalhes de seu SLA (Contrato de Nível de Serviço) ou janelas de backup; um administrador de dados pode fornecer links de documentação para os negócios de negócios aos quais os dados dão suporte; e um analista pode fornecer uma descrição em termos mais relevantes para outros analistas e que podem ser mais valiosos para usuários que precisam descobrir e entender os dados.

Todas essas perspectivas são inerentemente valiosas e com o Catálogo de Dados do Azure cada usuário pode fornecer as informações que são significativas para eles, enquanto todos os usuários podem usar essas informações para entender os dados e sua finalidade.

## Especialista

Especialista é um usuário que foi identificado como tendo uma perspectiva de "especialista" informada para um ativo de dados. Qualquer usuário pode adicionar a si mesmo ou outro usuário como um especialista para um ativo. Ser listado como especialista não concede privilégios adicionais no Catálogo de Dados do Azure; permite que os usuários localizem facilmente essas perspectivas que mais provavelmente serão úteis durante a análise de metadados descritivos de um ativo.

## Proprietário

Um proprietário é um usuário que tem privilégios adicionais para gerenciar um ativo de dados no Catálogo de Dados do Azure. Os usuários podem se apropriar de ativos de dados registrados e os proprietários podem adicionar outros usuários como co-proprietários.
> [AZURE.NOTE]Propriedade e gerenciamento estão disponíveis somente na Standard Edition do Catálogo de Dados do Azure.
 
## Registro

Registro é o ato de extrair metadados de ativos de dados de uma fonte de dados e copiá-los no serviço de Catálogo de Dados do Azure. Ativos de dados que foram registrados podem ser anotados e descobertos.

## Consulte também

- [o que é o Catálogo de Dados do Azure?](data-catalog-what-is-data-catalog.md)- Este artigo fornece uma visão geral do serviço Catálogo de Dados do Azure, o valor que ele oferece e os cenários aos quais ele dá suporte.

- [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md)- Este artigo apresenta um tutorial completo que mostra como usar o Catálogo de Dados do Azure para descoberta de fonte de dados.

<!---HONumber=July15_HO5-->