---
title: Retornar o seu dispositivo de borda de caixa de dados do Azure | Microsoft Docs
description: Descreve como retornar o dispositivo de borda de caixa de dados do Azure e excluir o pedido para o dispositivo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/07/2019
ms.author: alkohli
ms.openlocfilehash: 9aeae0ab68d809b36a3316054f12a5a9657721f1
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468598"
---
# <a name="return-your-azure-data-box-edge-device"></a>Retornar o seu dispositivo de borda de caixa de dados do Azure

Este artigo descreve como apagar os dados e, em seguida, retornar seu dispositivo de borda de caixa de dados do Azure. Depois de retornar o dispositivo, você também pode excluir os recursos associados ao dispositivo.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Apagar os dados dos discos de dados no dispositivo
> * Abra um tíquete de suporte para retornar o seu dispositivo
> * Pacote do dispositivo e agendar uma retirada
> * Exclua o recurso no portal do Azure

## <a name="erase-data-from-the-device"></a>Apagar dados do dispositivo

Para apagar os dados dos discos de dados do seu dispositivo, você precisará redefinir seu dispositivo. Você pode redefinir seu dispositivo usando a interface do usuário da web local ou a interface do PowerShell.

Antes de redefinir, crie uma cópia dos dados locais no dispositivo, se necessário. Você pode copiar os dados do dispositivo para um contêiner de armazenamento do Azure.

Para redefinir seu dispositivo usando o interface do usuário da web local, execute as seguintes etapas.

1. Na interface do usuário de web local, vá para **manutenção > Redefinir dispositivo**.
2. Selecione **redefinir dispositivo**.

    ![Redefinir dispositivo](media/data-box-edge-return-device/device-reset-1.png)

3. Quando solicitado para confirmação, examine o aviso e selecione **Sim** para continuar.

    ![Confirme a redefinição](media/data-box-edge-return-device/device-reset-2.png)  

A redefinição apaga os dados dos discos de dados do dispositivo. Dependendo da quantidade de dados em seu dispositivo, esse processo leva cerca de 30 a 40 minutos.

Como alternativa, conecte-se à interface do PowerShell do dispositivo e usar o `Reset-HcsAppliance` cmdlet apagar os dados dos discos de dados. Para obter mais informações, consulte [redefinir seu dispositivo](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Se você está trocando ou atualizando para um novo dispositivo, é recomendável que você redefinir seu dispositivo somente depois de receber o novo dispositivo.
> - O dispositivo redefinir somente exclui todos os dados locais desativar o dispositivo. Os dados que estão na nuvem não são excluídos e coleta [encargos](https://azure.microsoft.com/pricing/details/storage/). Esses dados precisam ser excluído separadamente usando uma ferramenta de gerenciamento de armazenamento de nuvem, como [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Abra um tíquete de suporte

Para iniciar o processo de retorno, execute as seguintes etapas.

1. Abra um tíquete de suporte com o Microsoft Support indicando que você deseja retornar o dispositivo. Selecione o tipo de problema como **dados de caixa de borda Hardware**.

    ![Abra um tíquete de suporte](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Um engenheiro da Microsoft Support entrará em contato com você. Forneça os detalhes de remessa.
3. Se você precisar de uma caixa de remessa de retornada, você pode solicitar a ele. Resposta **Yes** para a pergunta **precisa de uma caixa vazia para retornar**.


## <a name="schedule-a-pickup"></a>Agendar uma retirada

1. Desligar o dispositivo. Na interface do usuário de web local, vá para **manutenção > configurações de energia**.
2. Selecione **desligar**. Quando solicitado a confirmar, clique em **Sim** para continuar. Para obter mais informações, consulte [gerenciar poder de](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Desconecte os cabos de alimentação e remova todos os cabos de rede do dispositivo.
4. Prepare o pacote de remessa usando sua própria caixa ou a caixa vazia recebidas do Azure. Coloque o dispositivo e os cabos de energia que foram fornecidos com o dispositivo na caixa.
5. Fixar a etiqueta de remessa que você recebeu do Azure no pacote.
6. Agende uma retirada com sua operadora regional. Se retornar o dispositivo nos Estados Unidos, sua operadora é no-break. Para agendar uma retirada:

    1. Ligue para o serviço de no-break local (linha gratuita específica do país).
    2. Em sua chamada citar a remessa reversa conforme mostrado no seu rótulo impresso de número de controle.
    3. Se o número de controle não está entre aspas, no-break exigirá que você paga um encargo adicional durante a retirada.

    Em vez de agendar a retirada, você também pode descartar fora da borda da caixa de dados no local mais próximo de redistribuição.

## <a name="delete-the-resource"></a>Excluir o recurso

Depois que o dispositivo é recebido no datacenter do Azure, o dispositivo é inspecionado por danos ou sinais de violação.

- Se o dispositivo chega intactos e em boa forma, o medidor de cobrança será interrompida para esse recurso. Microsoft Support entrará em contato com você para confirmar se o dispositivo foi retornado. Em seguida, você pode excluir o recurso associado com o dispositivo no portal do Azure.
- Se o dispositivo chega seriamente danificado, multas podem ser aplicadas. Para obter detalhes, consulte o [perguntas Frequentes sobre o dispositivo perdido ou danificado](https://azure.microsoft.com/pricing/details/databox/edge/) e [termos de serviço do produto](https://www.microsoft.com/licensing/product-licensing/products).  


Você pode excluir o dispositivo no portal do Azure:
-   Depois que você fez o pedido e antes do dispositivo está preparado pela Microsoft.
-   Depois de retornar o dispositivo para a Microsoft, ele passa a inspeção física no datacenter do Azure e Microsoft Support chama para confirmar se o dispositivo foi retornado.

Se você já ativou o dispositivo em relação a outra assinatura ou local, a Microsoft moverá seu pedido para a nova assinatura ou o local em um dia útil. Depois que a ordem for movida, você pode excluir este recurso.


Execute as seguintes etapas para excluir o dispositivo e o recurso no portal do Azure.

1. No portal do Azure, vá até seu recurso e, em seguida, para **visão geral**. Na barra de comandos, selecione **excluir**.

    ![Selecione excluir](media/data-box-edge-return-device/delete-resource-1.png)

2. No **excluir dispositivo** folha, digite o nome do dispositivo que você deseja excluir e selecione **excluir**.

    ![Confirmar exclusão](media/data-box-edge-return-device/delete-resource-2.png)

Você será notificado depois que o dispositivo e o recurso associado for excluído com êxito.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar a largura de banda](data-box-edge-manage-bandwidth-schedules.md).
