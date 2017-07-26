---

title: Migrar do Azure RemoteApp para o Citrix XenApp Essentials | Microsoft Docs
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
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: fcd96a466d1c0dad17d7012308281ef868463b19
ms.contentlocale: pt-br
ms.lasthandoff: 06/02/2017


---

# <a name="migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>Migrar do Azure RemoteApp para o Citrix XenApp Essentials

Há alguns pré-requisitos a considerar se você usar o Azure RemoteApp e desejar migrar para o Citrix XenApp Essentials. Leia primeiro o [Guia de implantação técnica passo a passo do Citrix XenApp Essentials](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf) da Citrix, bem como a sua [biblioteca técnica online](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html). 

## <a name="prerequisite-steps-for-migration"></a>Etapas de pré-requisito para a migração

1. Crie uma nova rede virtual ou determine a rede virtual do Azure no Azure Resource Manager na qual você implantará o Citrix XenApp Essentials. O Azure RemoteApp usa o Portal Clássico do Azure; o Citrix XenApp Essentials dá suporte apenas ao Azure Resource Manager.  
2. Verifique se a rede virtual que você selecionou tem acesso de rede ao seu controlador de domínio, porque a Citrix só dá suporte a implantações híbridas. Se você está usando uma implantação de nuvem do Azure RemoteApp, verifique se sua rede virtual tem acesso de rede a um controlador de domínio do Active Directory. Você também pode usar o Azure AD DS (Azure Active Directory Domain Services). 
3. Verifique se o DNS está configurado corretamente para a rede virtual, para que esse ingresso no domínio seja bem-sucedido em sua primeira tentativa. Você pode criar uma VM (máquina virtual) na rede virtual selecionada e executar um ingresso manual no domínio para verificar se o DNS e o ingresso no domínio funcionam conforme esperado. Isso assegura que você tenha êxito na primeira vez que implantar o Citrix XenApp Essentials. 
4. Se necessário, crie um emparelhamento de rede virtual entre uma rede virtual do Portal Clássico do Azure que você está usando com o Azure RemoteApp e a sua rede virtual do Azure Resource Manager. Esse processo de emparelhamento funciona se as duas redes residem na mesma região. Se não for o caso, use o VPN site a site para conectar as redes virtuais para rede. 
5. Se for necessário, leia [Como migrar dados para dentro e fora do Azure RemoteApp](remoteapp-migrate.md). 
6. Atualize sua imagem existente do Azure RemoteApp para incluir o componente Citrix VDA (para obter instruções, veja a documentação da Citrix). 
7. Vá para o Azure Marketplace e inicie a implantação do Citrix XenApp Essentials.

## <a name="other-considerations"></a>Outras considerações

Lembre-se das seguintes considerações adicionais quando você migrar:
- O Citrix XenApp Essentials só oferece suporte a implantações híbridas. Por outras palavras, ele requer acesso de rede a um controlador de domínio para realizar o ingresso no domínio. Se você estiver usando uma implantação de nuvem do Azure RemoteApp, você precisará usar o Azure AD DS ou garantir que sua rede virtual tenha acesso ao Active Directory para o ingresso no domínio. 
- Para aprender a mover dados de usuário para o Citrix XenApp Essentials, leia [Como migrar dados para dentro e fora do Azure RemoteApp](remoteapp-migrate.md). 
- O Citrix XenApp Essentials somente oferece suporte para contas do Active Directory. Ele não dá suporte a contas da Microsoft (tais como outlook.com, msn.com ou hotmail.com). 

## <a name="citrix-xenapp-essentials-billing"></a>Cobrança do Citrix XenApp Essentials

Leia as [Perguntas Frequentes](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699) e o [artigo de visão geral da Citrix](https://www.citrix.com/global-partners/microsoft/remote-app.html) para obter detalhes completos sobre preços. Há três componentes de cobrança no Citrix XenApp Essentials:

- O encargo de serviço da Citrix, que é US $12 por usuário por mês. Como todas as aquisições do Azure Marketplace, esta é cobrada para o método de pagamento associado à sua assinatura do Azure. Para clientes do EA (Contrato Enterprise), os créditos monetários do Azure não podem ser usados. 
- CALs (licenças de acesso para cliente) do RDS (Serviços de Dados Remotos). No momento, você pode comprar a taxa de acesso remoto que é fornecida em pacote com o pagamento pelo Citrix XenApp Essentials, por US $6,25. Se você for um cliente EA, você poderá usar os créditos monetários do Azure para pagar por isso. Se desejar usar as suas RDS CALs existentes, fale conosco em [arainfo@microsoft.com](mailto:arainfo@microsoft.com) para que possamos aplicar isso à sua fatura. 
- Computação e armazenamento do Azure. Esse é o custo do armazenamento e consumo de computação do Azure para as VMs consumidas. Preste atenção aos preços quando seleciona seu tamanho e densidade de usuários da VM. Se você for um cliente EA, você poderá usar os créditos monetários do Azure para pagar por isso.

Se você ainda tiver perguntas, você poderá:
- Envie-nos um email para [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
- [Contate o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Comece [abrindo um caso de suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para ajudá-lo com as etapas de pré-requisito 1 a 5. Para as etapas 6 e 7, entre em contato com a Citrix abrindo um tíquete de suporte no portal de gerenciamento da Citrix. 

