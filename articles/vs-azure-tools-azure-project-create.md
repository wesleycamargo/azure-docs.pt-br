---
title: Criando um projeto do Azure com o Visual Studio | Microsoft Docs
description: Criando um projeto do Azure com o Visual Studio
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d933117cf513436e04c79a335998f2ddba848dc4


---
# <a name="creating-an-azure-project-with-visual-studio"></a>Criando um projeto do Azure com o Visual Studio
As Ferramentas do Azure para Visual Studio fornecem um modelo que permite criar um serviço de nuvem do Azure. As ferramentas também ajudarão você a configurar, depurar e implantar o serviço de nuvem no Azure.

Uma solução de serviço de nuvem do Azure contém os seguintes tipos de projetos:

* **Projeto do Azure**
  
    O projeto do Azure tem associações aos projetos de função na solução. Ele também inclui arquivos de configuração de serviço e de definição do serviço. O arquivo de definição de serviço define as configurações de tempo de execução de seu aplicativo, incluindo as funções que são necessárias, pontos de extremidade e tamanho da máquina virtual. O arquivo de configuração de serviço define quantas instâncias de uma função são executadas e os valores das configurações definidas para uma função. Consulte [Como configurar as funções para um Serviço de Nuvem do Azure com o Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md)para obter mais informações sobre essas configurações.
* **Projeto de função web**
  
    Uma função de trabalho executa o processamento em segundo plano. Uma função de trabalho pode se comunicar com os serviços de armazenamento e com outros serviços baseados na Internet. Uma função de trabalho pode ter qualquer número de pontos de extremidade HTTP, HTTPS ou TCP.
  
  * **Função web ASP.NET**, para criar um aplicativo ASP.NET com um front-end da web
  * **Função web do ASP.NET MVC5**
  * **Função web do ASP.NET MVC4**
  * **Função web do ASP.NET MVC3**
  * **Função web do serviço WCF**, para a criação de um serviço WCF
  * **Função web do Silverlight Business Application** (requer o Visual Studio 2012)
* **Função de trabalho de cache**
  
    Uma função que fornece um cache dedicado ao seu aplicativo.
* **Função de trabalho com fila do barramento de serviço**
  
    Uma fila do barramento de serviço que fornece o recurso de enfileiramento de mensagens para se comunicar com o processo de trabalho. Para obter mais informações, consulte [Como usar as Filas do Service Bus](http://go.microsoft.com/fwlink/?LinkId=260560).

## <a name="to-create-an-azure-cloud-service-project-in-visual-studio"></a>Abra o projeto de serviço de nuvem do Azure no Visual Studio
1. Inicie o Microsoft Visual Studio como um administrador.
2. Na barra de menus, escolha **Arquivo**, **Novo**, **Projeto**.
3. No painel **Tipos de Projeto**, escolha **Nuvem** nos nós do modelo de projeto do Visual C# ou Visual Basic.
4. No painel **Modelos**, escolha **Serviço de Nuvem do Azure**.
5. Especifica qual versão do .NET Framework você deseja usar para desenvolver o projeto.
6. Insira um nome e local para seu projeto e um nome para a solução. Escolha o botão **OK** .
7. Na caixa de diálogo **Novo projeto do Azure** , escolha as funções que você deseja adicionar e escolha o botão de seta para a direita para adicioná-las à sua solução. Você pode adicionar quantas funções desejar.
8. Para renomear uma função que você adicionou ao seu projeto, passe o mouse sobre a função na caixa de diálogo **Novo Projeto do Azure** e escolha o ícone **Renomear** à direita da função. Você também pode renomear uma função em sua solução após ela ter sido adicionada.




<!--HONumber=Nov16_HO3-->


