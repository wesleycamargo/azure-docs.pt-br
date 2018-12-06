---
title: 'Solução de problemas: criar, conectar a um espaço de trabalho do Machine Learning Studio - Azure | Microsoft Docs'
description: Este guia fornece soluções para alguns desafios encontrados com frequência quando você configura espaços de trabalho do Azure Machine Learning Studio.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 1a8aec4b-35f9-44e8-9570-2575b8979ab1
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 5e52abeeef7d40ac5594f4edfa2b80ef553081e4
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52312037"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-studio-workspace"></a>Guia de solução de problemas: criar e conectar a um espaço de trabalho do Machine Learning Studio
Este guia fornece soluções para alguns desafios encontrados com frequência quando você configura espaços de trabalho do Azure Machine Learning Studio.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>Proprietário do workspace
Para abrir um workspace no Machine Learning Studio, você deve estar conectado à Conta da Microsoft usada para criar o workspace, ou receber um convite do proprietário para ingressar no workspace. No portal do Azure você pode gerenciar o workspace, que inclui a capacidade de alterar o proprietário e configurar o acesso.

Para obter mais informações sobre como gerenciar um workspace, consulte [Gerenciar um workspace do Azure Machine Learning].

[Gerenciar um workspace do Azure Machine Learning]: manage-workspace.md

## <a name="allowed-regions"></a>Regiões permitidas
No momento, o Machine Learning está disponível em um número limitado de regiões. Se sua assinatura não incluir uma dessas regiões, talvez você receba a mensagem de erro "Você não tem assinaturas nas regiões permitidas".

Para solicitar a adição de uma região à sua assinatura, crie uma nova solicitação de suporte da Microsoft no portal do Azure, escolha o tipo de problema **Cobrança** e siga os prompts para enviar sua solicitação.

## <a name="storage-account"></a>Conta de armazenamento
O serviço de Machine Learning precisa de uma conta de armazenamento para armazenar dados. Você pode usar uma conta de armazenamento existente, ou pode criar uma nova conta de armazenamento ao criar o novo workspace de Machine Learning (se você tiver cota para criar uma nova conta de armazenamento).

Criado o novo workspace do Machine Learning, você pode entrar no Machine Learning Studio com a conta da Microsoft usada para criar o workspace. Se você encontrar a mensagem de erro "Workspace Não Encontrado" (semelhante à captura de tela a seguir), use as etapas a seguir para excluir os cookies do navegador.

![Workspace não encontrado][screen3]

**Para excluir cookies do navegador**

1. Se você usa o Internet Explorer, clique no botão **Ferramentas** no canto superior direito e selecione **Opções da Internet**.  

![Opções da Internet][screen4]

2. Na guia **Geral**, clique em **Excluir…**

![Guia Geral][screen5]

3. Na caixa de diálogo **Excluir Histórico de Navegação**, selecione **Cookies e dados de sites** e clique em **Excluir**.

![Excluir cookies][screen6]

Depois que os cookies forem excluídos, reinicie o navegador e vá para a página [Microsoft Azure Machine Learning](https://studio.azureml.net) . Quando forem solicitados nome de usuário e senha, insira os dados da mesma conta da Microsoft usada para criar o workspace.

## <a name="comments"></a>Comentários

Nosso objetivo é tornar a experiência do Machine Learning o mais simples possível. Poste comentários e problemas no [Fórum do Azure Machine Learning](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para nos ajudar a atendê-lo melhor.

[screen1]:media/troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/troubleshooting-creating-ml-workspace/screen6.png
