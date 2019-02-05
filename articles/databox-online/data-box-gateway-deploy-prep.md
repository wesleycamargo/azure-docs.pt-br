---
title: Tutorial sobre como preparar o portal do Azure para implantar o Data Box Gateway | Microsoft Docs
description: O primeiro tutorial para implantar o Azure Data Box Gateway envolve a preparação do portal do Azure.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: alkohli
ms.openlocfilehash: 6f47606e91ec55bae624527bace81d947c5ea4f9
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55091539"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway-preview"></a>Tutorial: Preparar para implantar o Azure Data Box Gateway (versão prévia)


Este é o primeiro tutorial da série de tutoriais de implantação necessários para implantar completamente o seu Azure Data Box Gateway. Este tutorial descreve como preparar o portal do Azure para implantar o recurso Data Box Gateway. 

Você precisará de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal leva menos de 10 minutos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Baixar a imagem do dispositivo virtual
> * Obter a chave de ativação

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


> [!IMPORTANT]
> - O Data Box Gateway está em versão prévia. Examine os [termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de solicitar e implantar essa solução. 

### <a name="get-started"></a>Introdução

Consulte os tutoriais a seguir na sequência prescrita para implantar o Data Box Gateway.

| **#** | **Nesta etapa** | **Use estes documentos** |
| --- | --- | --- | 
| 1. |**[Preparar o portal do Azure para o Data Box Gateway](data-box-gateway-deploy-prep.md)** |Crie e configure o recurso de Gateway da caixa de dados antes do provisionamento de um dispositivo virtual do Data Box Gateway. |
| 2. |**[Provisione o Data Box Gateway no Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Provisione o Data Box Gateway no VMware](data-box-gateway-deploy-provision-vmware.md)**|Para o Hyper-V, forneça e conecte-se a um dispositivo virtual do Data Box Gateway em um sistema host que execute o Hyper-V no Windows Server 2016 ou no Windows Server 2012 R2. <br><br><br> Para o VMware, provisione e conecte-se a um dispositivo virtual de Data Box Gateway em um sistema de host executando o VMware ESXi 6.0, 6.5 ou 6.7.<br></br> |
| 3. |**[Conectar-se, configurar, ativar o Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Conectar-se a interface do usuário da Web local, conclua a configuração do dispositivo e ativar o dispositivo. Em seguida, é possível provisionar compartilhamentos SMB.  |
| 4. |**[Transferir dados com o Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Adicionar compartilhamentos, conecte-se aos compartilhamentos via SMB ou NFS. |

Agora, você pode começar a configurar o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Aqui você encontra os pré-requisitos de configuração para o recurso Data Box Gateway, o dispositivo Data Box Gateway e a rede do datacenter.

### <a name="for-the-data-box-gateway-resource"></a>Para o recurso de Data Box Gateway

Antes de começar, verifique se:

* Sua assinatura do Microsoft Azure deve ser habilitada para o recurso de Data Box Gateway.
* Você tem sua conta de armazenamento do Microsoft Azure com credenciais de acesso.

### <a name="for-the-data-box-gateway-device"></a>Para o dispositivo de Data Box Gateway

Antes de implantar um dispositivo virtual, verifique se:

* Você tem acesso a um sistema host que executa o Hyper-V no Windows Server 2012 R2 ou posterior ou o VMware (ESXi 6.0, 6.5 ou 6.7) que pode ser usado para provisionar um dispositivo.
* O sistema host é capaz de dedicar os seguintes recursos para provisionar seu dispositivo virtual Data Box:
  
  * Um mínimo de quatro núcleos.
  * Pelo menos 8 GB de RAM. 
  * Uma interface de rede.
  * Um disco de SO de 250 GB.
  * Um disco virtual de 2 TB de dados do sistema.

### <a name="for-the-datacenter-network"></a>Para a rede de datacenter

Antes de começar, verifique se:

* A rede no seu datacenter é configurada de acordo com os requisitos de rede do seu dispositivo Data Box Gateway. Para obter mais informações, consulte os [Requisitos de sistema do Data Box Gateway](data-box-gateway-system-requirements.md).

* O Data Box Gateway tem uma largura de banda de Internet dedicada de 20 Mbps (ou mais) disponível o tempo todo. Essa largura de banda não deve ser compartilhada com outros aplicativos. Se estiver usando a aceleração de rede, para que a otimização funcione, recomendamos o uso de largura de banda de Internet de 32 Mbps ou mais.

## <a name="create-a-new-resource"></a>Criar um novo recurso

Execute as etapas a seguir para criar um novo recurso de Data Box Gateway. 

Se você tiver um recurso do Data Box Gateway existente para gerenciar seus dispositivos virtuais, ignore esta etapa e vá para [Obter a chave de ativação](#get-the-activation-key).

Execute as seguintes etapas no portal do Azure para criar um recurso de caixa de dados.
1. Use suas credenciais do Microsoft Azure para fazer logon no portal de visualização do Azure nesta URL: [https://aka.ms/databox-edge](https://aka.ms/databox-edge). 

2. Escolha a assinatura que você deseja usar para a visualização da Borda da Caixa de Dados. Selecione a região onde você deseja implantar o recurso Data Box Edge. No **Data Box Gateway** , clique em **criar**.

    ![Serviço de Data Box Gateway de pesquisa](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

3. Para o novo recurso, insira ou selecione as informações a seguir.
    
    |Configuração  |Valor  |
    |---------|---------|
    |Nome do recurso   | Um nome amigável para identificar o recurso.<br>O nome tem entre 2 e 50 caracteres contendo letras, números e hifens.<br> Nome começa e termina com uma letra ou um número.        |
    |Assinatura    |A assinatura está vinculada à sua conta de faturamento. |
    |Grupo de recursos  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre [grupos de recursos do Azure](../azure-resource-manager/resource-group-overview.md).     |
    |Local padrão     |Para este lançamento, os EUA do Leste, o Oeste dos EUA 2, o Sudeste Asiático e a Europa Ocidental estão disponíveis. <br> Escolha um local mais próximo da região geográfica em que você deseja implantar seu dispositivo.|
    
    ![Criar recurso de Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
4. Clique em **OK**.
 
A criação do recurso leva alguns minutos. Depois que o recurso for criado com sucesso, você será notificado adequadamente.


## <a name="download-the-virtual-device-image"></a>Baixar a imagem do dispositivo virtual

Depois que o recurso Data Box Gateway for criado, faça o download da imagem do dispositivo virtual apropriado para provisionar um dispositivo virtual em seu sistema host. As imagens de dispositivos virtuais são específicas do sistema operacional e podem ser baixadas do blade **Quickstart** do seu recurso no portal do Azure.

> [!IMPORTANT]
> O software em execução no Data Box Gateway só pode ser usado com o recurso Data Box Gateway.


Execute as etapas a seguir no [portal do Azure](https://portal.azure.com/).

1. Clique no recurso que você criou e, em seguida, clique em **Visão geral**. Se você tiver um recurso existente do Azure Data Box Gateway, clique no recurso e vá para **Visão Geral**.

    ![Novo recurso de Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

4. No início rápido no painel direito, clique no link correspondente à imagem que você deseja baixar. Os arquivos de imagem têm aproximadamente 4,8 GB.
   
   * [VHDX para Hyper-V no Windows Server 2012 R2 e posterior](https://aka.ms/dbe-vhdx-2012).
   * [VMDK para VMWare ESXi 6.0, 6.5 ou 6.7](https://aka.ms/dbe-vmdk).

5. Baixe e descompacte o arquivo em uma unidade local, observando onde o arquivo descompactado está localizado.


## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois que o recurso Data Box Gateway estiver em execução, será necessário obter a chave de ativação. Essa chave é usada para ativar e conectar seu dispositivo Data Box Gateway ao recurso.

A chave de ativação é usada para registrar todos os dispositivos do Data Box Gateway que precisam ser ativados com o recurso Data Box Gateway. Você pode obter essa chave agora enquanto estiver no portal do Azure.

1. Clique no recurso que você criou e, em seguida, clique em **Visão geral**.

    ![Novo recurso de Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Clique em **gerar chave** para criar uma chave de ativação. Clique no ícone de cópia para copiar a chave e salvá-la para uso posterior.

    ![Obter a chave de ativação](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - A chave de ativação expira 3 dias depois de ser gerada. 
> - Se a chave expirou, gere uma nova. A chave antiga não é válida.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre os tópicos do Data Box Gateway, como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Baixar a imagem do dispositivo virtual
> * Obter a chave de ativação

Avance para o próximo tutorial para aprender a provisionar uma máquina virtual para o Data Box Gateway. Dependendo do sistema operacional host, consulte as instruções detalhadas em:

> [!div class="nextstepaction"]
> [Provisionar um Data Box Gateway no Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

OU

> [!div class="nextstepaction"]
> [Provisionar um Data Box Gateway no VMware](./data-box-gateway-deploy-provision-vmware.md)


