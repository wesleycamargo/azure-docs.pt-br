---
title: Guia de Publicação da Oferta de Máquina Virtual para o Azure Marketplace
description: Este artigo descreve os requisitos para publicar uma máquina virtual e uma avaliação gratuita de software para implantação no Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 5508b5943e116545297d91e85621d2a11a635299
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39058068"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Guia de Publicação da Oferta de Máquina Virtual

As imagens de Máquina Virtual são uma das principais formas de publicar uma solução no Azure Marketplace. Use este guia para compreender os requisitos dessa oferta. 

Essas são ofertas de transação que são implantadas e cobradas pelo Marketplace. A chamada à ação que um usuário vê é "Obtenha agora".

## <a name="free-trial"></a>Avaliação gratuita 

Você pode providenciar para que os usuários testem sua oferta acessando licenças de software com período de validade limitado usando o modelo de cobrança BYOL (Traga sua própria licença). Abaixo estão os requisitos para implantar essa oferta. 

|Requisitos  |Detalhes  |
|---------|---------|
|Período de avaliação gratuita e experiência de avaliação     |   Seu cliente pode experimentar o aplicativo gratuitamente por um tempo limitado. Seu cliente não precisa pagar valores de licença ou de assinatura por sua oferta. Seu cliente não precisa pagar pelo produto ou serviço interno subjacente da Microsoft. Todas as opções da versão de avaliação são implantadas em sua assinatura do Azure. Você tem o controle exclusivo do gerenciamento e da otimização de custos. Você deve escolher uma avaliação gratuita ou uma demonstração interativa. Independente do que escolher, sua avaliação gratuita deverá fornecer ao cliente uma quantidade de tempo pré-definida para experimentar a oferta sem custo adicional.|
|Solução facilmente configurável, pronta para uso    |  Seu aplicativo deve ser rápido para configurar e fácil de usar.       |
|Disponibilidade/tempo de atividade    |    Seu aplicativo ou plataforma SaaS deve ter um tempo de atividade de pelo menos 99,9%.     |
|Azure Active Directory     |    Sua oferta deve permitir o SSO (logon único) federado do Azure AD (Azure Active Directory) (SSO federado do Azure AD) com o consentimento habilitado.     |

## <a name="test-drive"></a>Test drive

Implante uma ou mais máquinas virtuais por meio de IaaS (infrastructure-as-a-service) ou aplicativos SaaS. Uma vantagem da opção de publicação de test drive é o provisionamento automático de uma máquina virtual ou da solução inteira mostrada por um tour guiado pelo parceiro hospedado. Um test drive oferece uma avaliação sem custo adicional para o seu cliente. Seu cliente não precisa ser um cliente do Azure existente para aproveitar a experiência de avaliação. 

Envie um email para amp-testdrive@microsoft.com para começar. 

|Requisitos  |Detalhes |
|---------|---------|
| Você tem um aplicativo do Marketplace   |    Um ou mais máquinas de virtuais por meio de IaaS ou SaaS.      |

## <a name="interactive-demo"></a>Demonstração Interativa

Você fornece uma experiência interativa de sua solução aos clientes usando uma demonstração interativa. A vantagem da opção de publicação de demonstração interativa é que você fornece uma experiência de avaliação sem o provisionamento complicado da solução complexa. 

## <a name="virtual-machine-offer"></a>Oferta de Máquina Virtual

Use o tipo de oferta de Máquina Virtual quando implantar um dispositivo virtual na assinatura associada ao cliente. As VM são totalmente habilitadas para venda usando modelos de licença BYOL (traga sua própria licença) e pagamento conforme o uso. A Microsoft hospeda a transação comercial e cobra o cliente em seu nome. Você tem a vantagem de usar a relação de pagamento preferencial entre o cliente e a Microsoft, incluindo os Contratos Enterprise.

>[!NOTE]
>Neste momento, todos os compromissos pecuniários associados a um Contrato Enterprise podem ser usados em relação ao uso do Azure por sua VM, mas não em relação aos valores de licenciamento de seu software.  

>[!NOTE]
>Você pode restringir a descoberta e a implantação de sua VM a um conjunto específico de clientes publicando a imagem e o preço como uma oferta Privada. Ofertas privadas permitem que você crie ofertas exclusivas para seus clientes mais próximos e oferecer software e termos personalizados a eles. Os termos personalizados permitem a você destacar uma variedade de cenários, incluindo negócios de campos especializados com preços e termos especializados, e também acesso antecipado a software de versão limitada. As ofertas privadas permitem que você ofereça preços específicos ou produtos a um conjunto limitado de clientes criando um novo SKU com esses detalhes.  
*   Para obter mais informações sobre as Ofertas Privadas, visite Ofertas Privadas na página do Azure Marketplace localizada em [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Requisito | Detalhes |  
|:--- |:--- | 
| Cobrança e medição | Sua VM deve dar suporte à cobrança mensal BYOL ou Pagamento Conforme o Uso. |  
| VHD (disco rígido virtual) compatível com Azure | As VMs devem ser criadas em Windows ou Linux.<ul> <li>Para obter mais informações sobre como criar um VHD do Linux, visite a seção Criar um VHD compatível com o Azure (baseado em Linux) localizada em [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Para obter mais informações sobre como criar um VHD do Windows, visite a seção Criar um VHD compatível com o Azure (baseado em Windows) localizada em [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul> |  

## <a name="next-steps"></a>Próximas etapas

Caso ainda não tenha feito isso, 

- [registre-se](https://azuremarketplace.microsoft.com/sell) no Marketplace

Se você estiver registrado e estiver criando uma nova oferta ou trabalhando em uma existente,

- [Faça logon no Portal do Cloud Partner](https://cloudpartner.azure.com) para criar ou concluir a oferta
