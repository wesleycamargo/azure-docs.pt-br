---
title: "Usando o Emulator Expresso para executar e depurar um serviço de nuvem em um computador local | Microsoft Docs"
description: "Usando o Emulator Express para executar e depurar um serviço de nuvem em um computador local"
services: visual-studio-online
documentationcenter: n/a
author: TomArcher
manager: douge
editor: 
ms.assetid: 73108f98-a552-4817-b7a1-551367b71906
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/15/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b09fb0be256fc4cc832822f676b6d1f9de1813cb


---
# <a name="using-emulator-express-to-run-and-debug-a-cloud-service-on-a-local-machine"></a>Usando o Emulator Express para executar e depurar um serviço de nuvem em um computador local
Ao usar o Emulator Express, você poderá testar e depurar um serviço de nuvem sem executar o Visual Studio como um administrador. É possível definir as configurações do projeto para usar o Emulator Express ou o emulador completo, dependendo dos requisitos do seu serviço de nuvem. Para saber mais sobre o emulador completo, consulte [Executar um aplicativo Azure no emulador de computação](storage/storage-use-emulator.md). O Emulator Express foi incluído primeiro no Azure SDK 2.1 e, a partir do Azure SDK 2.3, é o emulador padrão.

## <a name="using-emulator-express-in-the-visual-studio-ide"></a>Usando o Emulator Express no Visual Studio IDE
Quando você cria um novo projeto no Azure SDK 2.3 ou posterior, o Emulator Express já está selecionado. No caso de projetos existentes que foram criados com uma versão anterior do SDK, siga estas etapas para selecionar o Emulator Express.

### <a name="to-configure-a-project-to-use-emulator-express"></a>Para configurar um projeto para usar o Emulator Express
1. No menu de atalho do projeto do Azure, escolha **Propriedades** e, em seguida, escolha a guia **Web**.
2. Em **Development Server Local**, escolha o botão de opção **Usar o IIS Express**. O Emulator Express não é compatível com o Servidor Web do IIS.
3. Em **Emulador**, escolha o botão de opção **Usar o Emulator Expresso**.
   
    ![Emulator Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## <a name="launching-emulator-express-at-a-command-prompt"></a>Iniciando o Emulator Express em um prompt de comando
Em um prompt de comando, você pode iniciar a versão expressa do Emulador de Computação do Azure, csrun.exe, usando a opção /useemulatorexpress.

## <a name="limitations"></a>Limitações
Antes de usar o Emulator Express, você deve estar ciente de algumas limitações:

* O Emulator Express não é compatível com o Servidor Web do IIS.
* O serviço de nuvem pode conter várias funções, mas cada função é limitada a uma instância.
* Você não pode acessar números de porta abaixo de 1000. Por exemplo, se você usar um provedor de autenticação que normalmente usa uma porta abaixo de 1000, talvez seja necessário alterar esse valor para um número de porta acima de 1000.
* As limitações que se aplicam ao Emulador de Computação do Azure também se aplicam ao Emulator Express. Por exemplo, você não pode ter mais de 50 instâncias de função por implantação. Consulte [Executar um aplicativo Azure no emulador de computação](http://go.microsoft.com/fwlink/p/?LinkId=623050)

## <a name="next-steps"></a>Próximas etapas
[Depurando serviços de nuvem](https://msdn.microsoft.com/library/azure/ee405479.aspx)




<!--HONumber=Nov16_HO3-->


