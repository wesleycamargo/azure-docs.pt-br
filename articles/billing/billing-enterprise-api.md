---
title: "APIs Enterprise de cobrança do Azure | Microsoft Docs"
description: "Saiba mais sobre as APIs de Relatório que permitem a clientes Enterprise do Azure efetuar pull dos dados de consumo de modo programático."
services: 
documentationcenter: 
author: aedwin
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 343b71e28adfd32295b837a40ecf64083341b972
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017


---
# <a name="overview-of-reporting-apis-for-enterprise-customers-preview"></a>Visão geral das APIs de Relatórios para clientes Enterprise (Preview)
As APIs de Relatórios permitem que clientes Enterprise do Azure efetuem pull de modo programático dos dados de consumo e cobrança nas ferramentas preferidas de análise de dados. 

## <a name="enabling-data-access-to-the-api"></a>Habilitando o acesso a dados para a API
* **Gerar\Recuperar a Chave de API**: faça logon no Enterprise Portal e siga o tutorial em Ajuda – APIs de Relatório. A primeira seção deste artigo explica como gerar\recuperar a chave de API para o registro especificado.
* **Passando chaves na API**: a chave de API precisa ser passada para cada chamada de Autenticação e Autorização. A propriedade a seguir precisa ser para os cabeçalhos HTTP

|Chave de cabeçalho da solicitação | Valor|
|-|-|
|Autorização| Especifique o valor neste formato: **portador {API_KEY}** <br/> Exemplo: portador e... 09|

## <a name="consumption-apis"></a>APIs de consumo
Um ponto de extremidade Swagger está disponível [aqui](https://consumption.azure.com/v1/swagger) para as APIs descritas abaixo, que devem permitir a fácil introspecção da API e a capacidade de gerar SDKs de cliente usando [AutoRest](https://github.com/Azure/AutoRest) ou [Swagger CodeGen](http://swagger.io/swagger-codegen/). Os dados a partir de 1º de maio de 2014 estão disponíveis por essa API. 

* **Saldo e Resumo**: a [API Saldo e Resumo](billing-enterprise-api-balance-summary.md) oferece um resumo mensal de informações sobre saldos, novas compras, encargos de serviço do Azure Marketplace, ajustes e encargos de excedente.

* **Detalhes de Uso**: a [API Detalhes de Uso](billing-enterprise-api-usage-detail.md) oferece um detalhamento diário das quantidades consumidas e de encargos estimados por um registro. O resultado também inclui informações sobre instâncias, medidores e departamentos. A API pode ser consultada por período de cobrança ou por uma data de início e de término especificada. 

* **Encargo de Repositório do Marketplace**: a [API Encargo de Repositório do Marketplace](billing-enterprise-api-marketplace-storecharge.md) retorna o detalhamento dos encargos do marketplace com base no uso por dia para o Período de Cobrança especificado ou as datas de início e término (taxas avulsas não estão incluídas).

* **Tabela de Preços**: a [API Tabela de Preços](billing-enterprise-api-pricesheet.md) fornece a taxa aplicável de cada Medidor para o Registro e o Período de Cobrança determinados. 

## <a name="helper-apis"></a>APIs auxiliares
 **Listar Períodos de Cobrança**: a [API Períodos de Cobrança](billing-enterprise-api-billing-periods.md) retorna uma lista de períodos de cobrança que têm dados de consumo para o Registro especificado em ordem cronológica inversa. Cada período contém uma propriedade que aponta para a rota da API dos quatro conjuntos de dados: BalanceSummary, UsageDetails, Encargos do Marketplace e PriceSheet.


## <a name="api-response-codes"></a>Códigos de resposta da API  
|Código de status de resposta|Mensagem|Descrição|
|-|-|-|
|200| OK|Nenhum erro|
|401| Não Autorizado| Chave de API não encontrada, inválida, expirada, etc.|
|404| Indisponível| Ponto de extremidade de relatório não encontrado|
|400| Solicitação incorreta| Parâmetros inválidos – intervalos de datas, números de EA, etc.|
|500| Erro de servidor| Solicitação de processamento de erro inesperada| 










