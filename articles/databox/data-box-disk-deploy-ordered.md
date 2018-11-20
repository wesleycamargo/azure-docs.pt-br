---
title: Tutorial para solicitar o disco do Microsoft Azure Data Box Disk | Microsoft Docs
description: Use este tutorial para aprender a se inscrever e solicitar um disco do Azure Data Box para importar dados no Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 11/05/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 961352dc99a164b8537fb588e038ad1b385941cc
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567444"
---
# <a name="tutorial-order-an-azure-data-box-disk-preview"></a>Tutorial: Solicitar um disco do Azure Data Box (visualização)

O Disco do Azure Data Box é uma solução de nuvem híbrida que permite importar dados do local no Azure de maneira rápida, fácil e confiável. Transfira os dados para SSDs (unidades de estado sólido) fornecidas pela Microsoft e envie os discos novamente. Após, esses dados são carregados no Azure.

Este tutorial descreve como você pode solicitar um disco do Azure Data Box. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Inscrever-se no disco do Data Box
> * Solicitar um disco do Data Box
> * Acompanhar o pedido
> * Cancelar o pedido

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> - O Data Box Disk está em versão prévia. Examine os [termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de solicitar e implantar essa solução. 
> - Durante a versão prévia, o Disco do Data Box pode ser enviado para os clientes nos EUA, na Europa Setentrional e na Europa Ocidental e Oriental, no Canadá e na Austrália. Para saber mais, acesse [Disponibilidade por região](data-box-disk-overview.md#region-availability).

## <a name="sign-up"></a>Inscrição

O disco do Data Box está em versão prévia e você precisa se inscrever no serviço. Execute as etapas a seguir para se inscrever no serviço do Data Box:

1. Entre no portal do Azure em: [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).
2. Escolha a assinatura que você deseja habilitar para a versão prévia. Responda às perguntas sobre o tamanho dos dados, país de residência de dados, período de tempo e a frequência de transferência de dados. Clique em **Inscrever-me!**.
3. Depois de se inscrever e estar habilitado para versão prévia, você pode solicitar um disco do Data Box.

## <a name="order-data-box-disk"></a>Solicitar disco do Data Box

Execute as etapas a seguir no [portal do Azure](https://aka.ms/azuredataboxfromdiskdocs) para solicitar o disco do Data Box.

1. No canto superior esquerdo do portal, clique em **+ Criar um recurso** e procure *Azure Data Box*. Clique em **Azure Data Box**.
    
   ![Pesquisar Azure Data Box 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Clique em **Criar**.

3. Verifique se o serviço Data Box está disponível em sua região. Insira ou selecione as seguintes informações e clique em **Aplicar**.

    ![Selecionar a opção Disco do Data Box](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Configuração|Valor|
    |---|---|
    |Assinatura|Selecione a assinatura para a qual o serviço Data Box está habilitado.<br> A assinatura está vinculada à sua conta de cobrança. |
    |Tipo de transferência| Importar para o Azure|
    |País de origem | Selecione o país onde os dados residem no momento.|
    |Região do Azure de destino|Selecione a região do Azure para onde você deseja transferir dados.|

  
5.  Selecione **Disco do Data Box**. A capacidade máxima da solução para um único pedido de 5 discos é de 35 TB. É possível criar vários pedidos de tamanhos maiores de dados.

     ![Selecionar a opção Disco do Data Box](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  No **Pedido**, especifique os **Detalhes do pedido**. Insira ou selecione as seguintes informações.

    |Configuração|Valor|
    |---|---|
    |NOME|Forneça um nome amigável para acompanhar o pedido.<br> O nome pode ter entre 3 e 24 caracteres que podem ser letras, números e hífens. <br> O nome deve começar e terminar com uma letra ou um número. |
    |Grupo de recursos| Use um grupo existente ou crie um novo. <br> Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. |
    |Região do Azure de destino| Selecione uma região para sua conta de armazenamento.<br> Atualmente, há suporte para contas de armazenamento em todas as regiões nos EUA, na Europa Setentrional e na Europa Ocidental, no Canadá e na Austrália. |
    |Conta(s) de armazenamento|Com base na região especificada do Azure, selecione uma conta de armazenamento existente na lista filtrada. <br>Você também pode criar uma nova conta de Uso geral v1 ou v2. |
    |Tamanho estimado de dados em TB| Insira uma estimativa em TB. <br>Com base no tamanho dos dados, a Microsoft envia um número apropriado de SSDs de 8 TB (7 TB de capacidade utilizável). <br>A capacidade máxima de uso de 5 discos é de 35 TB. |
    |Chave de acesso do disco| Forneça a chave de acesso do disco se você marcar **Usar chave personalizada em vez da chave de acesso gerada pelo Azure**. <br> Forneça uma chave de 12 a 32 caracteres alfanuméricos que tenha pelo menos um caractere numérico e um caractere especial. Os caracteres especiais permitidos são `@?_+`. <br> Você pode optar por ignorar essa opção e usar a chave de acesso gerada pelo Azure para desbloquear os discos.|

13. Clique em **Próximo**. 

    ![Fornecer detalhes do pedido](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. Na guia **Endereço para remessa**, forneça seu nome e sobrenome, nome e endereço da empresa e um número de telefone válido. Clique em **Validar endereço**. O serviço valida o endereço de remessa para a disponibilidade do serviço. Se o serviço está disponível para o endereço de remessa especificado, você receberá uma notificação para esse efeito. 

    ![Fornecer endereço de remessa](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. Nos **Detalhes da notificação**, especifique endereços de email. O serviço envia as notificações por email em relação a quaisquer atualizações do status do pedido para os endereços de email especificados. 

    É recomendável usar um email de grupo para que você continue a receber notificações caso um administrador do grupo saia.

16. Examine o **Resumo** de informações relacionadas com o pedido, o contato, a notificação e os termos de privacidade. Marque a caixa correspondente ao contrato de termos de privacidade.

17. Clique em **Pedido**. A criação do pedido demora alguns minutos.

 
## <a name="track-the-order"></a>Acompanhar o pedido

Depois de fazer o pedido, você pode acompanhar o status do pedido no portal de visualização do Azure. Vá até seu pedido e depois até **Visão geral** para exibir o status. O portal mostra o trabalho sob o estado **Pedido**. 

![Disco do Data Box com o status de pedido](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Se os discos não estiverem disponíveis, você receberá uma notificação. Se os discos estiverem disponíveis, a Microsoft identificará os discos para remessa e preparará o pacote de disco. Durante a preparação do disco, ocorrerão as seguintes ações:

- Os discos são criptografados usando a criptografia BitLocker AES-128.  
- Os discos são bloqueados para impedir o acesso não autorizado a eles.
- A chave de acesso que desbloqueia os discos é gerada durante esse processo.

Quando a preparação de disco for concluída, o portal mostra o pedido no estado **Processado**.

A Microsoft preparará e enviará seus discos por meio de uma operadora regional. Depois que os discos forem enviados, você receberá um número de acompanhamento. O portal mostra o pedido no estado **Despachado**.



## <a name="cancel-the-order"></a>Cancelar o pedido

Para cancelar esse pedido, no portal de visualização do Azure, vá para **Visão geral** e clique em **Cancelar** na barra de comandos. 

Só é possível fazer o cancelamento quando os discos forem solicitados e o pedido estiver sendo processado para remessa. Depois que o pedido for processado, não é mais possível cancelá-lo. 

![Cancelar o pedido](media/data-box-disk-deploy-ordered/cancel-order1.png)

Para excluir um pedido cancelado, vá para **Visão geral** e clique em **Excluir** na barra de comandos. 


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
> * Inscrever-se no disco do Data Box
> * Solicitar disco do Data Box
> * Acompanhar o pedido
> * Cancelar o pedido

Avance para o próximo tutorial para saber como proteger seu disco do Data Box.

> [!div class="nextstepaction"]
> [Configure seu Disco do Azure Data Box](./data-box-disk-deploy-set-up.md)


