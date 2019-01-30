---
title: Relatório de uso infraestrutura para provedores de serviços de nuvem do Azure Stack | Microsoft Docs
description: O Azure Stack inclui a infraestrutura necessária para controlar o uso para locatários atendidos por um provedor de serviços de nuvem (CSP), conforme ele ocorre e os encaminha para o Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 1b8b83491211ae26493a6bb41c7f0f219f47f620
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241297"
---
# <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>Uso de infraestrutura de relatórios para provedores de serviços de nuvem

O Azure Stack inclui a infraestrutura necessária para controlar o uso conforme ele ocorre e os encaminha para o Azure. No Azure, comércio do Azure processa os dados de uso e os encargos de uso para as assinaturas do Azure apropriadas. Isso ocorre da mesma forma que o acompanhamento de uso é monitorado na nuvem do Azure global.

Você deve observar que determinados conceitos são consistentes entre o Azure global e o Azure Stack. O Azure Stack tem assinaturas locais, que atender a uma função semelhante a uma assinatura do Azure. Assinaturas locais são válidas apenas localmente. Assinaturas locais são mapeadas para as assinaturas do Azure quando uso é encaminhado para o Azure.

O Azure Stack tem medidores de uso local. Uso local é mapeado para os medidores usados no comércio do Azure. No entanto, as IDs de medidor são diferentes. Há mais medidores disponíveis localmente que a Microsoft usa para cobrança.

Há algumas diferenças entre como os serviços são cobrados no Azure Stack e no Azure. Por exemplo, no Azure Stack, a cobrança pelas VMs apenas baseia-se em/horas do vcore, com a mesma taxa de todas as séries VM, ao contrário do Azure. O motivo é que, no Azure global diferentes preços refletem um hardware diferente. No Azure Stack, o cliente fornece o hardware, portanto, não há nenhum motivo para cobrar taxas diferentes para diferentes classes VM.

Você pode descobrir sobre os medidores de pilha do Azure usados no comércio e seus preços no Partner Center, da mesma forma como você faria para serviços do Azure:

1. No Partner Center, vá para o **menu do painel** > **preços e ofertas**.
2. Sob **com base no uso de serviços**, selecione **atual**.
3. Abra o **do Azure na lista de preços CSP Global** planilha.
4. Filtrar **região = Azure Stack**.

## <a name="usage-and-billing-error-codes"></a>Uso e códigos de erro de cobrança

As mensagens de erro a seguir podem ser encontradas ao adicionar locatários em um registro.

| Erro                           | Detalhes                                                                                                                                                                                                                                                                                                                           | Comentários                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **RegistrationNotFound**            | O registro fornecido não foi encontrado. Verifique se que as informações a seguir foram fornecidas corretamente:<br>1. Identificador de assinatura (valor fornecido: _identificador de assinatura_),<br>2. Grupo de recursos (valor fornecido: _grupo de recursos_),<br>3. Nome do registro (valor fornecido: _nome do registro_).                             | Esse erro normalmente ocorre quando as informações que aponta para o registro inicial não estão corretas. Se você precisar verificar se o grupo de recursos e o nome do seu registro, você pode localizá-la no portal do Azure, listando todos os recursos. Se você encontrar mais de um recurso de registro, examine os **CloudDeploymentID** em propriedades e selecione o registro cujo **CloudDeploymentID** corresponde da sua nuvem. Para localizar o **CloudDeploymentID**, você pode usar este PowerShell no Azure Stack:<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| **BadCustomerSubscriptionId**       | Fornecido _identificador de assinatura do cliente_ e o _nome do registro_ identificador de assinatura não pertencem ao mesmo provedor de serviços de nuvem de Microsoft. Verifique se o identificador de assinatura de cliente está correto. Se o problema persistir, contate o Suporte. | Esse erro ocorre quando a assinatura do cliente é uma assinatura do CSP, mas ele acumula um parceiro CSP diferente ao qual a assinatura usada no registro inicial acumula. Essa verificação é feita para evitar uma situação que resultaria na cobrança de um parceiro CSP que não é responsável para a pilha do Azure usada.                                                                                                                                                                                                                                                                          |
| **InvalidCustomerSubscriptionId**   | O '_identificador de assinatura de cliente_' não é válido. Certifique-se de que uma assinatura válida do Azure é fornecida.                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| **CustomerSubscriptionNotFound**    | _Identificador de assinatura de cliente_ não foi encontrado em _registration nome. Certifique-se de uma assinatura válida do Azure está sendo usada e que o identificador de assinatura foi adicionado para o registro usando a operação PUT.                                                   | Esse erro ocorre ao tentar verificar que um locatário foi adicionado a uma assinatura, e a assinatura do cliente não foi encontrada a ser associado com o registro. O cliente não tiver sido adicionado ao registro ou a ID da assinatura foi escrita incorretamente.                                                                                                                                                                                                                                                                                                                                |
| **UnauthorizedCspRegistration**     | Fornecido _nome do registro_ não seja aprovado para usar a multilocação. Envie um email para azstCSP@microsoft.com e incluir seu nome de registro, grupo de recursos e o identificador de assinatura usado no registro.                                                                                    | Um registro precisa ser aprovado para multilocação pela Microsoft antes de começar a adição de locatários a ele.                                                                                                                                                                                                                                                                                                                                                                                             |
| **CustomerSubscriptionsNotAllowed** | Não há suporte para operações de assinatura de cliente para clientes desconectados. Para usar esse recurso, registre novamente com o licenciamento de pagamento que você usar.                                                                                                                                                                    | O registro para o qual você está tentando adicionar locatários é um registro de capacidade; ou seja, quando o registro foi criado, o parâmetro `BillingModel Capacity` foi usado. Só paga pelo que você use registros têm permissão para adicionar os locatários. É necessário registrar novamente usando o parâmetro `BillingModel PayAsYouUse`.                                                                                                                                                                                                                                                                                          |
| **InvalidCSPSubscription**          | Fornecido _identificador de assinatura de cliente_ não é uma assinatura válida do CSP. Certifique-se de que uma assinatura válida do Azure é fornecida.                                                                                                                                                        | Isso ocorre provavelmente porque a assinatura de cliente que está sendo digitado incorretamente.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **MetadataResolverBadGatewayError** | Um dos servidores upstream retornou um erro inesperado. Tente novamente mais tarde. Se o problema persistir, contate o Suporte.                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="terms-used-for-billing-and-usage"></a>Termos usados para cobrança e uso

Os termos e conceitos a seguir são usados para uso e cobrança do Azure Stack:

| Termo | Definição |
| --- | --- |
| Parceiros CSP direto | Um parceiro direto do provedor de solução de nuvem (CSP) recebe uma nota fiscal diretamente da Microsoft para uso do Azure e o Azure Stack e listas de clientes diretamente. |
| CSP indireto | Revendedores indiretos trabalhar com um provedor indireto (também conhecido como um distribuidor). Os revendedores recrutar clientes finais; o provedor indireto mantém a relação de cobrança com a Microsoft gerencia a cobrança do cliente e fornece serviços adicionais, como suporte ao produto. |
| Cliente final | Os clientes finais são as empresas e agências do governo que possuem os aplicativos e outras cargas de trabalho que são executados no Azure Stack. |

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o programa CSP, consulte [programa Cloud Solution Provider](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Para saber mais sobre como recuperar informações de uso de recursos do Azure Stack, consulte [uso e cobrança do Azure Stack](azure-stack-billing-and-chargeback.md).
