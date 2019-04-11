---
title: Migrar do Enterprise Agreement para APIs de contrato do cliente da Microsoft - Azure | Microsoft Docs
description: Este artigo ajuda você a entender as consequências da migração de um Microsoft Enterprise Agreement (EA) para um contrato de cliente da Microsoft.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: e4c5607089efb247620766fb311b97cae3772770
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59279866"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrar do Enterprise Agreement para APIs de contrato do cliente da Microsoft

Este artigo ajuda você a entender a estrutura de dados, API e outras diferenças de integração de sistema entre contas de Enterprise Agreement (EA) e o contrato de cliente da Microsoft (MCA). O gerenciamento de custos do Azure dá suporte a APIs para ambos os tipos de conta. Examine os [configurar a conta de cobrança para](../billing/billing-mca-setup-account.md) artigo de contrato de cliente da Microsoft antes de continuar.

As organizações com uma conta EA existente devem examinar este artigo em conjunto com a configuração de uma conta MCA. Anteriormente, se você renovar uma conta do EA, será necessário um mínimo de trabalho para mover de um registro antigo para um novo. No entanto, a migração para uma conta MCA requer esforço adicional. Esforço adicional é devido às alterações no subsistema de cobrança subjacente, que afetam todas as APIs relacionadas ao custo e ofertas de serviço.

## <a name="mca-apis-and-integration"></a>APIs de MCA e integração

APIs de MCA e nova integração permite que você:

- Ter disponibilidade completa de API por meio de APIs nativas do Azure.
- Configure várias faturas em uma única conta de cobrança.
- Acesse uma API combinada com o uso do serviço do Azure, o uso do Marketplace de terceiros e compras no Marketplace.
- Exibir os custos entre os perfis (o mesmo que registros) de cobrança usando o gerenciamento de custos do Azure.
- Acessar novas APIs para mostrar os custos, seja notificado quando os custos excedem os limites predefinidos e exportar dados brutos automaticamente.

## <a name="migration-checklist"></a>Lista de verificação de migração

A seguintes itens Ajuda que fazer a transição para MCA APIs.

