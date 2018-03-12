---
title: "Relatório de uso infraestrutura para provedores de serviços de nuvem do Azure pilha | Microsoft Docs"
description: "A pilha do Azure inclui a infraestrutura necessária para controlar o uso conforme ela ocorre e as encaminha para o Azure."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 4ac808e0e85b1daeb54a3f2fd7bec0a7c10aa13e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
## <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>Uso de infraestrutura de relatório para provedores de serviços de nuvem

A pilha do Azure inclui a infraestrutura necessária para controlar o uso conforme ela ocorre e as encaminha para o Azure. No Azure, Azure Commerce processa os dados de uso e encargos de uso para as assinaturas do Azure apropriadas, da mesma maneira como o uso que ocorre na nuvem do Azure global.

Você deve estar ciente de que determinados conceitos são consistentes entre global Azure e a pilha do Azure. A pilha do Azure tem assinaturas locais, que atendem a uma função semelhante a uma assinatura do Azure. Assinaturas locais são válidas apenas localmente. Assinaturas locais são mapeadas para as assinaturas do Azure quando uso é encaminhado para o Azure.

A pilha do Azure tem metros de uso local. Uso local é mapeado para os medidores usados no comércio do Azure. No entanto, as IDs de medidor são diferentes. Há mais metros localmente que a Microsoft usa para cobrança.

Há algumas diferenças entre como os serviços são cobrados na pilha do Azure e o Azure. Por exemplo, na pilha do Azure, o custo de VMs é com base apenas na vcore/horas, com a mesma taxa de todas as séries VM, ao contrário do Azure. O motivo é que, no Azure global diferentes preços refletem um hardware diferente. Na pilha do Azure, o cliente fornece o hardware, portanto não há nenhum motivo para cobrar taxas diferentes para diferentes classes VM.

Você pode obter informações sobre os medidores de pilha do Azure usados em comércio e os preços no Centro de parceiros, da mesma maneira como você faria para serviços do Azure:

1. No Centro de parceiros, vá até o **menu do painel** > **preços e ofertas**.
2. Em **serviços baseados em uso**, selecione **atual**.
3. Abra o **do Azure na lista de preços do CSP Global** planilha.
4. Filtrar **região = pilha Azure**.

## <a name="usage-and-billing-error-codes"></a>Uso e códigos de erro de cobrança

As seguintes mensagens de erro podem ser encontradas quando adicionando locatários para um registro.

| Erro                           | Detalhes                                                                                                                                                                                                                                                                                                                           | Comentários                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RegistrationNotFound            | O registro fornecido não foi encontrado. Verifique se que as informações a seguir foi fornecidas corretamente:<br>1. Identificador de assinatura (valor fornecido: _identificador de assinatura_),<br>2. Grupo de recursos (valor fornecido: _grupo de recursos_),<br>3. Nome do registro (valor fornecido: _nome de registro_).                             | Esse erro normalmente ocorre quando as informações que apontam para o registro inicial não estão corretas. Se você precisar verificar se o grupo de recursos e o nome de seu registro, você pode localizá-la no portal do Azure, listando todos os recursos. Se você encontrar mais de um recurso de registro, examinar o CloudDeploymentID nas propriedades e selecione o registro cujo CloudDeploymentID corresponde da sua nuvem. Para localizar o CloudDeploymentID, você pode usar este PowerShell na pilha do Azure:<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| BadCustomerSubscriptionId       | Fornecido _identificador de assinatura de cliente_ e _nome de registro_ identificador de assinatura não pertencem ao mesmo provedor de serviços de nuvem de Microsoft. Verifique se o identificador de assinatura de cliente está correto. Se o problema persistir, contate o Suporte. | Esse erro ocorre quando a assinatura de cliente é um CSP, mas ele faz o acúmulo para um parceiro CSP diferente ao qual a assinatura usada no registro inicial acumula. Essa verificação é feita para evitar uma situação que pode resultar em um parceiro do CSP que não é responsável pela pilha do Azure usado de cobrança.                                                                                                                                                                                                                                                                          |
| InvalidCustomerSubscriptionId   | O '_identificador de assinatura de cliente_' não é válido. Certifique-se de que uma assinatura do Azure válida é fornecida.                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| CustomerSubscriptionNotFound    | _Identificador de assinatura de cliente_ não foi encontrado em nome de _registration. Certifique-se de uma assinatura válida do Azure está sendo usada e que o identificador de assinatura foi adicionado para o registro usando a operação PUT.                                                   | Esse erro ocorre ao tentar verificar que um locatário foi adicionado a uma assinatura, e a assinatura do cliente não foi encontrada para ser associado com o registro. O cliente não tiver sido adicionado ao registro ou a ID de assinatura foi escrita incorretamente.                                                                                                                                                                                                                                                                                                                                |
| UnauthorizedCspRegistration     | Fornecido _nome do registro_ não foi aprovado para usar multilocação. Envie um email para azstCSP@microsoft.com e inclua o nome de registro, grupo de recursos e o identificador de assinatura usado no registro.                                                                                    | Um registro precisa ser aprovado para multilocação pela Microsoft antes de iniciar a adição de inquilinos a ele. Consulte a seção registrando locatários neste documento para obter mais explicações.                                                                                                                                                                                                                                                                                                                                                                                                             |
| CustomerSubscriptionsNotAllowed | Operações de assinaturas de cliente não têm suporte para os clientes desconectados. Para usar esse recurso, registre novamente com o pagamento como você usar o licenciamento.                                                                                                                                                                    | O registro para o qual você está tentando adicionar locatários é um registro de capacidade, ou seja, quando o registro foi criado, o parâmetro de capacidade de BillingModel foi usada. Pague apenas ao usar registros são permitidos para adicionar os locatários. É necessário registrar novamente usando o parâmetro BillingModel PayAsYouUse.                                                                                                                                                                                                                                                                                          |
| InvalidCSPSubscription          | Fornecido _identificador de assinatura de cliente_ não é uma assinatura válida do CSP. Certifique-se de que uma assinatura do Azure válida é fornecida.                                                                                                                                                        | Isso tem maior probabilidade de haver a assinatura do cliente que está sendo digitado incorretamente.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| MetadataResolverBadGatewayError | Um dos servidores upstream retornou um erro inesperado. Tente novamente mais tarde. Se o problema persistir, contate o suporte.                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="terms-used-for-billing-and-usage"></a>Termos usados para cobrança e uso

Os termos e conceitos a seguir são usados para uso e cobrança na pilha do Azure:

| Termo | Definição |
| --- | --- |
| Parceiro direto de CSP | Um parceiro direto do provedor de solução de nuvem (CSP) recebe uma fatura diretamente da Microsoft para uso do Azure e a pilha do Azure e listas clientes diretamente. |
| CSP indireta | Revendedores indiretas trabalhar com um provedor indireto (também conhecido como um distribuidor). Os revendedores recrutamento clientes finais; o provedor indireto mantém a relação de cobrança com a Microsoft gerencia a cobrança do cliente e fornece serviços adicionais, como suporte ao produto. |
| Cliente final | Clientes finais são a empresas e órgãos do governo que possuem os aplicativos e outras cargas de trabalho que são executados na pilha do Azure. |

## <a name="next-steps"></a>Próximas etapas

 - Para saber mais sobre o programa CSP, consulte [programa do provedor de soluções de nuvem](https://partnercenter.microsoft.com/en-us/partner/programs).
 - Para saber mais sobre como recuperar informações de uso do recurso da pilha do Azure, consulte [uso e cobrança no Azure pilha](/azure-stack-billing-and-chargeback.md).
