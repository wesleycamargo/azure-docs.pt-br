<properties
    pageTitle="O que são as imagens de modelo do RemoteApp?"
    description="Saiba mais sobre as imagens de modelo incluídas no RemoteApp."
    services="remoteapp"
	documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/13/2015" 
    ms.author="elizapo" />

# O que são as imagens de modelo do RemoteApp?

Sua assinatura do RemoteApp do Azure inclui três imagens de modelo:


- Windows Server 2012
- Microsoft Office 365 ProPlus (assinatura do Office 365 necessária)
- Microsoft Office 2013 Professional Plus (somente avaliação)

> [AZURE.IMPORTANT]Independentemente da imagem você usar, existem considerações de licenciamento toda vez que você compartilhar um aplicativo com os usuários. Confira os [Detalhes de licenciamento do RemoteApp](remoteapp-licensing.md) para obter mais informações.

Leia mais para obter detalhes sobre o que cada imagem contém.

## Windows Server 2012 R2 (“a imagem baunilha")
Essa imagem é baseada no sistema operacional para Data Center Microsoft Windows Server 2012 R2 e tem as seguintes funções e recursos instalados para atender aos requisitos de imagens de modelo RemoteApp do Azure:


- .NET framework 4.5, 3.5.1 e 3.5
- Experiência Desktop
- Serviços de tinta e manuscrito
- Media Foundation
- Host de sessão de área de trabalho remota
- Windows PowerShell 4.0
- Windows PowerShell ISE
- Suporte a WoW64

Esta imagem também tem os seguintes aplicativos instalados:

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Microsoft Windows Media Player


## Microsoft Office 365 ProPlus (assinatura necessária)
O Office 365 é o aplicativo mais solicitado, criamos uma imagem "personalizada" para trabalhar com ele.

Essa imagem é uma extensão da imagem baunilha e tem os seguintes componentes do Microsoft Office 365 ProPlus instalados juntamente com os componentes descritos na imagem do Windows Server 2012 R2:


- Access
- Excel
- Lync
- OneNote
- OneDrive for Business
- Outlook
- PowerPoint
- Project
- Visio
- Word
- Revisores de texto do Microsoft Office

E os seguintes aplicativos, assim:

- SQL Native client
- Driver ODBC
- Cliente SQL Server Data Mining
- Cliente MasterDataServices
- Microsoft Publisher
- PowerQuery
- PowerMap


Toda a funcionalidade dos aplicativos do Office 365 ProPlus está disponível apenas para os usuários que têm um plano do Office 365 ProPlus. Para obter mais detalhes sobre os planos de assinatura do Office 365, consulte [Planos de serviço do Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx). Para obter mais detalhes sobre licenciamento no RemoteApp, consulte [Como funciona o licenciamento no RemoteApp do Azure?](remoteapp-licensing.md)

## Microsoft Office 2013 Professional Plus (somente avaliação)
Durante o período de avaliação gratuito, você pode testar o serviço com a imagem do Office 2013.

Essa imagem é uma extensão da imagem baunilha e tem os seguintes componentes do Microsoft Office 2013 ProPlus instalados juntamente com os componentes descritos na imagem do Windows Server 2012 R2:


- Access
- Excel
- Lync
- OneNote
- OneDrive for Business
- Outlook
- PowerPoint
- Project
- Visio
- Word
- Revisores de texto do Microsoft Office

> [AZURE.IMPORTANT]**Importantes informações legais:** Esta imagem não inclui uma licença do Microsoft Office e *não pode ser usada para a produção*. A imagem do Office 2013 Professional Plus é destinada apenas ao uso para avaliação. Se quiser usar os aplicativos do Office no RemoteApp do Azure para produção, você precisará usar a imagem do Office 365 ProPlus. Para obter mais detalhes sobre licenciamento no RemoteApp, consulte [Como funciona o licenciamento no RemoteApp do Azure?](remoteapp-licensing.md)
 

<!---HONumber=July15_HO4-->