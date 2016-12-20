---
title: "Instalação expressa de emulador para depurar aplicativos dos Serviços de Nuvem no Visual Studio | Microsoft Docs"
description: "Explica como instalar o C++ redistribuível para habilitar o Emulator Express no Visual Studio"
services: cloud-services
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 22b20f7a-23f4-4f7f-b536-3bf1e01adcd1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/02/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 165ab7363efaf90eaab41098f71e2f1b846c346e
ms.openlocfilehash: 05d672dcb1335c617bb8d8cae43947bcd5e9ab3d

---
# <a name="use-emulator-express-to-debug-cloud-services-application-in-vs-2017"></a>Usar o Emulator Express para depurar o aplicativo Serviços de Nuvem no VS 2017
Este artigo explica como usar o Emulator Express para depurar aplicativos dos Serviços de Nuvem no 2017 VS.

## <a name="background-context"></a>Contexto
O Emulator Express é usado por padrão para depuração de funções da Web e de Trabalho dos Serviços de Nuvem no Visual Studio. Essa configuração é especificada na página de propriedades do projeto dos Serviços de Nuvem.

![Abrir propriedades do projeto][0]

![O Emulator Express está selecionado como padrão][1]

O [Visual C++ Redistribuível][Visual C++ Redistribuível] para Visual Studio é exigido pelo Emulador Express. Atualmente, ele não é instalado com a carga de trabalho do Azure. Depois de você pressionar F5 para depurar um aplicativo dos Serviços de Nuvem, o Visual Studio solicitará a instalação desse componente e prosseguirá com a depuração.

![Prompt para instalar o C++ Redistribuível][2]

Clique em Sim para instalar o C++ Redistribuível.

![Instalar o C++ Redistribuível][3]

Pressione F5 novamente para iniciar as sessões de depuração.

![Iniciar a depuração][4]

![Depuração bem-sucedida][5]

> Observação: a instalação do Visual C++ Redistribuível é um esforço único. Se você atualizou de uma versão anterior do SDK do Azure e se tiver instalado o Emulator Express, não encontrará esse problema.
> 
> 

## <a name="manual-workaround"></a>Solução alternativa manual
Você também pode instalar o [Visual C++ Redistribuível][Visual C++ Redistribuível] manualmente e o mesmo efeito será aplicado à foma como o Visual Studio o instalou em seu sistema.

[vcredist_x86.exe][vcredist_x86.exe]

[vcredist_x64.exe][vcredist_x64.exe]

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como usar o Azure Computer Emulator para depurar seus aplicativos dos Serviços de Nuvem no Visual Studio: [Usando o Emulator Express para executar e depurar um serviço de nuvem em um computador local][Usando o Emulator Express para executar e depurar um serviço de nuvem em um computador local]

[Visual C++ Redistribuível]:https://www.microsoft.com/en-us/download/details.aspx?id=30679
[vcredist_x86.exe]:https://download.microsoft.com/download/1/6/B/16B06F60-3B20-4FF2-B699-5E9B7962F9AE/VSU_4/vcredist_x86.exe
[vcredist_x64.exe]:https://download.microsoft.com/download/1/6/B/16B06F60-3B20-4FF2-B699-5E9B7962F9AE/VSU_4/vcredist_x64.exe
[Usando o Emulator Express para executar e depurar um serviço de nuvem em um computador local]:https://azure.microsoft.com/en-us/documentation/articles/vs-azure-tools-emulator-express-debug-run/

[0]: ./media/cloud-services-emulator-express-fix/vs-05.png
[1]: ./media/cloud-services-emulator-express-fix/vs-06.png
[2]: ./media/cloud-services-emulator-express-fix/vs-01.png
[3]: ./media/cloud-services-emulator-express-fix/vs-02.png
[4]: ./media/cloud-services-emulator-express-fix/vs-03.png
[5]: ./media/cloud-services-emulator-express-fix/vs-04.png



<!--HONumber=Nov16_HO3-->


