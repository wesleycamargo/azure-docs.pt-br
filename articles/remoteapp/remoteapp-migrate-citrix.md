---

title: Como migrar do Azure RemoteApp para o Citrix XenApp Essentials | Microsoft Docs
description: Como migrar do Azure RemoteApp para o Citrix XenApp Essentials
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 695a8165-3454-4855-8e21-f2eb2c61201b
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: de47edc5ea4dc1b67bd69b74929acf249b0d43e6
ms.contentlocale: pt-br
ms.lasthandoff: 05/25/2017


---

# <a name="how-to-migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>Como migrar do Azure RemoteApp para o Citrix XenApp Essentials

Há alguns pré-requisitos para os clientes do Azure RemoteApp que desejem migrar para o Citrix XenApp Essentials.  Recomendamos que leia primeiro o [guia de implantação de técnica passo a passo do Citrix XenApp Essentials](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf) da Citrix, bem como a sua [biblioteca técnica online](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html). 

Felizmente, você pode reutilizar a maior parte dos investimentos que já fez no Azure RemoteApp, mas há alguns pré-requisitos de alto nível para a implantação do XenApp Essentials.

## <a name="prerequisites"></a>Pré-requisitos

1. Crie uma nova VNET, ou determine que VNET do Azure no Azure Resource Manager em que vai implantar o Citrix XenApp Essentials. O Azure RemoteApp usa o Azure clássico; o Citrix XenApp Essentials apenas suporta o Azure Resource Manager.  
2. Verifique se a VNET que selecionou tem acesso de rede ao controlador do seu domínio, dado que o Citrix apenas suporta implantações híbridos. Se estiver usando uma implantação na nuvem do Azure RemoteApp, vai precisar de garantir que sua VNET tem acesso de rede a um controlador de domínio do Azure Directory e podemos recomendar que use os Azure Active Directly Domain Services (AAD-DS). 
3. Verifique se o que DNS está configurado corretamente para a VNET, para que o seu ingresso no domínio seja bem-sucedido à primeira tentativa. Pode criar uma Máquina Virtual na VNET selecionada e executar uma junção de domínio manual para verificar se o DNS e o ingresso no domínio funcionam conforme o esperado. Isso garantirá que sua primeira vez a implantar o Citrix XenApp Essentials foi bem-sucedida. 
4. Se for necessário, crie um emparelhamento entre redes virtuais entre uma rede de virtual clássica que esteja a usar com o Azure RemoteApp, e sua VNET do Azure Resource Manager se estiverem na mesma região e/ou usar um VPN S2S se não estiverem em boas condições para conectar VNETs em rede. 
5. Se for necessário, leia [Como migrar dados para dentro e fora do Azure RemoteApp](remoteapp-migrate.md). 
6. Atualize sua imagem existente do Azure RemoteApp para incluir o componente Citrix VDA, encontra documentação do Citrix nas instruções. 
7. Vá para o Azure Marketplace e inicie a implantação do Citrix XenApp Essentials.

Boa sorte e obrigado por usar o Azure RemoteApp. 

## <a name="other-considerations"></a>Outras considerações:

- O Citrix XenApp Essentials só oferece suporte a implantações híbridas. Por outras palavras, ele requer acesso de rede a um controlador de domínio para realizar o ingresso no domínio. Se você estiver usando uma implantação de nuvem do Azure RemoteApp, você precisará usar o AAD-DS ou garantir que sua VNET tem acesso ao Active Directory para o ingresso no domínio. 
- Para aprender a mover dados de usuário para o CXE, leia [Como migrar dados dentro e fora do Azure RemoteApp](remoteapp-migrate.md). 
- O Citrix XenApp Essentials somente oferece suporte para contas do Active Directory. Não oferece suporte a Contas da Microsoft (@outlook.com, @msn.com, @hotmail.com, etc.). 

## <a name="understanding-billing-for-citrix-xenapp-essentials"></a>Compreendendo a cobrança do Citrix XenApp Essentials 

Leia as [Perguntas Frequentes](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699) e o [artigo de visão geral da Citrix](https://www.citrix.com/global-partners/microsoft/remote-app.html) para obter detalhes completos sobre preços. Há três componentes de cobrança no Citrix XenApp Essentials, que são:

1. O encargo de serviço de $12 por usuário por mês da Citrix. Como todas as aquisições do Azure Marketplace, esta é cobrada para o método de pagamento associado à sua assinatura do Azure. Para clientes do EA, os créditos monetários não podem ser usados. 
2. RDS CAL. Você deve colocar seu próprio RDS CAL (em breve) ou adquirir a Taxa de Acesso Remoto (RAF) que é fornecida com o pagamento de 6,25 US $ do Citrix XenApp Essentials. Para clientes do EA, os créditos monetários não podem ser usados para pagar isso. Se desejar usar as suas RDS CALs existentes, fale conosco [arainfo@microsoft.com] (mailto:arainfo@microsoft.com para que possamos aplicar na sua fatura. 
3. Computação e armazenamento do Azure. Esse é o custo do armazenamento e consumo de computação do Azure para as VMs consumidas. Preste atenção aos preços quando seleciona seu tamanho e densidade de usuários da VM. Para clientes do EA, os créditos monetários não podem ser usados para pagar isso

Se ainda tiver questões, contate-nos
1. Envie-nos um email para [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2. [Contate o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Comece por [abrir um caso de suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para ajudá-lo com as etapas n.º 1 a 5 acima. Entre em contato com a Citrix abrindo um tíquete de suporte no portal de gerenciamento do Citrix para as etapas de n.º 6 e 7. 

