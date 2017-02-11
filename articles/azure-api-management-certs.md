---
title: Carregar um Certificado de API de Gerenciamento do Azure | Microsoft Docs
description: "Saiba como carregar o certificado de API de gerenciamento para o Portal clássico do Azure."
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 783390791f27a9bf8ea400403a8b0f866f8c5cee
ms.openlocfilehash: 0f1ae486340270cac5cfdb5d44485d05cb996388


---
# <a name="upload-an-azure-management-api-management-certificate"></a>Carregar um Certificado de Gerenciamento de API do Gerenciamento do Azure
Certificados de gerenciamento permitem que você realize autenticação com a API de gerenciamento de serviço fornecida pelo Azure. Muitos programas e ferramentas (como o Visual Studio ou o SDK do Azure) usarão esses certificados para automatizar a configuração e a implantação de diversos serviços do Azure. **Isso se aplica somente ao portal clássico do Azure**.

> [!WARNING]
> Portanto, tenha cuidado! Esses tipos de certificados permitem que qualquer pessoa que os utilize para autenticação gerencie a assinatura à qual eles estão associados.
>
>

Há mais informações sobre certificados do Azure (incluindo a criação de um certificado autoassinado) [disponíveis](cloud-services/cloud-services-certs-create.md#what-are-management-certificates) para você, caso necessário.

Você também pode usar o [Azure Active Directory](https://azure.microsoft.com/en-us/services/active-directory/) para autenticar o código cliente, para fins de automação.

## <a name="upload-a-management-certificate"></a>Carregar um certificado de gerenciamento
Após criar um certificado de gerenciamento (arquivo .cer somente com a chave pública), você poderá carregá-lo no portal. Quando o certificado estiver disponível no portal, qualquer pessoa com um certificado correspondente (chave particular) pode conectar-se por meio da API de Gerenciamento e acessar os recursos para a assinatura associada.

1. Faça logon no [portal clássico do Azure](http://manage.windowsazure.com).
2. Certifique-se de selecionar a assinatura correta a qual você deseja associar um certificado. Pressione o texto **assinaturas** no canto superior direito do portal.

    ![Configurações](./media/azure-api-management-certs/subscription.png)
3. Depois de ter a assinatura correta selecionada, pressione **Configurações** no lado esquerdo do portal (talvez seja necessário rolar para baixo).

    ![Configurações](./media/azure-api-management-certs/settings.png)
4. Pressione a guia **Certificados de Gerenciamento** .

    ![Configurações](./media/azure-api-management-certs/certificates-tab.png)
5. Pressione o botão **Carregar** .

    ![Configurações](./media/azure-api-management-certs/upload.png)
6. Preencha as informações da caixa de diálogo e pressione a **marca de seleção**de conclusão.

    ![Configurações](./media/azure-api-management-certs/upload-dialog.png)

## <a name="next-steps"></a>Próximas etapas
Agora que você tem um certificado de gerenciamento associado a uma assinatura, você pode (depois de ter instalado localmente o certificado correspondente) conectar-se programaticamente à [API REST do Gerenciamento de Serviços](https://msdn.microsoft.com/library/azure/mt420159.aspx) e automatizar os diversos recursos do Azure que também estão associados à assinatura.



<!--HONumber=Feb17_HO2-->


