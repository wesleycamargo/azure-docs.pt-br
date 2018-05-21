---
title: Usando o portal do Azure pilha | Microsoft Docs
description: Saiba como acessar e usar o portal do usuário na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: mabrigg
ms.reviewer: efemmano
ms.openlocfilehash: 2ebafc4249f8455bdbe45a07a5bf88aa8984d67a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="using-the-azure-stack-portal"></a>Usando o portal de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode usar o portal de pilha do Azure para se inscrever para ofertas públicas e usar os serviços que fornecem essas ofertas. Se você usou o portal do Azure global, você já está familiarizado com como funciona o site.

## <a name="access-the-portal"></a>Acessar o portal

O operador de pilha do Azure (um provedor de serviço ou um administrador na sua organização), permitirá que você souber a URL correta para acessar o portal.

- Para um sistema integrado, a URL varia com base na região do operador e o nome de domínio externo e será no formato https://portal.&lt; *região*&gt;.&lt; *FQDN*&gt;.
- Se você estiver usando o Kit de desenvolvimento de pilha do Azure, o endereço do portal é https://portal.local.azurestack.external.

![Captura de tela do portal do usuário de pilha do Azure](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Personalizar o painel

O painel contém um conjunto padrão de blocos. Você pode clicar em **Editar painel** para modificar o painel padrão ou clique em **novo painel** para criar um painel personalizado. Você pode personalizar facilmente um painel, adicionando ou removendo blocos. Por exemplo, para adicionar um bloco de computação, clique em **novo**. Clique com botão direito **de computação**e, em seguida, clique em **fixar no painel**.

## <a name="create-subscription-and-browse-available-resources"></a>Criar assinatura e procurar recursos disponíveis
 
Se você ainda não tiver uma assinatura, a primeira coisa que você precisa fazer é assinar uma oferta. Depois disso, você pode procurar os recursos disponíveis. Para procurar e criar recursos, use qualquer uma das seguintes abordagens:

- Clique o **Marketplace** bloco no painel.
- Sobre o **todos os recursos** lado a lado, clique em **criar recursos**.
- No painel de navegação esquerdo, clique em **novo**.

## <a name="learn-how-to-use-available-services"></a>Saiba como usar os serviços disponíveis

Se você precisar de orientação sobre como usar os serviços disponíveis, pode haver diferentes opções disponíveis para você.

- Sua organização ou um provedor de serviços pode fornecer sua própria documentação, que normalmente é o caso se eles oferecem aplicativos ou serviços personalizados.
- Aplicativos de terceiros têm sua própria documentação.
- Para serviços do Azure consistentes, é altamente recomendável que você primeiro examine a documentação de pilha do Azure. Para acessar a documentação de usuário de pilha do Azure, clique no ícone de Ajuda e, em seguida, clique em **ajuda + suporte**.
 
    ![Captura de tela da opção de Ajuda e suporte na interface de usuário](media/azure-stack-use-portal/HelpAndSupport.png)

    Em particular, sugerimos que você analise os artigos a seguir para começar:

    - [Considerações de chave: usando os serviços ou criação de aplicativos para a pilha do Azure](azure-stack-considerations.md)
    - No **usar serviços** seção da documentação, há um artigo de considerações para cada serviço. A página considerações descreve as diferenças entre o serviço oferecido no Azure e o mesmo serviço oferecido na pilha do Azure. Para obter um exemplo, consulte [considerações de VM](azure-stack-vm-considerations.md). Pode haver outras informações no **usar serviços** seção que é exclusiva para a pilha do Azure.
     
      Você pode usar a documentação do Azure como referência geral para um serviço, mas você deve estar atento essas diferenças. Entender que a documentação contém links no **tutoriais** bloco aponte para documentação do Azure.

## <a name="get-support"></a>Obtenha suporte

Se você precisar de suporte, contate o provedor de serviço ou da organização para obter ajuda.

Se você estiver usando o Kit de desenvolvimento de pilha do Azure, o [Fórum do Azure pilha](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) é a única fonte de suporte.

## <a name="next-steps"></a>Próximas etapas

[Considerações de chave: usando os serviços ou criação de aplicativos para a pilha do Azure](azure-stack-considerations.md)
