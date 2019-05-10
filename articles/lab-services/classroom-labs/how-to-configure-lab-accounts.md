---
title: Configurar contas de laboratório no Azure Lab Services | Microsoft Docs
description: Saiba como configurar uma conta de laboratório depois que ele é criado.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: ba469c038f04a31a57e798b97b5120bec573feae
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65414051"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Configurar contas de laboratório no Azure Lab Services 
Nos serviços de laboratório do Azure, uma conta de laboratório é um contêiner para tipos de laboratório gerenciado, como laboratórios de sala de aula. Um administrador configura uma conta de laboratório no Azure Lab Services e fornece acesso aos proprietários de laboratório que podem criar laboratórios na conta. Este artigo descreve como criar uma conta de laboratório, exibir todas as contas de laboratório ou excluir uma conta de laboratório.

## <a name="connect-with-a-peer-virtual-network"></a>Conecte-se com uma rede virtual do par
Para conectar uma rede virtual como uma rede ponto a rede de virtual do laboratório, siga estas etapas:

1. Sobre o **conta de laboratório** página, selecione **configuração Labs** no menu à esquerda.

    ![Página de configuração de laboratórios](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Para **rede virtual do par**, selecione **Enabled** ou **desabilitado**. O valor padrão é **desabilitado**. Para habilitar a rede virtual do par, execute as seguintes etapas: 
    1. Selecione **Habilitado**.
    2. Selecione o **VNet** na lista suspensa. 
3. Selecione **Salvar** na barra de ferramentas. 

Os laboratórios criados nessa conta são conectados à rede virtual selecionada. Eles podem acessar os recursos na rede virtual selecionada. Para obter mais informações, consulte [conectar-se a rede do seu laboratório com uma rede virtual do par no Azure Lab Services](how-to-connect-peer-virtual-network.md).

Quando você seleciona uma rede virtual para o **rede virtual do par** campo, o **permitir que o criador de laboratório para escolher o local do laboratório** opção está desabilitada. É porque os laboratórios na conta de laboratório devem estar na mesma região que a conta de laboratório para que eles possam se conectar com os recursos na rede virtual ponto a ponto. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>Permitir que o criador de laboratório escolher o local para o laboratório
Você pode permitir que o criador de laboratório criar laboratórios em um local diferente do local da conta de laboratório, seguindo estas etapas: 

1. Sobre o **conta de laboratório** página, selecione **configuração Labs** no menu à esquerda.
2. Para o **permitir que o criador de laboratório para escolher o local do laboratório**, selecione **habilitado** se você quiser que o criador de laboratório para poder selecionar um local para o laboratório. Se ele estiver desabilitado, os laboratórios são criados automaticamente no mesmo local no qual existe a conta de laboratório. 
    
    Este campo será desabilitado quando você seleciona uma rede virtual para o **rede virtual do par** campo. É porque os laboratórios na conta de laboratório devem estar na mesma região que a conta de laboratório para que eles possam acessar recursos na rede virtual ponto a ponto. 
1. Selecione **Salvar** na barra de ferramentas. 

    ![Definir configuração de local de laboratório](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Especifique um intervalo de endereços para VMs no laboratório
O procedimento a seguir tem etapas para especificar um intervalo de endereços para VMs no laboratório. Se você atualizar o intervalo que você especificou anteriormente, o intervalo de endereços modificado se aplica apenas às VMs que são criadas após a alteração foi feita. 

Aqui estão algumas restrições ao especificar o intervalo de endereços que você deve ter em mente. 

- O prefixo deve ser menor que ou igual a 23. 
- Se uma rede virtual é emparelhada para a conta de laboratório, o intervalo de endereço fornecido não coincide com o intervalo de endereços de rede virtual emparelhada.

1. Sobre o **conta de laboratório** página, selecione **configuração Labs** no menu à esquerda.
2. Para o **intervalo de endereços** , especifique o intervalo de endereços para VMs que serão criadas no laboratório. O intervalo de endereços deve estar na notação CIDR (CIDR) (exemplo: 10.20.0.0/23). Máquinas virtuais no laboratório serão criadas nesse intervalo de endereço.
3. Selecione **Salvar** na barra de ferramentas. 

    ![Configurar o intervalo de endereços](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Adicionar um usuário à função de criador de laboratório
Para configurar um laboratório de curso em uma conta de laboratório, o usuário deve ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta usada para criar a conta de laboratório é automaticamente adicionada a essa função. Se você planeja usar a mesma conta de usuário para criar um laboratório de curso, poderá pular esta etapa. Para usar outra conta de usuário para criar um laboratório de sala de aula, execute as seguintes etapas: 

Para fornecer aos educadores a permissão para criar laboratórios para suas classes, adicione-os à função de **Criador de Laboratório**:

1. Na página **Conta de Laboratório**, selecione **Controle de acesso (IAM)** e clique em **+ Adicionar atribuição de função** na barra de ferramentas. 

    ![Controle de Acesso -> botão Adicionar atribuição de função](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na página **Adicionar atribuição de função**, selecione **Criador de Laboratório** em **Função**, selecione o usuário que você deseja adicionar à função de Criadores de Laboratório e selecione **Salvar**. 

    ![Adicionar criador de laboratório](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Especificar imagens do Marketplace disponíveis para os criadores de laboratório
Como proprietário da conta de laboratório, você pode especificar as imagens do Marketplace que os criadores do laboratório podem usar para criar laboratórios na conta de laboratório. 

1. Selecione **Imagens do Marketplace** no menu esquerdo. Por padrão, você deve ver a lista completa de imagens (habilitadas e desabilitadas). É possível filtrar a lista para ver apenas as imagens habilitadas/desabilitadas, selecionando a opção **Somente habilitadas**/**Somente desabilitadas** na lista suspensa na parte superior. 
    
    ![Página Imagens do Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    As imagens do Marketplace exibidas na lista são apenas aquelas que atendem as seguintes condições:
        
    - Cria uma única VM
    - Usa o Azure Resource Manager para provisionar VMs
    - Não requer a compra de um plano de licenciamento extra
2. Para **desabilitar** uma imagem do Marketplace que tenha sido habilitada, execute uma das seguintes ações: 
    1. Selecione **... (reticências)**  na última coluna e selecione **Desabilitar imagem**. 

        ![Desabilitar uma imagem](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Selecione uma ou mais imagens na lista, marcando as caixas de seleção antes dos nomes de imagem na lista e selecione **Desabilitar imagens selecionadas**. 

        ![Desabilitar várias imagens](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. De modo semelhante, para **habilitar** uma imagem do Marketplace execute uma das seguintes ações: 
    1. Selecione **... (reticências)**  na última coluna e selecione **Habilitar imagem**. 
    2. Selecione uma ou mais imagens na lista, marcando as caixas de seleção antes dos nomes de imagem na lista e selecione **Habilitar imagens selecionadas**. 




## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)
