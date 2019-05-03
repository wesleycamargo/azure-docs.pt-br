---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 05/06/2019
ms.openlocfilehash: 623e993dfbe6bbb3297fa6470865ab1a04f55b37
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028584"
---
>[!IMPORTANT]
>Você pode usar os recursos que você criou como pré-requisitos para outros tutoriais do serviço de Azure Machine Learning e artigos de instruções.


### <a name="delete-everything"></a>Excluir tudo

Se você não planeja usar qualquer outra coisa que você criou, exclua o grupo de recursos inteiro para não incorrer em encargos:

1. No portal do Azure, selecione **grupos de recursos** no lado esquerdo da janela.
 
   ![Excluir um grupo de recursos no portal do Azure](./media/aml-ui-cleanup/delete-resources.png)

1. Na lista, selecione o grupo de recursos que você criou.

1. No lado direito da janela, selecione o botão de reticências (**...** ).

1. Selecione **Excluir grupo de recursos**.

Também é excluir o grupo de recursos exclui todos os recursos que você criou na interface do visual.  

### <a name="delete-only-the-compute-target"></a>Excluir apenas o destino de computação

O destino de computação que você criou aqui *automaticamente é dimensionado automaticamente* para zero nós quando não está sendo usado. Isso é para minimizar os encargos. Se você quiser excluir o destino de computação, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), abra o espaço de trabalho.

    ![Excluir o destino de computação](./media/aml-ui-cleanup/delete-compute-target.png)

1. No **computação** seção do seu espaço de trabalho, selecione o recurso.

1. Selecione **Excluir**.

### <a name="delete-individual-assets"></a>Excluir recursos individuais

Na interface do visual em que você criou seu experimento, excluir ativos individuais selecionando-os e, em seguida, selecionando a **excluir** botão.

![Excluir testes](./media/aml-ui-cleanup/delete-experiment.png)
