---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: cd18d71d26410767a2d3119c12a1339bdc84bd33
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58114165"
---
1. No seu servidor de configuração, inicie CSPSConfigtool.exe. Está disponível como um atalho na área de trabalho e localizada na pasta *local da instalação*\home\svsystems\bin.
2. Clique em **Gerenciar Contas** > **Adicionar Conta**.

    ![Adicionar Conta](./media/site-recovery-add-vcenter-account/credentials1.png)
3. Em **Detalhes da Conta**, adicione a conta que será usada para a descoberta automática.

    ![Detalhes](./media/site-recovery-add-vcenter-account/credentials2.png)

    > [!Note]
   > Pode levar 15 minutos ou mais para que o nome da conta apareça no portal. Para atualizar imediatamente, clique em **Servidores de Configuração** > ***nome do servidor*** > **Atualizar Servidor**.
