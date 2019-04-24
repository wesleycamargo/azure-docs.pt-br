---
title: Registrar-se no Azure NetApp Files | Microsoft Docs
description: Descreve como enviar uma solicitação para inscrever-se no serviço Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2018
ms.author: b-juche
ms.openlocfilehash: 86c016a5dbcc0d78378e59bc6b3606ddf2c54f64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452750"
---
# <a name="register-for-azure-netapp-files"></a>Registro no Azure NetApp Files
Antes de usar o Azure NetApp Files, é necessário enviar uma solicitação para inscrever-se no serviço Azure NetApp Files.  Após o registro, inscreva-se para usar o serviço.

## <a name="request-to-enroll-in-the-service"></a>Solicitação para inscrever-se no serviço
É necessário fazer parte do programa de Versão Prévia Pública e estar na lista de permissões para acessar o Provedor de recursos do Microsoft.NetApp. Para obter mais detalhes sobre como ingressar no programa de Visualização Pública, confira a [Página de inscrição de versão prévia pública do Azure NetApp Files](https://aka.ms/nfspublicpreview). 


## <a name="register-the-netapp-resource-provider"></a>Registrar o Provedor de recursos do NetApp

Para usar o serviço, é necessário registrar o Provedor de recursos do Azure no Azure NetApp Files. 

1. No portal do Azure, clique no ícone do Azure Cloud Shell no canto superior direito:

      ![ícone do Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Se você tiver várias assinaturas em sua conta do Azure, selecione a que foi incluída na lista de permissões para o Azure NetApp Files:
    
        az account set --subscription <subscriptionId>

3. No console do Azure Cloud Shell, insira o seguinte comando para verificar se sua assinatura foi incluída na lista de permissões:
    
        az feature list | grep NetApp

   A saída do comando é exibida da seguinte maneira:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/publicPreviewADC",  
       "name": "Microsoft.NetApp/publicPreviewADC" 
       
   `<SubID>` é a ID da assinatura.

4. No console do Azure Cloud Shell, insira o seguinte comando para registrar o Provedor de recursos do Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait

   O parâmetro `--wait` instrui o console a aguardar a conclusão do registro. O processo de registro pode levar algum tempo para ser concluído.

5. No console do Azure Cloud Shell, insira o seguinte comando para verificar se o Provedor de recursos do Azure foi registrado: 
    
        az provider show --namespace Microsoft.NetApp

   A saída do comando é exibida da seguinte maneira:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` é a ID da assinatura.  O valor do parâmetro `state` indica `Registered`.

6. No portal do Azure, clique na folha **Assinaturas**.
7. Na folha Assinaturas, clique na ID de assinatura. 
8. Nas configurações da assinatura, clique em **Provedores de recursos** para verificar se o Provedor do Microsoft.NetApp indica o status Registrado: 

      ![Microsoft.NetApp registrado](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Próximos passos  

[Criar uma conta do NetApp](azure-netapp-files-create-netapp-account.md)