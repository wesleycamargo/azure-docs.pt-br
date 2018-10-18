---
title: Guia de administrador do portal do Azure Data Box | Microsoft Docs
description: Descreve como usar o portal do Azure para administrar o Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 49c2258100e99742bcb2e22fbce7f05b69c70ef6
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090715"
---
# <a name="use-the-azure-portal-to-administer-your-data-box"></a>Usar o portal do Azure para administrar o Data Box

Este artigo descreve alguns dos fluxos de trabalho e das tarefas de gerenciamento complexas que podem ser executados no Data Box. Você pode gerenciar o Data Box pelo portal do Azure ou pela interface do usuário da Web local. 

Este artigo se concentra nas tarefas que você pode executar usando o portal do Azure. Use o portal do Azure para gerenciar pedidos, gerenciar o Data Box e acompanhar o status do pedido, conforme ele progride para a conclusão.


## <a name="cancel-an-order"></a>Cancelar um pedido

Sempre pode haver a necessidade de cancelar um pedido depois de feito, por vários motivos. Você somente pode cancelar o pedido antes que ele seja processado. Depois que o pedido for processado e o Data Box for preparado, não será mais possível cancelar o pedido. 

Execute as seguintes etapas para cancelar um pedido.

1.  Vá para **Visão Geral > Cancelar**. 

    ![Cancelar pedido 1](media/data-box-portal-admin/cancel-order1.png)

2.  Preencha um motivo para cancelar o pedido.  

    ![Cancelar pedido 2](media/data-box-portal-admin/cancel-order2.png)

3.  Depois que o pedido for cancelado, o portal atualizará o status do pedido e o exibirá como **Cancelado**. 

## <a name="clone-an-order"></a>Clonar um pedido

A clonagem é útil em determinadas situações. Por exemplo, um usuário usou o Data Box para transferir alguns dados. Conforme mais dados são gerados, surge a necessidade de outro Data Box para transferir dados para o Azure. Nesse caso, o mesmo pedido pode simplesmente ser clonado.

Execute as etapas a seguir para clonar um pedido.

1.  Vá para **Visão geral > Clonar**. 

    ![Clonar pedido 1](media/data-box-portal-admin/clone-order1.png)

2.  Todos os detalhes do pedido permanecem inalterados. O nome do pedido é o nome original do pedido acrescido de *-Clone*. Marque a caixa de seleção para confirmar que você leu as informações de privacidade. Clique em **Criar**.    

O clone é criado em alguns minutos e a portal é atualizado para mostrar o novo pedido.


## <a name="delete-order"></a>Excluir pedido

Você talvez queira excluir um pedido quando ele é concluído. O pedido contém suas informações pessoais, como nome, endereço e informações de contato. Essas informações pessoais são excluídas quando o pedido é excluído.

Só é possível excluir pedidos que tenham sido concluídos ou cancelados. Execute as etapas a seguir para excluir um pedido.

1. Acesse **Todos os recursos**. Pesquise seu pedido.

2. Clique no pedido que você deseja excluir e vá para **Visão Geral**. Na barra de comandos, clique em **Excluir**.

    ![Excluir pedido do Data Box 1](media/data-box-portal-admin/delete-order1.png)

3. Insira o nome do pedido quando solicitado a confirmar a exclusão de um pedido. Clique em **Excluir**.

## <a name="download-shipping-label"></a>Faça o download da etiqueta de remessa

Talvez você precise baixar a etiqueta de remessa se a exibição de tinta virtual do Data Box não estiver funcionando e não exibir a etiqueta de remessa de devolução. 

Execute as etapas a seguir para baixar uma etiqueta de remessa.
1.  Vá para **Visão geral > Baixar etiqueta de remessa**. Essa opção fica disponível somente depois que o dispositivo é enviado. 

    ![Faça o download da etiqueta de remessa](media/data-box-portal-admin/download-shipping-label.png)

