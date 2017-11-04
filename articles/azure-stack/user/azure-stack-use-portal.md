---
title: Usando o portal do Azure pilha | Microsoft Docs
description: "Saiba como acessar e usar o portal do usuário na pilha do Azure."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 5aa00123-5b87-45e0-a671-4165e66bfbc6
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 63c270affca31d3db7e03116f5e287d8569b0dae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-stack-portal"></a>Usando o portal de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Como um consumidor de serviços de pilha do Azure, use o portal de pilha do Azure para se inscrever para ofertas públicas e usar os serviços que são disponibilizados por meio dessas ofertas. Se você usou o portal do Azure antes, você já está familiarizado com a interface do usuário.

## <a name="access-the-portal"></a>Acessar o portal

O operador de pilha do Azure (um provedor de serviço ou um administrador na sua organização), permitirá que você souber a URL correta para acessar o portal. 

- Para um sistema integrado, a URL varia com base na região do operador e o nome de domínio externo e será em https://portal o formato. &lt; *região*&gt;.&lt; *FQDN*&gt;.
- Se você estiver usando o Kit de desenvolvimento de pilha do Azure, o endereço do portal é https://portal.local.azurestack.external.

![Captura de tela do portal do usuário de pilha do Azure](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Personalizar o painel

O painel contém um conjunto de blocos padrão. Você pode clicar em **Editar painel** para modificar o painel padrão ou clique em **novo painel** adicionar painéis personalizados. Você pode adicionar facilmente blocos ao painel. Por exemplo, você pode clicar em **novo**, clique com botão direito **de computação**e, em seguida, clique em **fixar no painel**.

## <a name="create-subscription-and-browse-available-resources"></a>Criar assinatura e procurar recursos disponíveis
 
Se você ainda não tiver uma assinatura, a primeira coisa que você precisa fazer é assinar uma oferta. Depois disso, você pode procurar a quais recursos estão disponíveis para você. Para procurar e criar recursos, siga um destes procedimentos:

- Clique o **Marketplace** bloco no painel. 
- Sobre o **todos os recursos** lado a lado, clique em **criar recursos**.
- No painel de navegação esquerdo, clique em **novo**.

## <a name="learn-how-to-use-available-services"></a>Saiba como usar os serviços disponíveis

Se você precisar de orientação sobre como usar os serviços disponíveis, pode haver diferentes opções disponíveis para você.

- Sua organização ou um provedor de serviços pode fornecer sua própria documentação. Isso é especialmente verdadeiro se eles oferecem aplicativos ou serviços personalizados.
- Aplicativos de terceiros têm sua própria documentação.
- Para serviços do Azure consistentes, é altamente recomendável que você primeiro examine a documentação de pilha do Azure. Para acessar a documentação de usuário de pilha do Azure, clique no ícone de Ajuda e, em seguida, clique em **ajuda + suporte**.
 
    ![Captura de tela da opção de Ajuda e suporte na interface de usuário](media/azure-stack-use-portal/HelpAndSupport.png)

    Em particular, sugerimos que você analise os artigos a seguir para começar:

    - [Considerações de chave: usando os serviços ou criação de aplicativos para a pilha do Azure](azure-stack-considerations.md)
    - A seção "Usar serviços" da documentação, você verá cada serviço do Azure consistente listado. Há um tópico "Considerações sobre" para cada serviço, que descreve os deltas entre o serviço oferecido no Azure e o mesmo serviço oferecido na pilha do Azure. Para obter um exemplo, consulte [considerações de VM](azure-stack-vm-considerations.md). Pode haver outras informações na seção "Usar serviços" que é exclusiva para a pilha do Azure. 
     
      Você pode usar a documentação do Azure como referência geral para um serviço, mas você deve estar atento essas diferenças. Entender que a documentação contém links no **tutoriais** bloco aponte para documentação do Azure.

## <a name="get-support"></a>Obtenha suporte

Se você precisar de suporte adicional, entre em contato com seu provedor de serviço ou da organização para obter assistência. 

Se você estiver usando o Kit de desenvolvimento de pilha do Azure, o [Fórum do Azure pilha](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) é o único método de suporte.

## <a name="next-steps"></a>Próximas etapas

[Considerações de chave: usando os serviços ou criação de aplicativos para a pilha do Azure](azure-stack-considerations.md)
