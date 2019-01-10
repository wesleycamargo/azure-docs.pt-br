---
title: Tutorial sobre como preparar o portal do Azure para implantar o Data Box Edge | Microsoft Docs
description: O primeiro tutorial sobre como implantar o Azure Data Box Edge envolve a preparação do portal do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 25f68b011d1fcba450903e9a691b98dfe9e87281
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726112"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Tutorial: Preparar para implantar o Azure Data Box Edge  


Este é o primeiro tutorial da série de tutoriais de implantação necessários para implantar completamente seu Azure Data Box Edge. Este tutorial descreve como preparar o portal do Azure para implantar um recurso do Data Box Edge. 

Você precisará de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal leva menos de 10 minutos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


> [!IMPORTANT]
> O Data Box Edge está em versão prévia. Antes de pedir e implantar essa solução, examine os [Termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).  

### <a name="get-started"></a>Introdução

Veja os tutoriais a seguir na sequência indicada para implantar o Data Box Edge.

| **#** | **Nesta etapa** | **Use estes documentos** |
| --- | --- | --- | 
| 1. |**[Preparar o portal do Azure para o Data Box Edge](data-box-edge-deploy-prep.md)** |Crie e configure o recurso do Data Box Edge antes de instalar um dispositivo físico do Data Box Edge. |
| 2. |**[Instalar o Data Box Edge](data-box-edge-deploy-install.md)**|Desempacote, monte no rack e prepare o cabeamento do dispositivo físico do Data Box Edge.  |
| 3. |**[Conectar-se, configurar e ativar o Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |Conectar-se a interface do usuário da Web local, conclua a configuração do dispositivo e ativar o dispositivo. O dispositivo está pronto para configurar compartilhamentos NFS ou SMB.  |
| 4. |**[Transferir dados com o Data Box Edge](data-box-edge-deploy-add-shares.md)** |Adicione compartilhamentos e conecte-se a ele por meio de SMB ou NFS. |
| 5. |**[Transformar dados com o Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Configure módulos do Edge no dispositivo para transformar os dados à medida que eles se movem para o Azure. |

Agora, você pode começar a configurar o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

A seguir estão os pré-requisitos de configuração para o recurso do Data Box Edge, o dispositivo do Data Box Edge e a rede do datacenter.

### <a name="for-the-data-box-edge-resource"></a>Para o recurso do Data Box Edge

Antes de começar, verifique se:

* Sua assinatura do Microsoft Azure está habilitada para o recurso do Data Box Edge.
* Você tem sua conta de armazenamento do Microsoft Azure com credenciais de acesso.

### <a name="for-the-data-box-edge-device"></a>Para o dispositivo do Data Box Edge

Antes de implantar um dispositivo físico, verifique se:
- Você tem um slot de 1 U disponível em um rack padrão de 19" no seu datacenter para montar o dispositivo em rack. 
- Você tem acesso a uma superfície de trabalho plana, estável e nivelada na qual o dispositivo pode apoiar-se com segurança.
- O local em que você pretende instalar o dispositivo tem alimentação CA padrão de uma fonte independente ou uma PDU (unidade de distribuição de energia) no rack com um no-break.
- Você tem acesso a um dispositivo físico.


### <a name="for-the-datacenter-network"></a>Para a rede de datacenter

Antes de começar, verifique se:

* A rede no seu datacenter é configurada de acordo com os requisitos de rede do seu dispositivo de Data Box Edge. Para obter mais informações, confira [Requisitos do sistema do Data Box Edge](data-box-gateway-system-requirements.md).

* O Data Box Edge tem largura de banda de Internet dedicada de 20 Mbps (ou mais) disponível o tempo todo. Essa largura de banda não deve ser compartilhada com nenhum outro aplicativo. Se você está usando limitação de rede, para que a limitação funcione, recomendamos usar uma largura de banda de Internet de 32 Mbps ou mais.

## <a name="create-a-new-resource"></a>Criar um novo recurso

Execute as etapas a seguir para criar um novo recurso do Data Box Edge. 

Se você tiver um recurso do Data Box Edge existente para gerenciar seu dispositivo físico, ignore esta etapa e vá para [Obter a chave de ativação](#get-the-activation-key).

Para criar um recurso do Data Box Edge, execute as seguintes etapas no portal do Azure.

1. Use suas credenciais do Microsoft Azure para entrar na Versão Prévia do Portal do Azure nesta URL: [https://aka.ms/databox-edge](https://aka.ms/databox-edge). 

2. Escolha a assinatura que deseja usar para a versão prévia do Data Box Edge. Selecione a região em que você deseja implantar o recurso do Data Box Edge. Na opção **Data Box Edge**, selecione **Criar**.

    ![Pesquisar o serviço do Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Para o novo recurso, insira ou selecione as informações a seguir.
    
    |Configuração  |Valor  |
    |---------|---------|
    |Nome do recurso   | Um nome amigável para identificar o recurso.<br>O nome do recurso tem entre 2 e 50 caracteres contendo letras, números e hifens.<br> Nome começa e termina com uma letra ou um número.        |
    |Assinatura    |A assinatura está vinculada à sua conta de faturamento. |
    |Grupo de recursos  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre [grupos de recursos do Azure](../azure-resource-manager/resource-group-overview.md).     |
    |Local padrão     |Para este lançamento, os EUA do Leste, o Oeste dos EUA 2, o Sudeste Asiático e a Europa Ocidental estão disponíveis. <br> Escolha um local mais próximo da região geográfica em que você deseja implantar seu dispositivo.|
    
    ![Criar um recurso do Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource.png)
    
4. Selecione **OK**.
 
A criação do recurso leva alguns minutos. Depois que o recurso for criado com sucesso, você será notificado adequadamente.


## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois que o recurso do Data Box Edge estiver em execução, será necessário obter a chave de ativação. Essa chave é usada para ativar e conectar seu dispositivo do Data Box Edge ao recurso. Você pode obter essa chave agora enquanto estiver no portal do Azure.

1. Selecione o recurso que você criou e, em seguida, selecione **Visão Geral**.

2. Selecione **Gerar chave** para criar uma chave de ativação. Selecione o ícone de copiar para copiar a chave e salvá-la para uso posterior.

    ![Obter a chave de ativação](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - A chave de ativação expira três dias depois de ser gerada. 
> - Se a chave tiver epxired, gere uma nova chave. A chave antiga não é válida.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre os tópicos do Data Box Edge, como:

> [!div class="checklist"]
> * Criando um novo recurso
> * Obtendo a chave de ativação

Prossiga para o próximo tutorial para saber como instalar o Data Box Edge. 

> [!div class="nextstepaction"]
> [Instalar o Data Box Edge](./data-box-edge-deploy-install.md)



