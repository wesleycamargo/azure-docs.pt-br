---
title: Usando o portal do Azure Stack | Microsoft Docs
description: Saiba como acessar e usar o portal do usuário no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: efemmano
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 026e37534169fda8dbbf5a568f72046bc6ff55f7
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252040"
---
# <a name="use-the-azure-stack-portal"></a>Usar o portal do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode usar o portal do Azure Stack para assinar as ofertas públicas e usar os serviços que fornecem essas ofertas. Se você já usou o portal do Azure global, você já está familiarizado com o funcionamento do site.

## <a name="access-the-portal"></a>Acessar o portal

O operador do Azure Stack (um provedor de serviços ou um administrador em sua organização), permitem que você souber a URL correta para acessar o portal.

- Para um sistema integrado, a URL varia com base na região de seu operador e o nome de domínio externo e será no formato https://portal.&lt; *região*&gt;.&lt; *FQDN*&gt;.
- Se você estiver usando o Kit de desenvolvimento do Azure Stack, o endereço do portal é https://portal.local.azurestack.external.
- O fuso horário de padrão para todas as implantações do Azure Stack é definido para o tempo Universal Coordenado (UTC). Você pode selecionar um fuso horário ao instalar o Azure Stack, no entanto, ele será revertido automaticamente para UTC como padrão durante a instalação.

## <a name="customize-the-dashboard"></a>Personalizar o painel

O painel contém um conjunto padrão de blocos. Você pode selecionar **Editar painel** para modificar o painel padrão ou selecione **novo painel** para criar um painel personalizado. Você pode personalizar facilmente um painel, adicionando ou removendo os blocos. Por exemplo, para adicionar um bloco de computação, selecione **+ criar um recurso**. Clique com botão direito **Compute**e, em seguida, selecione **fixar no painel**.

![Captura de tela do portal do usuário do Azure Stack](media/azure-stack-use-portal/userportal.png)

## <a name="create-subscription-and-browse-available-resources"></a>Criar a assinatura e procurar recursos disponíveis

Se você ainda não tiver uma assinatura, a primeira coisa que você precisa fazer é assinar uma oferta. Depois disso, você pode procurar os recursos disponíveis. Para procurar e criar recursos, use qualquer uma das seguintes abordagens:

- Selecione o **Marketplace** lado a lado no painel.
- Sobre o **todos os recursos** lado a lado, selecione **criar recursos**.
- No painel de navegação à esquerda, selecione **+ criar um recurso**.

## <a name="learn-how-to-use-available-services"></a>Saiba como usar os serviços disponíveis

Se você precisar de orientação sobre como usar os serviços disponíveis, pode haver diferentes opções disponíveis para você.

- Sua organização ou provedor de serviços pode fornecer sua própria documentação, que normalmente é o caso se eles oferecem serviços personalizados ou aplicativos.
- Aplicativos de terceiros têm sua própria documentação.
- Para os serviços do Azure consistente, é altamente recomendável que você primeiro examine a documentação do Azure Stack. Para acessar a documentação do usuário do Azure Stack, selecione o ícone de Ajuda e, em seguida, selecione **ajuda + suporte**.

    ![Ajuda e suporte de opção na interface do usuário](media/azure-stack-use-portal/HelpAndSupport.png)

    Em particular, sugerimos que você examine os artigos a seguir para começar a usar:

    - [Considerações importantes: Usando os serviços ou criação de aplicativos para o Azure Stack](azure-stack-considerations.md)
    - No **usar serviços** seção da documentação, há um artigo de considerações para cada serviço. A página de considerações descreve as diferenças entre o serviço oferecido no Azure e o mesmo serviço oferecidos no Azure Stack. Por exemplo, consulte [considerações sobre VM](azure-stack-vm-considerations.md). Pode haver outras informações na **usar serviços** seção que é exclusiva para o Azure Stack.

      Você pode usar a documentação do Azure como referência geral para um serviço, mas você deve estar ciente dessas diferenças. Entender que a documentação contém links sobre o **tutoriais de início rápido** bloco aponte para documentação do Azure.

## <a name="get-support"></a>Obtenha suporte

Se você precisar de suporte, entre em contato com seu provedor de serviço ou da organização para obter ajuda.

Se você estiver usando o Kit de desenvolvimento do Azure Stack, o [Fórum do Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) é a única fonte de suporte.

## <a name="next-steps"></a>Próximas etapas

[Considerações importantes: Usando os serviços ou criação de aplicativos para o Azure Stack](azure-stack-considerations.md)
