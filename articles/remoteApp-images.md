<properties title="What is in the RemoteApp template images?" pageTitle="O que são as imagens de modelo do RemoteApp?" description="Saiba sobre as imagens modelo incluídas com o RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="mbaldwin" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#O que são as imagens de modelo do RemoteApp?

Sua assinatura do RemoteApp do Azure inclui três imagens de modelo:


- Windows Server 2012
- Microsoft Office 365 ProPlus (assinatura do Office 365 necessária)
- Microsoft Office 2013 Professional Plus (somente avaliação)

Independentemente da imagem você usar, existem considerações de licenciamento toda vez que você compartilhar um aplicativo com os usuários. Confira os [Detalhes de licenciamento do RemoteApp](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-licensing/) para obter mais informações.

Leia mais para obter detalhes sobre o que cada imagem contém.

##Windows Server 2012 R2 ("a imagem baunilha")
Essa imagem é baseada no sistema operacional para Data Center Microsoft Windows Server 2012 R2 e tem as seguintes funções e recursos instalados para atender aos requisitos de imagens de modelo do RemoteApp do Azure: 


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


##Microsoft Office 365 ProPlus (assinatura necessária)
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

Toda a funcionalidade dos aplicativos do Office 365 ProPlus está disponível apenas para os usuários que têm um plano do Office 365 ProPlus. Para obter mais detalhes sobre os planos de assinatura do Office 365 consulte[Planos de serviço do Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx). Para obter mais detalhes sobre licenciamento no RemoteApp, consulte [Como funciona o licenciamento no RemoteApp do Azure?](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-licensing/) 

##Microsoft Office 2013 Professional Plus (somente avaliação)
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

**Informações legais importantes:** essa imagem não inclui uma licença do Microsoft Office e *não pode ser usada para produção*. A imagem do Office 2013 Professional Plus é destinada apenas para o uso de avaliação. Se você quiser usar os aplicativos do Office no RemoteApp do Azure para produção, você precisa usar a imagem do Office 365 ProPlus. Para obter mais detalhes sobre licenciamento no RemoteApp, consulte [Como funciona o licenciamento no RemoteApp do Azure?](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-licensing/)

<!--HONumber=35.2-->
