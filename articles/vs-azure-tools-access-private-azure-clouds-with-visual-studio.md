---
title: Acesso a nuvens privadas do Azure com o Visual Studio | Microsoft Docs
description: Saiba como acessar recursos de nuvem privada usando o Visual Studio.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/13/2017
ms.author: ghogen
ms.openlocfilehash: 09f74e1e43d63fbd1a8fd4ddea7b23f8dc9370db
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2018
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Acessando nuvens privadas do Azure com o Visual Studio

Por padrão, o Visual Studio dá suporte a pontos de extremidade REST de nuvem do Azure. Neste artigo, você aprende a usar o certificado da nuvem privada para acessar e interagir com a nuvem privada no Visual Studio.

1. No Portal do Azure da nuvem privada, faça o download do arquivo de configurações de publicação ou contate o administrador para obter um arquivo de configurações de publicação. (O arquivo tem a extensão `.publishsettings`.)

1. No **Gerenciador de Servidores** do Visual Studio, clique com o botão direito do mouse no nó **Azure** e selecione **Gerenciar e Filtrar Assinaturas**.

    ![Comando Gerenciar assinaturas](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. Na caixa de diálogo **Gerenciar Assinaturas do Microsoft Azure**, selecione a guia **Certificados** e, em seguida, **Importar**.

    ![Importação de certificados do Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. Na caixa de diálogo **Importar Assinaturas do Microsoft Azure**, selecione **Procurar**.

    ![Botão Procurar na caixa de diálogo Importar Assinaturas do Microsoft Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. Na caixa de diálogo **Abrir**, procure o diretório em que você salvou o arquivo de configurações de publicação, selecione o arquivo e, em seguida, selecione **Abrir**.

    ![Selecionar o arquivo de configurações de publicação](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. Ao retornar para a caixa de diálogo **Importar Assinaturas do Microsoft Azure**, selecione **Importar**.

    ![Importar o arquivo de configurações de publicação](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    Os certificados são importados do arquivo de configurações de publicação para o Visual Studio, e agora é possível interagir com os recursos de nuvem privada.