2.  Essa opção baixa a etiqueta de remessa de devolução a seguir. Salve a etiqueta e imprimi-a. Dobre e insira a etiqueta na abertura do dispositivo. Verifique se a etiqueta está visível. Remova todos os adesivos do dispositivo referentes ao envio anterior.

    ![Etiqueta de remessa de exemplo](media/data-box-portal-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>Editar endereço para entrega

Talvez seja necessário editar o endereço de envio depois que o pedido é feito. Essa opção só está disponível até o envio do disco. Depois que o disco é enviado, essa opção não fica mais disponível.

Execute as etapas a seguir para editar o pedido.

1. Vá para **Detalhes do pedido > Editar endereço para entrega**.

    ![Editar endereço para entrega 1](media/data-box-portal-admin/edit-shipping-address1.png)

2. Edite e valide o endereço para entrega e, em seguida, salve as alterações.

    ![Editar endereço para entrega 2](media/data-box-portal-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>Editar detalhes da notificação

Talvez seja necessário alterar os usuários que devem receber os emails de status do pedido. Por exemplo, um usuário precisa ser informado quando o dispositivo é entregue ou recolhido. Outro usuário talvez precise ser informado quando a cópia de dados é concluída para que possa verificar se os dados estão na conta de armazenamento do Azure antes de excluí-los da origem. Nesses casos, você pode editar os detalhes da notificação.

Execute as etapas a seguir para editar os detalhes de notificação.

1. Vá para **Detalhes do pedido > Editar detalhes da notificação**.

    ![Editar detalhes da notificação 1](media/data-box-portal-admin/edit-notification-details1.png)

2. Agora você pode editar os detalhes de notificação e salvar as alterações.
 
    ![Editar detalhes da notificação 2](media/data-box-portal-admin/edit-notification-details2.png)


## <a name="view-order-status"></a>Exibir status do pedido

Quando o status do dispositivo for alterado no portal, você será notificado por email.

|Status do pedido |DESCRIÇÃO |
|---------|---------|
|Encomendado     | Pedido feito com êxito. <br>Se o dispositivo estiver disponível, a Microsoft identificará um dispositivo para envio e o preparará. <br> Se o dispositivo não estiver disponível imediatamente, o pedido apenas será processado quando o dispositivo estiver disponível. O pedido pode levar de vários dias a alguns meses para ser processado. Se o pedido não puder ser processado em até 90 dias, ele será cancelado e você será notificado.         |
|Processado     | O processamento de pedidos foi concluído. De acordo com seu pedido, o dispositivo está preparado para o envio no data center.         |
|Expedido     | O pedido foi enviado. Use a ID de acompanhamento exibida no pedido no portal para acompanhar a remessa.        |
|Entregue     | A remessa foi entregue no endereço especificado no pedido.        |
|Recolhido     |A remessa de devolução foi recolhida e verificada pela transportadora.         |
|Recebido     | O dispositivo foi recebido e verificado no datacenter do Azure. <br> Depois que a remessa for inspecionada, o upload do dispositivo será iniciado.      |
|Cópia de dados     | Cópia de dados em andamento. Acompanhe o progresso de cópia do pedido no portal do Azure. <br> Aguarde até que a cópia de dados seja concluída. |
|Concluído       |Pedido concluído com êxito.<br> Verifique se os dados estão no Azure antes de excluir os dados locais dos servidores.         |
|Concluído com erros| A cópia de dados foi concluída, mas apresentou erros. <br> Examine os logs de cópia usando o caminho fornecido no portal do Azure.   |
|Cancelado            |Pedido cancelado. <br> Você cancelou o pedido ou ocorreu um erro e o serviço cancelou o pedido. Se o pedido não puder ser processado em até 90 dias, ele também será cancelado e você será notificado.     |
|Limpar | Os dados nos discos do dispositivo foram apagados. A limpeza do dispositivo é considerada concluída quando o relatório de log do pedido fica disponível no portal do Azure.|



## <a name="next-steps"></a>Próximas etapas

- Saiba como [Solucionar problemas do Data Box](data-box-faq.md).