- Familiarize-se com o novo [conta de cobrança de contrato do Microsoft Customer](../billing/billing-mca-overview.md).
- Determine quais APIs você usará e ver quais delas são substituídos na seção a seguir.
- Familiarize-se com o [APIs de REST do Azure Resource Manager](/rest/api/azure).
- Se você ainda não estiver usando APIs do Azure Resource Manager [registrar seu aplicativo cliente com o Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Atualize qualquer código de programação para [autenticação de uso do Azure AD](/rest/api/azure/#create-the-request).
- Atualize qualquer código de programação para substituir chamadas à API de EA por chamadas à API MCA.
- Atualize o tratamento de erros para usar novos códigos de erro.
- Revise as ofertas de integração adicional, como o Cloudyn e o Power BI, para outros necessária ação.

## <a name="ea-apis-replaced-with-mca-apis"></a>APIs de EA substituído com APIs MCA

APIs de EA usam uma chave de API para autenticação e autorização. APIs de MCA usam a autenticação do Azure AD.

| Finalidade | API DE EA | API DE MCA |
| --- | --- | --- |
| Saldo e créditos | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Uso (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Uso (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Uso do Marketplace (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Períodos de cobrança | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Tabela de preços | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format=json|CSV Microsoft.Billing/billingAccounts/.../billingProfiles/.../invoices/... /pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/../billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| Compras de reserva | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Recomendações de reserva | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Uso de reserva | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> serviço do azure e o uso do Marketplace de terceiros estão disponíveis com o [API de detalhes de uso](/rest/api/consumption/usagedetails).

As seguintes APIs estão disponíveis para contas de cobrança MCA:

| Finalidade | API do cliente da Microsoft (MCA) do contrato |
| --- | --- |
| Contas de cobrança<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Perfis de cobrança<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Seções de nota fiscal<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Faturas | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Assinaturas de cobrança | {scope}/billingSubscriptions |

<sup>2</sup> APIs retornam listas de objetos, que são escopos, em que experiências de gerenciamento de custo no portal do Azure e APIs de operam. Para obter mais informações sobre escopos de gerenciamento de custos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

Se você usar quaisquer APIs de EA existente, você precisará atualizá-los para dar suporte a contas de cobrança MCA. A tabela a seguir mostra outras alterações de integração:

| Finalidade | Oferta antiga | Nova oferta |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Gerenciamento de Custos do Azure](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Consumo de informações do Microsoft](/power-bi/desktop-connect-azure-consumption-insights) pack e o conector de conteúdo | [Aplicativo Microsoft Azure consumo Insights Power BI](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) e [conector do Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>APIs para obter o equilíbrio e créditos

O [obter o resumo do saldo](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API fornece um resumo mensal de:

- Saldos
- Novas compras
- Encargos de serviço do Azure Marketplace
- Ajustes
- Encargos excedentes do serviço

Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, consulte [Introdução ao REST](/rest/api/azure/#create-the-request).

A API de resumo de saldo obter é substituída pela API do Microsoft.Billing/billingAccounts/billingProfiles/availableBalance.

Para obter os saldos disponíveis com a API de saldo disponível:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>APIs para obter o uso e custo

Obter um detalhamento diário de custos do uso do serviço do Azure, o uso do Marketplace de terceiros e outras compras do Marketplace com as seguintes APIs. As seguintes APIs separadas foram mescladas para serviços do Azure e o uso do Marketplace de terceiros. As APIs antigas são substituídas pela [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) API. Ele adiciona as compras no Marketplace, que foram apenas mostradas anteriormente no saldo resumidas para data.

- [Obtenha detalhes de uso/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Obter detalhes de uso/enviar](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Obter detalhes de uso/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Obter detalhes de uso/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Obter o custo de armazenamento do marketplace/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Obter o encargo de armazenamento do marketplace/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, consulte [Introdução ao REST](/rest/api/azure/#create-the-request).

As APIs anteriores são substituídas pela API de detalhes de uso/consumo.

Para obter detalhes de uso com a API de detalhes de uso:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

A API de detalhes de uso, assim como acontece com todas as APIs de gerenciamento de custo, está disponível em vários escopos. Para custos faturados, como tradicionalmente, você receberia em um nível de registro, use o escopo de perfil de cobrança.  Para obter mais informações sobre escopos de gerenciamento de custos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

| Type | Formato de ID |
| --- | --- |
| Conta de cobrança | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Perfil de faturamento | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Assinatura | `/subscriptions/{subscriptionId}` |
| Grupo de recursos | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Use os seguintes parâmetros de querystring para atualizar qualquer código de programação.

| Parâmetros antigos | Novos parâmetros |
| --- | --- |
| `billingPeriod={billingPeriod}` | Sem suporte |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

O corpo da resposta também foi alterado.

Corpo de resposta antigo:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Novo corpo de resposta:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

O nome da propriedade que contém a matriz de registros de uso é alterado de dados a serem _valores_. Cada registro usado para ter uma lista simples de propriedades detalhadas. No entanto, cada registro agora todos os detalhes agora estão em uma propriedade aninhada chamada _propriedades_, exceto as marcas. A nova estrutura é consistente com as outras APIs do Azure. Alguns nomes de propriedade foram alterados. A tabela a seguir mostra as propriedades correspondentes.

| Propriedade antiga | Nova propriedade | Observações |
| --- | --- | --- |
| Id da Conta | N/D | O criador da assinatura não controlado. Use invoiceSectionId (mesmo que a ID do departamento). |
| AccountNameAccountOwnerId e AccountOwnerEmail | N/D | O criador da assinatura não controlado. Use invoiceSectionName (mesmo que departmentName). |
| Informações Adicionais | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Observe que essas propriedades são opostos. Se isAzureCreditEnabled for true, ChargesBilledSeparately será false. |
| ConsumedQuantity | quantidade | &nbsp; |
| Serviço Consumido | consumedService | Valores de cadeia de caracteres podem ser diferentes. |
| Id do Serviço Consumido | Nenhum | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Data e usageStartDate | data | &nbsp;  |
| Dia | Nenhum | Analisa o dia da data. |
| Id do Departamento | invoiceSectionId | Os valores exatos são diferentes. |
| DepartmentName | invoiceSectionName | Valores de cadeia de caracteres podem ser diferentes. Configure as seções de nota fiscal de acordo com os departamentos, se necessário. |
| ExtendedCost e custo | costInBillingCurrency | &nbsp;  |
| InstanceId | ResourceId | &nbsp;  |
| É Cobrança Recorrente | Nenhum | &nbsp;  |
| Local padrão | location | &nbsp;  |
| Categoria do Medidor | meterCategory | Valores de cadeia de caracteres podem ser diferentes. |
| MeterId | meterId | Valores de cadeia de caracteres são diferentes. |
| Nome do Medidor | meterName | Valores de cadeia de caracteres podem ser diferentes. |
| Região do Medidor | meterRegion | Valores de cadeia de caracteres podem ser diferentes. |
| Sub-categoria do Medidor | meterSubCategory | Valores de cadeia de caracteres podem ser diferentes. |
| Mês | Nenhum | Analisa o mês da data. |
| Nome da oferta | Nenhum | Use publisherName e productOrderName. |
| OfferId | Nenhum | &nbsp;  |
| Número do Pedido | Nenhum | &nbsp;  |
| PartNumber | Nenhum | Use meterId e productOrderName para identificar exclusivamente os preços. |
| Nome do Plano | productOrderName | &nbsp;  |
| Produto | Produto |   |
| ProductId | productId | Valores de cadeia de caracteres são diferentes. |
| Nome do editor | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | Valores de cadeia de caracteres são diferentes. |
| Localização do Recurso | resourceLocation | &nbsp;  |
| Id da Localização do Recurso | Nenhum | &nbsp;  |
| Taxa de Recursos | effectivePrice | &nbsp;  |
| Id de Administrador de Serviço | N/D | &nbsp;  |
| Informações de Serviço 1 | serviceInfo1 | &nbsp;  |
| Informações de Serviço 2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Valores de cadeia de caracteres podem ser diferentes. |
| ServiceTier | meterSubCategory | Valores de cadeia de caracteres podem ser diferentes. |
| Identificador de Serviço da Loja | N/D | &nbsp;  |
| Guid de Assinatura | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Marcas | marcas | A propriedade tags se aplica a raiz do objeto, não para a propriedade de propriedades aninhadas. |
| Unidade de Medida | unitOfMeasure | Valores de cadeia de caracteres são diferentes. |
| usageEndDate | data | &nbsp;  |
| Ano | Nenhum | Analisa o ano da data. |
| (novo) | billingCurrency | Moeda usada para a cobrança. |
| (novo) | billingProfileId | ID exclusiva para o perfil de cobrança (mesmo que o registro). |
| (novo) | billingProfileName | Nome do perfil de cobrança (mesmo que o registro). |
| (novo) | chargeType | Use para diferenciar o uso do serviço do Azure, o uso do Marketplace e compras. |
| (novo) | invoiceId | ID exclusiva da nota fiscal. Vazio para o mês atual, abra. |
| (novo) | publisherType | Tipo de publicador para compras. Vazio para uso. |
| (novo) | serviceFamily | Tipo de compra. Vazio para uso. |
| (novo) | servicePeriodEndDate | Data de término para o serviço comprado. |
| (novo) | servicePeriodStartDate | Data inicial para o serviço comprado. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>Substituído pela API de notas fiscais de API de períodos de cobrança

Contas de cobrança MCA não usam períodos de cobrança. Em vez disso, eles usam notas fiscais para períodos de cobrança específicos de custos de escopo. O [API períodos de cobrança](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) é substituído pela API de notas fiscais. Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, consulte [Introdução ao REST](/rest/api/azure/#create-the-request).

Para obter as notas fiscais com a API de notas fiscais:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>APIs de folha de preço

Esta seção discute as APIs de folha de preço existentes e fornece recomendações para mover para a API de tabela de preços para contratos de clientes da Microsoft. Ele também discute a API de tabela de preços para os contratos de cliente do Microsoft e explica os campos em folhas de preços. O [Enterprise obter tabela de preços](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) e [Enterprise obter períodos de cobrança](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) APIs são substituídas pela API de tabela de preços para contratos de clientes da Microsoft (Microsoft.Billing/billingAccounts/billingProfiles / tabela de preços). O novo API dá suporte a formatos JSON e CSV, nos formatos assíncronos de REST. Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, consulte [Introdução ao REST](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>APIs Enterprise de cobrança

Você usou APIs de cobrança do Enterprise com registros empresariais para obter informações do período de cobrança e de preço. Autenticação e autorização usados tokens de web do Azure Active Directory.

Para obter os preços aplicáveis para o registro Enterprise especificado com a folha de preços e as APIs do período de cobrança:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>API de tabela de preços para os contratos de cliente da Microsoft

Use a API de tabela de preços para contratos de clientes da Microsoft para exibir os preços para todos os serviços de consumo do Marketplace e de consumo do Azure. Os preços mostrados para o perfil de cobrança se aplicam a todas as assinaturas que pertencem ao perfil de cobrança.

Use a API de tabela de preços para exibir todos os dados de tabela de preços de serviços de consumo do Azure no formato CSV:

| Método | URI da solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Use a API de tabela de preços para exibir todos os dados de tabela de preços de serviços de consumo do Azure no formato JSON:

| Método | URI da solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Usando a API retorna a tabela de preços para toda a conta. No entanto, você também pode obter uma versão condensada da tabela de preços no formato PDF. O resumo inclui serviços de consumo de consumo do Azure e do Marketplace que são cobrados por uma nota fiscal específica. A nota fiscal é identificada pelo {invoiceId}, que é igual a **número da nota fiscal** mostrado nos arquivos PDF de resumo da fatura. Aqui está um exemplo.

![Imagem de exemplo que mostra o número de nota fiscal que corresponde ao InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Para exibir informações de fatura com a API de tabela de preços no formato CSV:

| Método | URI da solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Para exibir informações de fatura com a API de tabela de preços no formato JSON:

| Método | URI da solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Você também pode ver os preços previstos para qualquer serviço de consumo do Marketplace ou de consumo do Azure no ciclo de cobrança atual aberto ou período de serviço.

Para exibir os preços previstos para serviços de consumo com a API de tabela de preços no formato CSV:

| Método | URI da solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Para exibir os preços previstos para serviços de consumo com a API de tabela de preços no formato JSON:

| Método | URI da solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

São as APIs de folha de preço do Microsoft Customer contrato *APIs de REST assíncronas*. As respostas para as APIs alterado das APIs síncronas mais antigas. O corpo da resposta da API também foi alterado.

#### <a name="old-response-body"></a>Corpo de resposta antigo

Aqui está um exemplo da resposta da API REST síncrona:

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>Novo corpo de resposta

As APIs oferecem suporte a [assíncronas de REST do Azure](../azure-resource-manager/resource-manager-async-operations.md) formato. Chamar a API usando GET e recebe a seguinte resposta:

```
No Response Body

HTTP Status 202 Accepted
```

Os seguintes cabeçalhos são enviados com o local da saída:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Fazer com que o GET outra chamada para o local. A resposta para a chamada GET é o mesmo até que a operação atinja um estado de conclusão ou falha. Quando concluído, a resposta para o local de chamada GET retorna a URL de download. Como se a operação foi executada ao mesmo tempo. Aqui está um exemplo:

```
HTTP Status 200

                                    {
                            “id”: “providers/Microsoft.Consumption/operationresults/{operationId}”,
                            “name”: {operationId},
                           “type”: “Microsoft.Consumption/operationResults”,
                           “properties” : {
                                  “downloadUrl”: {urltoblob},
                                  “vaildTill”: “Date”
}
                     }
```

O cliente também pode fazer uma chamada GET o `Azure-AsyncOperation`. O ponto de extremidade retorna o status da operação.

A tabela a seguir mostra os campos em que a API de tabela de preços Enterprise Obtenha mais antigo. Ele inclui campos correspondentes na nova folha de preço para contratos de clientes da Microsoft:

| Propriedade antiga | Nova propriedade | Observações |
| --- | --- | --- |
| billingPeriodId  | _Não aplicável_ | Não aplicável. Contratos do Microsoft Customer, a nota fiscal e a tabela de preços associado substituído o conceito de billingPeriodId. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Valores de cadeia de caracteres podem ser diferentes. |
| includedQuantity  | includedQuantity | Não aplicável para serviços em contratos de clientes da Microsoft. |
| partNumber  | _Não aplicável_ | Em vez disso, use uma combinação de productOrderName (mesmo que offerId) e meterid. |
| unitPrice  | unitPrice | Preço unitário é aplicável para serviços consumidos em contratos de clientes da Microsoft. |
| currencyCode  | pricingCurrency | Contratos de clientes da Microsoft têm representações de preços na moeda de preços e cobrança. O currencyCode corresponde à pricingCurrency em contratos de clientes da Microsoft. |
| offerId | productOrderName | Em vez de OfferId, você pode usar productOrderName mas não é o mesmo OfferId. No entanto, productOrderName e medidor determinam um preço de contratos de clientes da Microsoft relacionados a meterId e o Offerid em registros herdados. |

## <a name="consumption-price-sheet-api-operations"></a>Operações de API tabela de preços de consumo

Contratos Enterprise, você usou a API de tabela de preços de consumo [Obtenha](/rest/api/consumption/pricesheet/get) e [obter por período de cobrança](/rest/api/consumption/pricesheet/getbybillingperiod) operações para um escopo por um período de cobrança ou subscriptionId. A API usa a autenticação de gerenciamento de recursos do Azure.

Para obter as informações de folha de preços para um escopo com a API de tabela de preços:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Para obter informações de folha de preços por período com a API de tabela de preços de cobrança:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Em vez de pontos de extremidade de API acima, use os seguintes contratos do Microsoft Customer:

**API de tabela de preços para contratos de clientes da Microsoft (API de REST assíncronas)**

Essa API é para contratos de clientes da Microsoft e fornece atributos adicionais.

**Tabela de preços para um escopo de perfil de cobrança em uma conta de cobrança**

Essa API é a API existente. Ele foi atualizado para fornecer a folha de preços para um perfil de cobrança em uma conta de cobrança.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Tabela de preços para um escopo por conta de cobrança

A autenticação do Azure Resource Manager é usada quando você receber a folha de preços no escopo de registro em uma conta de cobrança.

Para obter a tabela de preços na conta de registro em uma conta de cobrança:

| Método | URI da solicitação |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Para um contrato de cliente da Microsoft, use as informações na seção a seguir. Ele fornece as propriedades de campo usadas para contratos de Microsoft Customer.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Tabela de preços para um escopo de perfil de cobrança em uma conta de cobrança

A tabela de preços atualizado por conta de cobrança API obtém a tabela de preços no formato CSV. Para obter a tabela de preços no escopo do perfil de cobrança para um MCA:

| Método | URI da solicitação |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

No escopo de registro do EA, a resposta da API e as propriedades são idênticas. As propriedades correspondem às mesmas propriedades MCA.

As propriedades mais antigas [APIs de folha de preço do Azure Resource Manager](/rest/api/consumption/pricesheet) e as mesmas propriedades novas estão na tabela a seguir.

| Propriedade do antigo do Azure Resource Manager preço folha API  | Nova propriedade de API de tabela de preços de contrato do Microsoft Customer   | DESCRIÇÃO |
| --- | --- | --- |
| ID de medidor | _meterId_ | Identificador exclusivo para o medidor. Mesmo que meterId. |
| Medir nome | meterName | Nome do medidor. Medidor representa o recurso implantável do serviço do Azure. |
| Medir categoria  | propriedade serviço | Nome da categoria de classificação para o medidor. Mesmo que o serviço na tabela de preços do contrato Microsoft Customer. Valores de cadeia de caracteres são diferentes. |
| Medidor de subcategoria | meterSubCategory | Nome da categoria subclassificação medidor. Com base na classificação de diferenciação de conjunto de recursos de alto nível no serviço. Por exemplo, banco de dados SQL básico versus banco de dados SQL Standard |
| Região de medição | meterRegion | &nbsp;  |
| Unidade | _Não aplicável_ | Pode ser analisado do unitOfMeasure. |
| Unidade de medida | unitOfMeasure | &nbsp;  |
| Número da peça | _Não aplicável_ | Em vez de partNumber, use productOrderName e MeterId para identificar exclusivamente o preço de um perfil de cobrança. Os campos são listados na fatura em vez do partNumber em faturas MCA MCA. |
| Preço unitário | unitPrice | Preço da unidade de contrato de cliente da Microsoft. |
| Código de moeda | pricingCurrency | Contratos de clientes da Microsoft representam os preços na moeda de preços e cobrança de moeda. Código de moeda é o mesmo que o pricingCurrency em contratos de clientes da Microsoft. |
| Quantidade incluída | includedQuantity | Não é aplicável aos serviços em contratos de clientes da Microsoft. Mostrar com valores de zero. |
|  ID da Oferta  | productOrderName | Em vez de OfferId, use productOrderName. Não é igual ao OfferId, no entanto o medidor e productOrderName determinam um preço de contratos de clientes da Microsoft. Relacionados a meterId e Offerid em registros herdados. |

O preço para contratos de clientes da Microsoft é definido diferente dos contratos Enterprise. O preço para serviços no registro Enterprise é exclusivo para o produto, PartNumber, medidor e oferta. O PartNumber não é usado em contratos de clientes da Microsoft.

O preço do serviço de consumo do Azure que faz parte de um contrato de cliente da Microsoft é exclusivo para productOrderName e meterId. Eles representam o medidor de serviço e o plano de produto.

Para reconciliar entre a tabela de preços e o uso na API de detalhes de uso, você pode usar o productOrderName e meterId.

Os usuários que têm a cobrança de proprietário, colaborador, leitor de perfil e direitos do Gerenciador de nota fiscal podem baixar a folha de preços.

A tabela de preços inclui os preços para os serviços cujo preço é baseado em uso. Os serviços incluem o consumo do Azure e o consumo do Marketplace. Os preços mais recentes no final de cada período de serviço é bloqueado e aplicado ao uso em um período de serviço único. Para os serviços de consumo do Azure, o período de serviço é geralmente um mês do calendário.

### <a name="retired-price-sheet-api-fields"></a>Campos de API tabela de preços obsoletos

Os seguintes campos são não está disponível nas APIs de folha de preço do Microsoft cliente contrato ou ter os mesmos campos.

|Campo obsoleto| DESCRIÇÃO|
|---|---|
| billingPeriodId | Não aplicável. Corresponde a InvoiceId para MCA. |
| offerId | Não aplicável. Corresponde a productOrderName em MCA. |
| meterCategory  | Não aplicável. Corresponde ao serviço no MCA. |
| unit | Não aplicável. Pode ser analisado do unitOfMeasure. |
| currencyCode | Mesmo que o pricingCurrency em MCA. |
| meterLocation | Mesmo que o meterRegion em MCA. |
| partNumber partnumber | Não aplicável porque o número de peça não está listado no MCA faturas. Em vez de partnumber, use a combinação de meterId e productOrderName para identificar exclusivamente os preços. |
| totalIncludedQuantity | Não aplicável. |
| pretaxStandardRate  | Não aplicável. |

## <a name="reservation-instance-charge-api-replaced"></a>API de cobrança da instância de reserva substituído

Você pode obter cobrança transações para as compras de reserva com o [API de encargo de instância reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). A nova API inclui todas as compras, incluindo as ofertas do Marketplace de terceiros. Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, consulte [Introdução ao REST](/rest/api/azure/#create-the-request). A API encargo de instância reservado é substituído pela API de transações.

Para obter a reserva de transações de compra com a API de transações:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Recomendações de APIs substituídas

APIs de recomendações de compra de instância reservado fornecem o uso da máquina virtual nos últimos 7, 30 ou 60 dias. As APIs também fornecem recomendações de compra de reserva. Elas incluem:

- [API de recomendação de instância reservada de compartilhado](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [API de recomendações de única instância reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, consulte [Introdução ao REST](/rest/api/azure/#create-the-request). As recomendações de reserva as APIs listadas anteriormente são substituídas pela [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API.

Para obter recomendações de reserva com a API de recomendações de reserva:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>APIs de uso de reserva substituído

Você pode obter o uso de reserva em um registro com a API de uso de instância reservada. Se houver mais de uma instância reservada em um registro, você também pode obter o uso de todas as compras de instância reservada com essa API.

Elas incluem:

- [Detalhes de uso de instância reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)
- [Resumo de uso de instância reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, consulte [Introdução ao REST](/rest/api/azure/#create-the-request). As recomendações de reserva as APIs listadas anteriormente são substituídas pela [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) e [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) APIs.

Para obter detalhes da reserva com a API de detalhes de reserva:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Para obter os resumos de reserva com a API de resumos de reserva:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Mover do Cloudyn para o gerenciamento de custos

As organizações que usam [Cloudyn](https://cloudyn.com) deve começar a usar [gerenciamento de custos do Azure](https://azure.microsoft.com/services/cost-management/) para quaisquer necessidades de gerenciamento de custo. Gerenciamento de custos está disponível no portal do Azure com uma latência de oito horas e não há integração. Para obter mais informações, consulte o [documentação do gerenciamento de custos](index.yml).

Com o gerenciamento de custos do Azure, você pode:

- Exibir os custos ao longo do tempo em relação a um orçamento predefinido. Analise os padrões de custo para identificar e parar anomalias de gastos diariamente. Divida os custos por marcas, grupo de recursos, serviços e localização.
- Crie orçamentos para definir limites sobre o uso e os custos e ser notificado quando os limites de importantes são aproximados. Configure a automação com grupos de ação para disparar eventos personalizados e impor limites de disco rígidos do seu jeito.
- Otimize o custo e o uso com as recomendações do Assistente do Azure. Descubra otimizações de compra com reservas, diminuir o tamanho de máquinas de virtuais subutilizadas e excluir recursos não utilizados para permanecer dentro do orçamento.
- Agende uma exportação de dados de uso e custo para publicar um arquivo CSV para sua conta de armazenamento diariamente. Automatize a integração com sistemas externos para manter os dados de cobrança em sincronia e atualizado.

## <a name="power-bi-integration"></a>Integração com Power BI

Se você usar o Power BI para relatórios de custo, você precisa fazer a transição para o seguinte:

- Microsoft Azure consumo Insights aplicativo do Power BI
- Conector da área de trabalho do Azure Consumption Insights


O conector é recomendado para organizações que desejam o máximo de flexibilidade. No entanto, o aplicativo do Power BI também está disponível para instalação rápida.

- Instalar o [Microsoft Azure Consumption Insights do Power BI de aplicativo](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)
- [Conectar-se com o conector do Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights)

O pacote de conteúdo de consumo de informações e o conector mais antigos trabalharam em um nível de registro. Ele necessário pelo menos acesso de leitura. O novo aplicativo de consumo Insights Power BI e o novo conector do Azure Consumption Insights estão disponíveis para usuários de perfil de cobrança. Equipes que precisam de opções adicionais para analisar os custos ou exibir os custos entre os perfis de cobrança devem usar [análise de custo](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis) o portal do Azure.

## <a name="next-steps"></a>Próximas etapas

- Leia as [documentação do gerenciamento de custos](index.yml) para aprender a monitorar e controlar os gastos do Azure. Ou, se você deseja otimizar o uso de recursos com o gerenciamento de custos.
