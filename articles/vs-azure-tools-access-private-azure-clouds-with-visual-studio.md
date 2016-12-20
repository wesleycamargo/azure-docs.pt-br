---
title: Acesso a nuvens privadas do Azure com o Visual Studio | Microsoft Docs
description: Saiba como acessar recursos de nuvem privada usando o Visual Studio.
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 26fea09810e4a5ed1dc19123a5354905ec3e37ea


---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Acessando nuvens privadas do Azure com o Visual Studio
## <a name="overview"></a>Visão geral
Por padrão, o Visual Studio dá suporte a pontos de extremidade REST de nuvem pública do Azure. Isso pode ser um problema, no entanto, se você estiver usando o Visual Studio com uma nuvem privada da Azure. Você pode usar certificados para configurar o Visual Studio para acessar os pontos de extremidade REST de nuvem privada do Azure. Você pode obter esses certificados através do arquivo de configurações de publicação do Azure.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Para acessar uma nuvem privada do Azure no Visual Studio
1. No [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885) para a nuvem privada, baixe o arquivo de configurações de publicação ou entre em contato com o administrador para um arquivo de configurações de publicação. Na versão pública do Azure, o link para baixá-lo é [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (O arquivo baixado deve ter uma extensão .publishsettings.)
2. Em **Gerenciador de Servidores** no Visual Studio, escolha o nó do **Azure** e, no menu de atalho, escolha o comando **Gerenciar assinaturas**.
   
    ![Comando Gerenciar assinaturas](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)
3. Na caixa de diálogo **Gerenciar assinaturas do Microsoft Azure**, clique na guia **Certificados** e no botão **Importar**.
   
    ![Importação de certificados do Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)
4. Na caixa de diálogo **Importar assinaturas do Microsoft Azure**, navegue até a pasta em que você salvou o arquivo de configurações de publicação e escolha o arquivo, então selecione o botão **Importar**. Isso importa os certificados no arquivo de configurações de publicação no Visual Studio. Agora, você poderá interagir com seus recursos de nuvem privada.
   
    ![Importação de configurações de publicação](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## <a name="next-steps"></a>Próximas etapas
[Publicando em um Serviço de Nuvem do Azure do Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[Como baixar e importar informações de assinatura e configurações de publicação](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx)




<!--HONumber=Nov16_HO3-->


