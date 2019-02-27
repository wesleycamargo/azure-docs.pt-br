---
title: 'Solucionar problemas: Criar, conectar a um workspace do Machine Learning Studio'
titleSuffix: Azure Machine Learning Studio
description: Este guia fornece soluções para alguns desafios encontrados com frequência quando você configura espaços de trabalho do Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7f0485221f0f29d08275f9508ba79e32c3f8924a
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455431"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-workspace"></a>Guia de Solução de Problemas: Criar e conectar a um workspace do Azure Machine Learning Studio
Este guia fornece soluções para alguns desafios encontrados com frequência quando você configura espaços de trabalho do Azure Machine Learning Studio.



## <a name="workspace-owner"></a>Proprietário do workspace
Para abrir um workspace no Machine Learning Studio, você deve estar conectado à Conta da Microsoft usada para criar o workspace, ou receber um convite do proprietário para ingressar no workspace. No portal do Azure você pode gerenciar o workspace, que inclui a capacidade de alterar o proprietário e configurar o acesso.

Para obter mais informações sobre como gerenciar um workspace, confira [Gerenciar um workspace do Azure Machine Learning Studio].

[Gerenciar um workspace do Azure Machine Learning Studio]: manage-workspace.md

## <a name="allowed-regions"></a>Regiões permitidas
No momento, o Machine Learning está disponível em um número limitado de regiões. Se sua assinatura não incluir uma dessas regiões, talvez você receba a mensagem de erro "Você não tem assinaturas nas regiões permitidas".

Para solicitar a adição de uma região à sua assinatura, crie uma nova solicitação de suporte da Microsoft no portal do Azure, escolha o tipo de problema **Cobrança** e siga os prompts para enviar sua solicitação.

## <a name="storage-account"></a>Conta de armazenamento
O serviço de Machine Learning precisa de uma conta de armazenamento para armazenar dados. Você pode usar uma conta de armazenamento existente ou pode criar uma nova conta de armazenamento ao criar o novo workspace de Machine Learning Studio (se você tiver cota para criar uma nova conta de armazenamento).

Criado o novo workspace do Machine Learning Studio, você pode entrar no Machine Learning Studio com a conta da Microsoft usada para criar o workspace. Se você encontrar a mensagem de erro "Workspace Não Encontrado" (semelhante à captura de tela a seguir), use as etapas a seguir para excluir os cookies do navegador.

![Workspace não encontrado](media/troubleshooting-creating-ml-workspace/screen3.png)

**Para excluir cookies do navegador**

1. Se você usa o Internet Explorer, clique no botão **Ferramentas** no canto superior direito e selecione **Opções da Internet**.  

   ![Opções da Internet](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Na guia **Geral**, clique em **Excluir…**

   ![Guia Geral](media/troubleshooting-creating-ml-workspace/screen5.png)

3. Na caixa de diálogo **Excluir Histórico de Navegação**, selecione **Cookies e dados de sites** e clique em **Excluir**.

   ![Excluir cookies](media/troubleshooting-creating-ml-workspace/screen6.png)

Depois que os cookies forem excluídos, reinicie o navegador e vá para a página [Microsoft Azure Machine Learning Studio](https://studio.azureml.net). Quando forem solicitados nome de usuário e senha, insira os dados da mesma conta da Microsoft usada para criar o workspace.

## <a name="comments"></a>Comentários

Nosso objetivo é tornar a experiência do Machine Learning o mais simples possível. Poste comentários e problemas no [Fórum do Azure Machine Learning](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para nos ajudar a atendê-lo melhor.
