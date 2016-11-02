<properties
   pageTitle="Como descobrir as fontes de dados | Microsoft Azure"
   description="Artigo de instruções realçando como descobrir ativos de dados registrados com o Catálogo de Dados do Azure, incluindo pesquisa e filtragem e o uso dos recursos de realce de ocorrências do portal do Catálogo de Dados do Azure."
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
   ms.date="10/04/2016"
   ms.author="maroche"/>


# <a name="how-to-discover-data-sources"></a>Como descobrir fontes de dados

## <a name="introduction"></a>Introdução
**Catálogo de Dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que atua como um sistema de registro e sistema de descoberta em fontes de dados da empresa. Em outras palavras, o **Catálogo de Dados do Azure** ajuda as pessoas a descobrir, entender e usar fontes de dados, ajudando as empresas a obter mais valor de seus dados existentes. Depois que uma fonte de dados é registrada com o **Catálogo de Dados do Azure**, seus metadados são indexados pelo serviço para que os usuários possam pesquisar facilmente e descobrir os dados de que precisam.

## <a name="searching-and-filtering"></a>Pesquisando e filtrando

A descoberta no **Catálogo de Dados do Azure** usa dois mecanismos primários: pesquisa e filtragem.

A pesquisa foi projetada para ser intuitiva e poderosa. Por padrão, os termos de pesquisa são comparados a qualquer propriedade do catálogo, inclusive anotações fornecidas pelo usuário.

A filtragem foi desenvolvida para complementar a pesquisa. Os usuários podem selecionar características específicas como especialistas, tipo de fonte de dados, tipo de objeto e marcas, para exibir apenas ativos de dados correspondentes e também para restringir os resultados da pesquisa a ativos correspondentes.

Usando uma combinação de pesquisa e filtragem, os usuários podem navegar rapidamente pelas fontes de dados que foram registradas com o **Catálogo de Dados do Azure** para descobrir as fontes de dados de que precisam.

## <a name="search-syntax"></a>Sintaxe de pesquisa

Embora a pesquisa de texto livre padrão seja simples e intuitiva, os usuários também podem usar a sintaxe de pesquisa do **Catálogo de Dados do Azure**para ter maior controle sobre os resultados da pesquisa. **Catálogo de Dados do Azure** dá suporte às seguintes técnicas:

| Técnica                 | Uso                                                                                                                                     | Exemplo                                                   |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| Pesquisa básica              | Pesquisa básica usando um ou mais termos de pesquisa. Os resultados são quaisquer ativos que correspondam a qualquer propriedade com um ou mais dos termos especificados. | dados de vendas                                                |
| Escopo de propriedade          | Retornar apenas as fontes de dados em que o termo de pesquisa corresponda à propriedade especificada                                                   | nome:finanças                                              |
| Operadores boolianos         | Ampliar ou restringir uma pesquisa usando operações boolianas                                                                                     | finanças NOT corporativo                                     |
| Agrupando com parênteses | Usar parênteses para agrupar partes da consulta para obter o isolamento lógico, principalmente em conjunto com operadores boolianos              | name:finance AND (tags:Q1 OR tags:Q2) |
| Operadores de comparação      | Usar comparações que não sejam de igualdade para propriedades que tenham tipos de dados numéricos e de data                                                | modifiedTime > "11/05/2014"                                 |

Para obter mais informações sobre a pesquisa do **Catálogo de Dados do Azure** , consulte [https://msdn.microsoft.com/library/azure/mt267594.aspx](https://msdn.microsoft.com/library/azure/mt267594.aspx).

## <a name="hit-highlighting"></a>Realce de ocorrência
Ao serem exibidos resultados de pesquisa, todas as propriedades exibidas que corresponderem aos termos de pesquisa especificados (como o nome do ativo de dados, a descrição e as marcas) serão realçadas para facilitar a identificação do motivo pelo qual determinado ativo de dados foi retornado por determinada pesquisa.

> [AZURE.NOTE] Os usuários podem desativar o realce de ocorrências, se desejado, usando a opção "Realce" no portal do **Catálogo de Dados do Azure** .

Ao serem exibidos resultados de pesquisa, talvez não seja sempre óbvio o motivo pelo qual um ativo de dados é incluído, mesmo com o realce de ocorrências habilitado. Como todas as propriedades são pesquisadas por padrão, um ativo de dados pode ser retornado devido a uma correspondência em uma propriedade de nível de coluna. E como vários usuários podem anotar ativos de dados registrados com suas próprias marcas e descrições, talvez nem todos os metadados sejam exibidos na lista de resultados de pesquisa.

No modo de exibição em bloco padrão, cada bloco exibido nos resultados de pesquisa inclui um ícone "Exibir correspondências do termo de pesquisa", que permite ao usuário exibir rapidamente o número de correspondências e sua localização e acessá-las, se desejar.

 ![Realce de ocorrências e correspondências de pesquisa no portal do Catálogo de Dados do Azure](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Resumo
O registro de uma fonte de dados no **Catálogo de Dados do Azure** facilita a descoberta e a compreensão da fonte de dados, copiando metadados estruturais e descritivos da fonte de dados para o serviço de Catálogo. Depois que uma fonte de dados é registrada, os usuários podem descobri-la usando a filtragem e a pesquisa no portal do **Catálogo de Dados do Azure** .

## <a name="see-also"></a>Consulte também
- [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md) para obter detalhes passo a passo sobre como descobrir fontes de dados.



<!--HONumber=Oct16_HO2-->


