<properties 
	pageTitle="Gerenciamento de riscos com acesso condicional" 
	description="Um tópico que explica como permitir acesso de qualquer lugar a recursos específicos de dispositivos conhecidos em conformidade com políticas e não permitir acesso de dispositivos perdidos, roubados ou em não conformidade." 
	services="active-directory, virtual-network" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.devlang="na" 
	ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
	ms.date="05/05/2015" 
	ms.author="Justinha"/>


# Gerenciamento de riscos com acesso condicional

As tendências atuais de como os funcionários trabalham, como eles são produtivos e os meios para fazer seu trabalho estão mudando rapidamente. Os funcionários estão levando em seus dispositivos pessoais para o trabalho. Nesses dispositivos pessoais, eles têm aplicativos que usam em suas vidas pessoais e digitais. E eles estão acostumados com a liberdade e os recursos que isso leva a eles. Eles querem usar esses mesmos aplicativos no trabalho e querem ter a mesma flexibilidade que têm em suas vidas digitais pessoais em sua rotina de trabalho. Os funcionários das empresa hoje em dia esperam trabalhar de qualquer local, em dispositivos de sua preferência e se conectar perfeitamente e acessar os aplicativos de negócios.

Essa flexibilidade de permitir que os usuários trabalhem onde, quando e como eles quiserem traz um risco maior. Muitos pontos de dados para esses dispositivos podem ser roubados, mal posicionados ou perdidos. Em muitos desses smartphones e tablets, há uma quantidade incrível de conteúdos sensíveis e confidenciais de clientes e corporativos. Esse é o ponto de equilíbrio que os arquitetos de TI, especialistas em segurança e administradores de TI estão tentando manter. É esse equilíbrio entre permitir que os usuários sejam produtivos em todos os dispositivos de que gostam, fornecendo ao mesmo tempo o nível apropriado de segurança e proteção de conteúdos corporativos que residem nesses dispositivos.

Com os vários recursos de acesso condicional oferecidos por meio do Azure Active Directory, Office 365 e Microsoft Intune, os administradores de TI podem resolver as seguintes questões:

- Oferecer acesso em qualquer lugar aos funcionários, sem abrir a porta para qualquer pessoa na internet.
- Oferecer acesso em qualquer lugar a recursos específicos a partir de dispositivos conhecidos em conformidade com políticas.
- Proibir acesso de dispositivos perdidos, roubados ou que não estejam em conformidade.

![][1]

## O que vem a seguir?

Os tópicos a seguir abordam cada um dos mecanismos diferentes disponíveis para definir políticas de acesso condicional na sua organização.

- [Visão geral do registro de dispositivos do Azure Active Directory](https://msdn.microsoft.com/library/azure/dn903763.aspx)
- [Configurando o acesso condicional no local usando o registro do dispositivo do Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx)
- [Políticas de dispositivo de acesso condicional para serviços do Office 365](https://msdn.microsoft.com/library/azure/dn903766.aspx)
- [Visualização de acesso condicional do Azure para aplicativos SaaS](https://msdn.microsoft.com/library/azure/dn906877.aspx)


<!--Image references-->
[1]: ./media/active-directory-conditional-access/condaccoverviewvsdx1.png
 

<!---HONumber=62-->