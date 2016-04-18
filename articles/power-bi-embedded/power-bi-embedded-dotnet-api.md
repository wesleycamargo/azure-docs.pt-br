<properties
   pageTitle="API do .NET do Power BI Embedded"
   description="API do .NET do Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# API do .NET do Power BI Embedded

O **Microsoft Power BI Embedded** Preview concentra-se principalmente na exposição da maioria das funcionalidades de API do Power BI existentes como parte de um serviço do Azure com o qual você possa desenvolver seus aplicativos. Além disso, você poderá provisionar programaticamente, desenvolver e implantar os recursos necessários e o conteúdo do Power BI.

Você pode usar a **API do Power BI** para criar e gerenciar espaços de trabalho de conteúdo do Power BI. Com a API, você pode

  - Importar um arquivo do Power BI Desktop (PBIX) para um espaço de trabalho usando autenticação baseada em chave.
  - Modificar cadeias de conexão de um conjunto de dados.
  - Obter **Relatórios** para integrar em seu próprio aplicativo.
  - Definir credenciais para um conjunto de dados poder se comunicar com a fonte de dados de origem correta.
  - Para saber mais sobre a **API do Power BI**, consulte [Introdução ao Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md).

Consulte a [Referência do Power BI no MSDN](https://msdn.microsoft.com/library/mt669800.aspx).

Aqui estão algumas das classes e métodos que são usados no **exemplo do Power BI Embedded**:

## Namespace Microsoft.PowerBI.Api.Beta

### Classe ReportsExtensions
|Nome|Descrição
|---|---
|[GetReports(IReports, Cadeia de Caracteres, Cadeia de Caracteres)](https://msdn.microsoft.com/library/microsoft.powerbi.api.beta.reportsextensions.getreports.aspx)|Obtém uma lista de relatórios disponíveis no espaço de trabalho especificado
|[GetReportsAsync(IReports, Cadeia de Caracteres, Cadeia de Caracteres, CancellationToken)](https://msdn.microsoft.com/library/microsoft.powerbi.api.beta.reportsextensions.getreportsasync.aspx)|Obtém uma lista de relatórios disponíveis no espaço de trabalho especificado

## Namespace Microsoft.PowerBI.Security

### Métodos de PowerBIToken
|Nome| Descrição
|---|---
|[CreateDevToken (Cadeia de Caracteres, Guid)](https://msdn.microsoft.com/library/mt670215.aspx)|Cria um token de desenvolvedor com data de validade padrão usada para acessar serviços de plataforma do Power BI
|[CreateProvisionToken(Cadeia de Caracteres)](https://msdn.microsoft.com/library/mt670218.aspx)|Cria um token de provisionamento com data de validade padrão usada para gerenciar espaços de trabalho dentro de uma coleção de espaço de trabalho

## Consulte também

- [O que é o Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Introdução ao Microsoft Power BI Embedded preview](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0406_2016-->