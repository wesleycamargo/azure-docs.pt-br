<properties
   pageTitle="Versões de API da Pesquisa do Azure | Microsoft Azure | API da Pesquisa"
   description="Política de versão para APIs REST da Pesquisa do Azure e a biblioteca de cliente no SDK do .NET."
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="03/08/2016"
   ms.author="brjohnst"/>

# Versões de API na Pesquisa do Azure

A Pesquisa do Azure lança atualizações de recursos regularmente. Às vezes, mas não sempre, essas atualizações exigem que publiquemos uma nova versão de nossa API para preservar a compatibilidade com versões anteriores. A publicação de uma nova versão permite controlar quando e como as atualizações de serviço de pesquisa são integradas ao seu código.

Como regra, tentamos publicar novas versões somente quando necessário, pois isso pode envolver esforços para atualizar seu código, a fim de usar uma nova versão de API. Apenas publicaremos uma nova versão se for necessário alterar alguns aspectos da API de uma forma que interrompe a compatibilidade com versões anteriores. Isso pode ocorrer devido a correções a recursos existentes ou devido a recursos novos que alteram a área da superfície da API existente.

Seguimos a mesma regra para atualizações do SDK. O SDK da Pesquisa do Azure segue as regras do [controle de versão semântico](http://semver.org/), o que significa que sua versão tem três partes: principal, secundária e número de build (por exemplo, 1.1.0). Liberaremos uma nova versão principal do SDK somente no caso de alterações que interrompem a compatibilidade com versões anteriores. Para atualizações de recursos contínuas, incrementaremos a versão secundária, e para correções de bug, só aumentaremos a versão de build.

##Instantâneo das versões atuais 

Veja abaixo um instantâneo das versões atuais de todas as interfaces de programação da Pesquisa do Azure. Mapas e outros detalhes podem ser encontrados nas próximas seções deste documento.

Interfaces|Versão principal mais recente|Status
----------|-------------------------|------
[SDK .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx)|1,1|Disponível, liberado em fevereiro de 2016
[API REST do Serviço](https://msdn.microsoft.com/library/azure/dn798935.aspx)|2015-02-28|Disponível
[Preview da API REST do Serviço](search-api-2015-02-28-preview.md)|2015-02-28-Preview|Visualização
[API REST de gerenciamento](https://msdn.microsoft.com/library/azure/dn832684.aspx)|2015-08-19|Disponível

Para as APIs REST, é necessário incluir a `api-version` em cada chamada. Isso facilita direcionar uma versão específica, como uma API de preview. O exemplo a seguir ilustra como o parâmetro `api-version` é especificado:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2015-02-28

> [AZURE.NOTE] Embora cada solicitação tenha uma `api-version`, recomendamos que você use a mesma versão para todas as solicitações de API. Isso ocorre especificamente quando novas versões de API introduzem atributos ou operações que não são reconhecidos por versões anteriores. A combinação de versões de API pode trazer consequências indesejadas e deve ser evitada.
> 
A API REST do Serviço e a API REST de Gerenciamento têm controle de versão que não dependem entre si. Qualquer semelhança nos números de versão são uma coincidência.

APIs GA (ou Disponíveis) podem ser usadas na produção e estão sujeitas aos SLAs do Azure. Versões prévias têm recursos experimentais que nem sempre são migrados para uma versão GA. **É altamente recomendável não usar APIs de preview em aplicativos de produção.**

##Mapa de versão do SDK

Cada versão do SDK do .NET é direcionada a uma versão específica da API REST do Serviço. Os recursos são lançados na API REST primeiro e, então, implementados no SDK.

Agora, o SDK do .NET está disponível e já estamos trabalhando na próxima versão. A tabela a seguir traz as versões futuras do SDK, para que você tenha uma ideia das novidades que estão por vir.

Versão do SDK do .NET|Versão da API REST|Recursos|ETA
----------------|----------------|--------|---
1,1|2015-02-28|Sintaxe de consulta Lucene|Fevereiro de 2016
2\.x-preview|2015-02-28-Preview|Analisadores personalizados, indexador do Blob do Azure, Mapeamentos de campo, ETags|Os recursos começarão a ser fornecidos no T1 de 2016
2\. x|Nova versão GA de API|Igual à 2.x-preview|Logo após a conclusão da 2.x-preview

##Sobre versões Prévias e Disponíveis para o Público em Geral

A Pesquisa do Azure sempre faz o pré-lançamento de recursos experimentais por meio da API REST primeiro, e, então, por meio de versões de pré-lançamento do SDK do .NET. Uma lista dos recursos de visualização pode ser encontrada em [Novidades na Pesquisa do Azure](search-latest-updates.md).

Não há nenhuma garantia de que os recursos de visualização serão migrados para uma versão GA. Enquanto os recursos em uma versão GA são considerados estáveis e não têm a probabilidade de ser alterados, com a exceção de pequenas melhorias e correções de compatibilidade com versões anteriores, os recursos de visualização estão disponíveis para teste e experimento, com o objetivo de coletar comentários sobre o design e a implementação do recurso.

No entanto, como os recursos de visualização estão sujeitos a alterações, não recomendamos escrever um código de produção que dependa de versões prévias. Caso esteja usando uma versão prévia mais antiga, é recomendável migrar para a versão GA (disponível).

- Para o SDK do .NET: obtenha orientações para a migração de código em [Atualizar o SDK do .NET](search-dotnet-sdk-migration.md).
- Para a API REST: obtenha orientações para a migração de código em [Fazer a transição da preview para a GA](search-transition-from-preview.md).

Disponibilidade geral significa que a Pesquisa do Azure agora está vinculada aos termos do Contrato de Nível de Serviço (SLA). O SLA pode ser encontrado em [SLA da Pesquisa do Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

<!---HONumber=AcomDC_0309_2016-->