---
title: "Solução de problemas: criar e conectar-se a um Espaço de Trabalho do Machine Learning | Microsoft Docs"
description: "Soluções para problemas comuns na criação e conexão a um espaço de trabalho de aprendizado de máquina do Azure"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 1a8aec4b-35f9-44e8-9570-2575b8979ab1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 9e738c4e5f43ae6c939f7c6da90c258498943e73
ms.openlocfilehash: b4740816ac61ffad032b86367fef99be7581f4c6
ms.lasthandoff: 12/14/2016


---
# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>Guia de solução de problemas: criar e conectar-se a um espaço de trabalho do Aprendizado de Máquina
Este guia fornece soluções para alguns desafios encontrados com frequência quando você configura espaços de trabalho do Aprendizado de Máquina do Azure.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>Proprietário do espaço de trabalho
Para abrir um espaço de trabalho no Machine Learning Studio, você deve estar conectado à Conta da Microsoft usada para criar o espaço de trabalho, ou receber um convite do proprietário para ingressar no espaço de trabalho. No portal do Azure você pode gerenciar o espaço de trabalho, que inclui a capacidade de alterar o proprietário e configurar o acesso.

Para obter mais informações sobre como gerenciar um espaço de trabalho, consulte [Gerenciar um espaço de trabalho do Aprendizado de Máquina do Azure].

[Gerenciar um espaço de trabalho do Aprendizado de Máquina do Azure]: machine-learning-manage-workspace.md

## <a name="allowed-regions"></a>Regiões permitidas
No momento, o Aprendizado de Máquina está disponível em um número limitado de regiões. Se sua assinatura não incluir uma dessas regiões, talvez você receba a mensagem de erro "Você não tem assinaturas nas regiões permitidas".

Para solicitar a adição de uma região à sua assinatura, crie uma nova solicitação de suporte da Microsoft no portal do Azure, escolha o tipo de problema **Cobrança** e siga os prompts para enviar sua solicitação.

## <a name="storage-account"></a>Conta de armazenamento
O serviço de Aprendizado de Máquina precisa de uma conta de armazenamento para armazenar dados. Você pode usar uma conta de armazenamento existente, ou pode criar uma nova conta de armazenamento ao criar o novo espaço de trabalho de Aprendizado de Máquina (se você tiver cota para criar uma nova conta de armazenamento).

Criado o novo espaço de trabalho do Machine Learning, você pode entrar no Machine Learning Studio com a conta da Microsoft usada para criar o espaço de trabalho. Se você encontrar a mensagem de erro "Espaço de Trabalho Não Encontrado" (semelhante à captura de tela a seguir), use as etapas a seguir para excluir os cookies do navegador.

![Espaço de trabalho não encontrado][screen3]

**Para excluir cookies do navegador**

1. Se você usa o Internet Explorer, clique no botão **Ferramentas** no canto superior direito e selecione **Opções da Internet**.  

![Opções da Internet][screen4]

2. Na guia **Geral**, clique em **Excluir…**

![Guia Geral][screen5]

3. Na caixa de diálogo **Excluir Histórico de Navegação**, selecione **Cookies e dados de sites** e clique em **Excluir**.

![Excluir cookies][screen6]

Depois que os cookies forem excluídos, reinicie o navegador e vá para a página [Aprendizado de Máquina do Microsoft Azure](https://studio.azureml.net) . Quando forem solicitados nome de usuário e senha, insira os dados da mesma conta da Microsoft usada para criar o espaço de trabalho.

## <a name="comments"></a>Comentários

Nosso objetivo é tornar a experiência do Aprendizado de Máquina o mais simples possível. Poste comentários e problemas no [Fórum do Aprendizado de Máquina do Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para nos ajudar a atendê-lo melhor.

[screen1]:media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png

