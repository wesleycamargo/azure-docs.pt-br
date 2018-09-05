---
title: Opções de cobrança no Microsoft Azure Marketplace | Azure
description: Opções de cobrança no Microsoft Azure Marketplace para editores.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: 67806563dcd67876d4d922c7f2c76379f7ad7222
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125506"
---
# <a name="billing-options-in-the-azure-marketplace"></a>Opções de cobrança no Microsoft Azure Marketplace

Este artigo descreve as opções de cobrança disponíveis no [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com).

## <a name="commercial-considerations-in-the-marketplace"></a>Considerações comerciais no Marketplace
O Marketplace não compartilha receita para os seguintes tipos de listagem: 
*   Listar
*   Avaliação
*   Transação usando o modelo de cobrança BYOL (Traga sua própria licença)

Não são cobrados valores adicionais pela participação em vitrines no Marketplace.

Para obter mais informações, consulte [Políticas de Participação do Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies).  

## <a name="pay-as-you-go-and-byol-billing-options"></a>Opções de cobrança BYOL e Pagamento Conforme o Uso
Ao usar um modelo de cobrança de Pagamento Conforme o Uso como opção de publicação, sua receita de licenciamento de software baseada em uso é compartilhada em uma divisão de 80%/20% entre você e a Microsoft. É possível precificar uma única oferta usando os modelos de cobrança BYOL e Pagamento Conforme o Uso. Os dois modelos de cobrança coexistirem no nível de oferta como SKUs separados. Você configura os modelos de cobrança na sua oferta no Portal do Cloud Partner. 

Considere os exemplos a seguir:
*   Se você habilitar a opção Pagamento Conforme o Uso, terá o seguinte resultado:
    | Custo de sua licença | US $ 1,00 por hora |
    |:--- |:--- |
    | Custo de uso do Azure (D1/1-Núcleo) | US $ 0,14 por hora |
    | *O cliente é cobrado pela Microsoft* | *US $ 1,14 por hora* |

    Nesse cenário, a Microsoft fatura US $ 1,14 por hora pelo uso de sua imagem de VM publicada:
    | Faturas da Microsoft | US$ 1,14 por hora |
    |:--- |:--- |
    | A Microsoft paga para você 80% do seu custo de licença | US $ 0,80 por hora |
    | A Microsoft mantém 20% do seu custo de licença | US $ 0,20 por hora |
    | A Microsoft mantém o custo de uso do Azure | US $ 0,14 por hora |

*   Se você habilitar a opção BYOL, terá o seguinte resultado:
    | Custo de sua licença | Valor de licença negociada e faturada por você |
    |:--- |:--- |
    | Custo de uso do Azure (D1/1-Núcleo) | US $ 0,14 por hora |
    | *O cliente é cobrado pela Microsoft* | *US $ 0,14 por hora* |

    Nesse cenário, a Microsoft fatura US$ 0,14 por hora pelo uso da sua imagem de VM publicada: 
    | Faturas da Microsoft | US $ 0,14 por hora |
    |:--- |:--- |
    | A Microsoft mantém o custo de uso do Azure | US $ 0,14 por hora |
    | A Microsoft mantém 0% do seu custo de licença | US $ 0.00 por hora |

## <a name="single-billing-and-payment-methods"></a>Formas de pagamento e cobrança única
Um benefício importante de usar a opção de publicação Transact no Marketplace é que os custos de licenciamento e o uso do Azure são faturados diretamente para seu cliente.

Nesse cenário, a Microsoft cobra e coleta em seu nome. A cobrança da Microsoft elimina a necessidade de criar sua própria relação de compras com o cliente. A cobrança única pode economizar tempo e recursos. Além disso, pode ajudá-lo a concentrar-se em conseguir a venda, em vez realizar cobrança. 

## <a name="enterprise-agreement"></a>Contrato Enterprise  
Se você for um cliente do Microsoft Enterprise Agreement, poderá usar o Contrato Enterprise para pagar por produtos da Microsoft. Você pode ser cobrado por produtos, inclusive pelo uso do Azure. O uso do Contrato Enterprise para pagar é destinado a organizações que queiram licenciar software e serviços de nuvem por três anos ou mais. Você parcela os pagamentos em vez de realizar um pagamento adiantado. Se você usar uma opção de publicação Pagamento Conforme o Uso, a cobrança dos custos de licenciamento de software seguirá o ciclo trimestral de cobrança excedente do Contrato Enterprise.  

### <a name="monetary-commitment"></a>Compromisso monetário
Se você for um cliente do Enterprise Agreement, poderá adicionar o Azure ao seu contrato. Você adiciona o Azure ao contrato fazendo compromisso monetário antecipado com o Azure. O compromisso monetário é consumido ao longo do ano. Seu compromisso inclui qualquer combinação de uso dos serviços do Azure.

## <a name="next-steps"></a>Próximas etapas
Examine o [Guia de publicação do Microsoft Azure Marketplace e AppSource](./marketplace-publishers-guide.md).
