---
title: Guia de administrador do portal do Azure Data Box | Microsoft Docs
description: Descreve como usar o portal do Azure para administrar o Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 10/19/2018
ms.author: alkohli
ms.openlocfilehash: f2ad4dc3d74771d28ca91e41f2b0eb547d678908
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122987"
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


## <a name="download-order-history"></a>Baixar histórico de pedidos

Depois que o pedido do Data Box estiver completo, os dados nos discos do dispositivo serão apagados. Quando a limpeza de dispositivo for concluída, você poderá baixar o histórico de pedidos no portal do Azure.

Execute as etapas a seguir para baixar o histórico de pedidos.

1. No seu pedido do Data Box, vá para **Visão Geral**. Verifique se o pedido está completo. Se o pedido estiver completo e a limpeza for concluída, vá para **Detalhes da ordem**. A opção **Baixar histórico de pedidos** está disponível.

    ![Baixar histórico de pedidos](media/data-box-portal-admin/download-order-history-1.png)

2. Clique em **Baixar histórico de pedidos**. No histórico baixado, você verá um registro de logs de rastreamento da operadora. Se você rolar para baixo até a parte inferior desse log, poderá ver os links para:
    
   - **Copiar logs**: tem a lista de arquivos com erros durante a cópia dos dados do Data Box para a conta de armazenamento do Azure.
   - **Logs de auditoria**: contêm informações sobre ativação e acesso compartilhado no Data Box quando ele está fora do datacenter do Azure.
   - **Arquivos BOM**: tem a lista de arquivos (também conhecida como manifesto de arquivo) que você pode baixar durante a **Preparação para o envio** e tem nomes de arquivo, tamanhos de arquivo e somas de verificação do arquivo.

       ```
       -------------------------------
       Microsoft Data Box Order Report
       -------------------------------
        
       Name                                               : eastusdryrun                                      
       StartTime(UTC)                                     : 9/6/2018 12:54:47 PM +00:00                       
       DeviceType                                         : ImolaPod                                          
        
       -------------------
       Data Box Activities
       -------------------
        
       Time(UTC)             | Activity                       | Status          | Description                                                                                                                                           
        
       9/6/2018 12:54:51 PM  | OrderCreated         | Completed  |                                                                                                                              
       9/11/2018 8:57:38 PM  | DevicePrepared       | Completed  |                                                                                                                                                       
       9/12/2018 7:28:15 PM  | ShippingToCustomer   | InProgress | Pickup Scan. Local Time : 9/12/2018 2:52:31 PM at Chantilly                                                                                           
       9/13/2018 2:33:04 AM  | ShippingToCustomer   | InProgress | Departure Scan. Local Time : 9/12/2018 9:00:00 PM at Chantilly                                                                                                                                                                                                                                                              
       9/13/2018 12:40:31 PM | ShippingToCustomer   | InProgress | Arrival Scan. Local Time : 9/13/2018 5:00:00 AM at Oakland                                                                                            
       9/13/2018 2:42:10 PM  | ShippingToCustomer   | InProgress | Departure Scan. Local Time : 9/13/2018 6:08:00 AM at Oakland                                                                                          
       9/13/2018 3:42:12 PM  | ShippingToCustomer   | InProgress | Destination Scan. Local Time : 9/13/2018 8:14:08 AM at Sunnyvale                                                                                      
       9/13/2018 4:43:05 PM  | ShippingToCustomer   | InProgress | Destination Scan. Local Time : 9/13/2018 8:56:54 AM at Sunnyvale                                                                                      
       9/13/2018 4:43:05 PM  | ShippingToCustomer   | InProgress | Out For Delivery Today. Local Time : 9/13/2018 9:11:21 AM at Sunnyvale                                                                                
       9/13/2018 5:43:07 PM  | ShippingToCustomer   | Completed  | Delivered. Local Time : 9/13/2018 9:44:17 AM at SUNNYVALE                                                                                             
       9/14/2018 11:48:35 PM | ShippingToDataCenter | InProgress | Pickup Scan. Local Time : 9/14/2018 3:55:37 PM at Sunnyvale                                                                                                                                                                                 
       9/15/2018 1:52:35 AM  | ShippingToDataCenter | InProgress | Arrival Scan. Local Time : 9/14/2018 6:31:00 PM at San Jose                                                                                           
       9/15/2018 2:52:39 AM  | ShippingToDataCenter | InProgress | Departure Scan. Local Time : 9/14/2018 7:17:00 PM at San Jose                                                                                                                                                                             
       9/17/2018 8:23:31 AM  | ShippingToDataCenter | InProgress | Destination Scan. Local Time : 9/17/2018 4:14:37 AM at Chantilly                                                                                      
       9/17/2018 12:24:42 PM | ShippingToDataCenter | InProgress | Loaded on Delivery Vehicle. Local Time : 9/17/2018 7:45:36 AM at Chantilly                                                                            
       9/17/2018 1:25:11 PM  | ShippingToDataCenter | InProgress | Out For Delivery Today. Local Time : 9/17/2018 8:27:11 AM at Chantilly                                                                                
       9/17/2018 2:25:51 PM  | ShippingToDataCenter | Completed | Delivered. Local Time : 9/17/2018 9:56:32 AM at STERLING                                                                                              
       9/18/2018 9:55:41 PM  | DeviceBoot           | Completed | Appliance booted up successfully                                                                                                                      
       9/18/2018 11:00:25 PM | DataCopy             | Started   |                                                                                                                                                       
       9/18/2018 11:01:33 PM | DataCopy             | Completed | Copy Completed.                                                                                                                                       
       9/18/2018 11:20:58 PM | SecureErase          | Started   |                                                                                                                                                       
       9/18/2018 11:28:46 PM | SecureErase          | Completed | Azure Data Box:BY506B4B616700 has been sanitized according to NIST 800 -88 Rev 1.                                                                     
        
       ----------------------
       Data Box Job Log Links
       ----------------------
        
       Account Name         : eastusdryrun                                         
       Copy Logs Path       : copylog/copylogd695869a2a294396b7b903296c208388.xml                                                                                                                                                     
       Audit Logs Path      : azuredatabox-chainofcustodylogs\3b4cf163-f1af-475c-a391-f8afea3fa327\by506b4b616700                                                                                                                     
       BOM Files Path       : azuredatabox-chainofcustodylogs\3b4cf163-f1af-475c-a391-f8afea3fa327\by506b4b616700
       ```
     Você pode, em seguida, ir para sua conta de armazenamento e exibir os logs de cópia.

![Logs nas contas de armazenamento](media/data-box-portal-admin/logs-in-storage-acct-2.png)

Você também pode exibir a cadeia de logs de custódia que incluem os logs de auditoria e os arquivos BOM.

![Logs nas contas de armazenamento](media/data-box-portal-admin/logs-in-storage-acct-1.png)

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
|Limpar | Os dados nos discos do dispositivo foram apagados. A limpeza do dispositivo é considerada concluída quando o histórico de pedidos fica disponível no portal do Azure.|



## <a name="next-steps"></a>Próximas etapas

- Saiba como [Solucionar problemas do Data Box](data-box-faq.md).
