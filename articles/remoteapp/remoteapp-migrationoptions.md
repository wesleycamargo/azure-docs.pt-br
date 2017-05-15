---
title: "Opções de migração para fora do Azure RemoteApp | Microsoft Docs"
description: "Saiba mais sobre as opções de migração para fora do Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: c4e0e5bc-5c13-4487-b1b6-ebf2a5edc1f0
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 11254a9bd82885701b457ba3b4ec0ef0aad710e6
ms.contentlocale: pt-br
ms.lasthandoff: 04/27/2017


---
# <a name="options-for-migrating-out-of-azure-remoteapp"></a>Opções de migração fora do Azure RemoteApp
> [!IMPORTANT]
> O Azure RemoteApp será descontinuado até 31 de agosto de 2017. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.


Se você deixou de usar o Azure RemoteApp devido ao [comunicado de desativação](https://go.microsoft.com/fwlink/?linkid=821148) ou porque você terminou sua avaliação, você precisa migrar do Azure RemoteApp para outro serviço de aplicativo. Há duas abordagens diferentes para a migração: uma implantação autogerenciada (geralmente chamada de IaaS [infraestrutura como serviço]) ou uma oferta totalmente gerenciada (geralmente chamada de PaaS/SaaS [plataforma como serviço ou software como serviço]). 

O IaaS de autoatendimento é uma implantação do tipo faça você mesmo que é gerenciada, operada e de sua propriedade, implantada diretamente em VMs (máquinas virtuais) ou sistemas físicos. Na outra extremidade, uma oferta PaaS/SaaS totalmente gerenciada é mais como o Azure RemoteApp – um parceiro fornece uma camada de serviço sobre uma solução de comunicação remota que manipula a parte operacional e de manutenção enquanto você, como o cliente, faz algum gerenciamento de imagem e o aplicativo.

[Consulte os webinars do Azure RemoteApp sobre opções de migração](https://social.msdn.microsoft.com/Forums/azure/40557aaa-3e9f-403c-b221-ad3eac10dc56/migration-option-webinar-recordings?forum=AzureRemoteApp) ou continue lendo este artigo para obter mais informações (incluindo exemplos das diferentes opções de hospedagem).

## <a name="self-managed-iaas-solutions"></a>Soluções (IaaS) autogerenciadas
### <a name="rds-on-iaas"></a>**RDS em IaaS**
Você pode implantar uma implantação de Serviços de Área de Trabalho Remota (no Windows Server) com base em sessão nativa usando o RemoteApp ou áreas de trabalho locais ou em um ambiente hospedado (como em VMs do Azure). Os RDS em implantações de IaaS são melhores para os clientes já familiarizados com eles e que já têm experiência técnica com implantações de RDS. 

> [!NOTE]
> Você precisa de Licenciamento por Volume com SA (Software Assurance) para licenças de acesso para cliente RDS para usar essa opção de implantação.

A implantação dos RDS na VMs do Azure é mais fácil do que nunca quando você usa modelos de aplicação de patch e de implantação (leia uma [visão geral](https://blogs.technet.microsoft.com/enterprisemobility/2015/07/13/azure-resource-manager-template-for-rds-deployment/) e [obtenha-as](https://aka.ms/rdautomation)). Você pode obter os mesmos recursos de colocação em escala elástica com recursos de modelo de implantação clássico do Azure (e não recursos de Modelo de Recursos do Azure) no Azure RemoteApp usando o [script de colocação em escala automática](https://gallery.technet.microsoft.com/scriptcenter/Automatic-Scaling-of-9b4f5e76), embora haja mais personalizações e configurações. Quando você implanta os RDS em VMs do Azure, o suporte é fornecido por meio do [Suporte do Azure](https://azure.microsoft.com/support/plans/), os mesmos profissionais de suporte que deram suporte para o Azure RemoteApp a você. Você pode obter estimativas de custo com base no seu uso existente entrando em contato com o [Suporte do Azure](https://azure.microsoft.com/support/plans/) ou você mesmo pode executar cálculos usando uma Calculadora de Custos a ser lançada em breve.  Além disso, com as VMs da série N (atualmente no modo de visualização privada) você pode adicionar vGPU – saiba mais sobre como adicionar vGPU e sobre como [aproveitar as melhorias de RDS no Windows Server 2016](https://myignite.microsoft.com/videos/2794) em nossa sessão Ignite.   

Temos os guias de implantação passo a passo para [Windows Server 2012 R2](http://aka.ms/rdsonazure) e [Windows Server 2016](http://aka.ms/rdsonazure2016) para ajudá-lo em sua implantação. Confira o [blog de Área de Trabalho Remota](https://blogs.technet.microsoft.com/enterprisemobility/?product=windows-server-remote-desktop-services) para obter as notícias mais recentes.

### <a name="citrix-on-iaas"></a>**Citrix no IaaS**
Uma implantação de Citrix nativa de XenApp ou XenDesktop com base em sessão pode ser implantada localmente ou em um ambiente hospedado (como em VMs do Azure). 

Consulte o guia passo a passo de implantação, [Citrix XA 7.6 no Azure](http://www.citrixandmicrosoft.com/Documents/Citrix-Azure Deployment Guide-v.1.0.docx), para obter mais informações. Leia mais sobre [Citrix no Azure](http://www.citrixandmicrosoft.com/Solutions/AzureCloud.aspx), incluindo uma calculadora de preços. Você também pode encontrar um [contato do Citrix](http://citrix.com/English/contact/index.asp) com o qual discutir suas opções.

## <a name="fully-managed-paassaas-offerings"></a>Ofertas (PaaS/SaaS) totalmente gerenciadas

### <a name="awingu"></a>Awingu
O Awingu fornece uma solução de espaço de trabalho online simples que executa aplicativos herdados, SaaS e documentos em um navegador com html5. Disponibilizando, dessa forma, os aplicativos com segurança em qualquer tipo de dispositivo. Para serviços SaaS, há uma ampla variedade de opções de logon único disponível. Também é possível integrar profundamente diversos sistemas de arquivos (nuvem) ao seu espaço de trabalho. Além de mobilidade completa, o sofisticado espaço de trabalho online do Awingu proporciona segurança ideal com controles granulares (por exemplo, download/upload), auditoria de uso completa, Autenticação Multifator (por exemplo, a MFA do Azure), gravação da sessão e muito mais. Desde a configuração inicial, o Awingu habilita o compartilhamento de documentos e aplicativos para colaboração otimizada e segura.
A solução do Awingu é uma API livre multilocatário e com diversos AD. Ele é usado por pequenas e grandes empresas, provedores de serviços de nuvem e [ISVs](http://www.isv2saas.com). Esses clientes apreciam especialmente a facilidade de uso, de instalação e o baixo TCO.

O Awingu All-in-One está [disponível no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/awingu.awingu-arm) com 2 usuários simultâneos internos. Licenças adicionais estão disponíveis por meio de uma [ampla gama de distribuidores e revendedores](http://www.awingu.com/reseller).

Saiba mais sobre o [Awingu em como alternativa ao Azure RemoteApp](http://alternative-for-azure-remoteapp.awingu.com/).


> Localização primária: Bélgica
> 
> Regiões de operação: EMEA, América do Norte e Brasil
> 
> Oferece soluções de área de trabalho e RemoteApp com base em sessão: sim, ambos 
> 
> **Global**:
> 
> Arnaud Marlière, CMO
> 
> Email: [arnaud@awingu.com](mailto:arnaud@awingu.com)
> 
> Telefone: +1 646 583 3025
> 
> **EC da Bélgica**:
> 
> Ottergemsesteenweg-Zuid 808 B44
> 
> 9000 Gent
> 
> Email: [info@awingu.com](mailto:info@awingu.com) 
> 
> Telefone: +32 9 296 40 11
> 
> **EUA**:
> 
> 7th floor, 1177 Ave of the Americas,
> 
> New York, NY 10036
> 
> Email: [info.us@awingu.com](mailto:info.us@awingu.com)

### <a name="citrix-xenapp-essentials-released-april-2017"></a>Citrix XenApp Essentials (lançado em abril de 2017)
Disponível agora no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Citrix.XenAppEssentials), o Citrix XenApp Essentials é o novo serviço de virtualização de aplicativo, combinando o poder e a flexibilidade da plataforma de nuvem Citrix com a pesquisa visual simples, prescritiva e de fácil consumo do Microsoft Azure RemoteApp.  

Os clientes existentes do Azure RemoteApp podem [registrar-se para uma avaliação gratuita](https://www.citrix.com/global-partners/microsoft/remote-app.html).  Observação: somente o encargo de serviço do usuário do Citrix é gratuito, os custos de computação e armazenamento do Azure ainda se aplicam

Saiba mais sobre o [Citrix XenApp Essentials](https://www.citrix.com/global-partners/microsoft/remote-app.html).

### <a name="citrix-cloud-xenapp-service-and-xendesktop-service"></a>Citrix Cloud XenApp Service e XenDesktop Service 

[Citrix Cloud XenApp Service e XenDesktop Service](https://www.citrix.com/products/citrix-cloud/services.html) é a melhor solução para a entrega de aplicativos e áreas de trabalho, além de recursos avançados de gerenciamento e monitoramento. 

### <a name="citrix-service-provider-program"></a>Programa Citrix Service Provider
O Programa de Provedores de Serviços da Citrix facilita para que provedores de serviços entreguem a simplicidade da computação em nuvem virtual para SMBs, oferecendo a eles os serviços que desejam em um modelo fácil e pré-pago. Provedores de Serviços da Citrix aumentam seus negócios Microsoft SPLA e expandem seus investimentos na plataforma RDS com qualquer dispositivo, acesso em qualquer local, o mais amplo suporte de aplicativo, uma experiência avançada, segurança adicional e maior escalabilidade. Por sua vez, provedores Citrix atraem mais assinantes, aumentam a satisfação do cliente e reduzem seus custos operacionais. [Saiba mais](http://www.citrix.com/products/service-providers.html) ou [encontre um parceiro](https://www.citrix.com/buy/partnerlocator.html).

### <a name="frame"></a>Frame

Organizações de TI corporativas e governamentais, provedores de serviços gerenciados e principais fornecedores de software escolhem o Frame para criar e gerenciar seus espaços de trabalho definidos por software na nuvem. Em organizações de pequeno à grande porte, o Frame facilita incrivelmente o acesso dos usuários aos aplicativos dos Windows em qualquer navegador de qualquer dispositivo. A plataforma Frame inclui tudo que um administrador precisa para implantar aplicativos da nuvem, incluindo a infraestrutura do Azure e as licenças RDS (trazer sua própria conta e licenças do Azure é opcional). 

Saiba sobre o [Frame no Azure](https://www.fra.me/ara). 

> Localização primária: San Mateo, CA, EUA
>
> Região de operação: em todo o mundo
>
> Parceiro da Microsoft: Sim
> 
> Telefone: 1-480-269-4668

### <a name="microsoft-hosted-service-provider"></a>Provedor de serviços hospedados da Microsoft
Parceiros de hospedagem normalmente oferecem um serviço de aplicativo e da área de trabalho do Windows hospedado e totalmente gerenciado, que pode incluir o gerenciamento de recursos do Azure, sistemas operacionais, aplicativos e assistência técnica usando os contratos de licenciamento do parceiro com a Microsoft e outros fornecedores de software, além de ser um Contrato de Licença de Provedor de Serviços para permitir a revenda de SAL (licenças de acesso do assinante). As informações a seguir fornecem detalhes e informações de contato para alguns dos hosters especializados em ajudar os clientes com a sua migração do Azure RemoteApp. Verifique [a lista atual de provedores de serviços hospedados](http://aka.ms/rdsonazurecertified) que concluíram o roteiro de aprendizagem e avaliação do RDS no IaaS.  

#### <a name="aspex"></a>ASPEX
O [ASPEX](http://www.aspex.be/en) é especialista em transição de ISVs para a nuvem e de ISVs buscando otimizar suas configurações de nuvem atuais. O ASPEX oferece uma ampla variedade de serviços gerenciados, DeVOps e serviços de consultoria.  

> Localização primária: Antuérpia, Bélgica
> 
> Região de operação: Europa Ocidental
> 
> Status de parceiro: [prata](https://partnercenter.microsoft.com/pcv/solution-providers/aspex_9397f5dd-ebdd-405b-b926-19a5bda61f7a/cfe00bac-ea36-4591-a60b-ec001c4c3dff)
> 
> Provedor de Serviços Microsoft Cloud: sim
> 
> Oferece soluções de área de trabalho e RemoteApp com base em sessão: sim, ambos
> 
> Soluções de migração do Azure RemoteApp: sim, [saiba mais](https://www.aspex.be/en/azure-remote-apps)
> 
> Telefone: +3232202198
> 
> Email: [info@aspex.be](mailto:info@aspex.be)
> 
> Web: [http://cloud.aspex.be/contact-ara-0](http://cloud.aspex.be/contact-ara-0)

#### <a name="conexlink-platform-name-mycloudit"></a>Conexlink (nome da plataforma: MyCloudIT)
[MyCloudIT](https://mycloudit.com) é uma plataforma de automação para as empresas de TI simplificarem, otimizarem e dimensionarem a migração e a entrega de áreas de trabalho remotas, aplicativos remotos e infraestrutura na Nuvem do Microsoft Azure. 

A plataforma MyCloudIT reduz o tempo de implantação em 95%, os custos do Azure em 30% e move toda a infraestrutura de TI do seu cliente para a nuvem pressionando apenas algumas teclas. Parceiros agora podem gerenciar os clientes de um painel global, prestar serviços aos usuários finais em todo o mundo como nunca antes e aumentar as receitas sem adicionar uma sobrecarga adicional nem muito treinamento do Azure.  

> Localização primária: Dallas, Texas, EUA
> 
> Região de operação: em todo o mundo
> 
> Status de parceiro: [ouro](https://partnercenter.microsoft.com/pcv/solution-providers/conexlink_4298787366/843036_1?k=Conexlink)
> 
> Provedor de Serviços Microsoft Cloud: sim
> 
> Oferece soluções de área de trabalho e RemoteApp com base em sessão: sim, ambos
> 
> Soluções de migração do Azure RemoteApp: sim, [saiba mais](https://mycloudit.com/remote-app-microsoft/)
> 
> Brian Garoutte, vice-presidente de desenvolvimento de negócios
> 
> Telefone: 972-218-0741
>   
> Email: [brian.garoutte@conexlink.com](mailto:brian.garoutte@conexlink.com)

#### <a name="acuutech"></a>Acuutech
A [Acuutech](http://www.acuutech.com) é especialista no fornecimento de soluções de área de trabalho hospedadas, entregando experiências de aplicativo de área de trabalho completa e os aplicativos ISV criadas com base em tecnologia da Microsoft para uma base de clientes global do Azure e seus próprios datacenters.

> Localização primária: Londres, Reino Unido; Cingapura; Houston, Texas
> 
> Região de operação: em todo o mundo
> 
> Status de parceiro: ouro
> 
> Provedor de Serviços Microsoft Cloud: sim
> 
> Oferece soluções de área de trabalho e RemoteApp com base em sessão: sim, ambos
> 
> Soluções de migração do Azure RemoteApp: sim, [saiba mais](http://www.acuutech.com/ara-migration/)
> 
> **Reino Unido**:
>   
> 5/6 York House, Road Langston,
>   
> Loughton, Essex IG10 3TQ
>   
> Telefone: +44 (0) 20 8502 2155
> 
> **Cingapura**:
>   
> 100 Cecil Street, #09-02, 
>   
> Todo o mundo, Cingapura, 069532
> 
> Telefone: +65 6709 4933
>   
> **América do Norte**:
>   
> 3601 S. Sandman St.
>   
> Suite 200, Houston, Texas 77098
>   
> Telefone: +1 713 691 0800

#### <a name="saasplaza"></a>**SaaSplaza**
O [SaaSplaza](http://www.saasplaza.com/) oferece um portfólio completo do Microsoft Dynamics (NAV, AX, GP, SL, CRM) em nuvem privada e pública (Azure).

> Localização primária: Países Baixos
> 
> Região de operação: em todo o mundo
> 
> Status de parceiro: [ouro](https://partnercenter.microsoft.com/pcv/solution-providers/saasplaza_4295495801/791011_2?k=saasplaza)
> 
> Provedor de Serviços Microsoft Cloud: sim
> 
> Oferece soluções de área de trabalho e RemoteApp com base em sessão: sim, ambos
> 
> **EMEA**:
> 
> Prins Mauritslaan 29-35
> 
> 71 LP Badhoevedorp
> 
> Países Baixos
> 
> Telefone: +31 20 547 8060 
> 
>  **Américas**:
> 
> 171 Saxony Road, Suite 105
> 
> Encinitas, CA 92024
> 
> San Diego
> 
> Estados Unidos
> 
> Telefone: +1 858 385 8900 
> 
> **APAC**:
> 
> 105 Cecil Street
>    
> \#11-08, The Octagon
> 
> Cingapura 069534
> 
> Cingapura
>   
> Telefone – Cingapura: +65 6222 6591
> 
> Telefone – Austrália: +61 2 8310 5568 
>    
> Telefone – Nova Zelândia: +64 4 488 0321
> 
## <a name="need-more-help"></a>Precisa de mais ajuda?
Ainda precisa de ajuda escolhendo ou tem mais perguntas? Use um dos seguintes métodos para obter ajuda. 

1. Envie-nos um email para [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2. Entre em contato com o [Suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Comece abrindo um [caso de Suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
3. Ligue para nós. [Encontre um número de vendas local](https://azure.microsoft.com/overview/sales-number/).


