---
title: "Adicionar um usuário à sua coleção do Azure RemoteApp | Microsoft Docs"
description: "Saiba como adicionar usuários à sua coleção do RemoteApp do Azure"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 6b751fd2-2b11-499f-a2eb-12cb47f3129b
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3dbeba840501f395c9bad07109fe8ea47947a5fa


---
# <a name="how-to-add-a-user-to-your-azure-remoteapp-collection"></a>Como adicionar um usuário à sua coleção do RemoteApp do Azure
> [!IMPORTANT]
> O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Antes que os usuários possam ver e usar seus aplicativos no RemoteApp do Azure, você precisa conceder acesso à sua coleção a eles. Essa é a parte fácil: na guia **Acesso de Usuário** , insira as informações da conta para o usuário e depois clique na marca de seleção.

Quais informações da conta são necessárias? Isso depende do tipo de coleção criada (nuvem ou híbrida) e se você estiver usando Office 365 ProPlus nessa coleção.

## <a name="supported-user-identities"></a>Identidades de usuário com suporte
Os tipos de coleção diferentes (nuvem versus híbridos) são compatíveis com o uso de identidades de usuário diferentes para acesso aos aplicativos.  

Para obter uma coleção híbrida do RemoteApp, você precisa configurar uma infraestrutura de domínio do Active Directory local e um locatário do Active Directory do Azure com a Integração de diretório (e opcionalmente logon único). Além disso, você precisa criar alguns objetos do Active Directory no diretório local.  

Para uma coleção de nuvem do RemoteApp, qualquer usuário que tenha o Active Directory do Azure que dá suporte a identidades pode receber acesso de usuário para o RemoteApp para incluir as Contas da Microsoft.  Consulte a tabela abaixo.

Os usuários do Office 365 são usuários do Active Directory do Azure. Se eles tiverem o Active Directory do Azure híbrido, as contas sincronizadas do diretório, eles poderão receber acesso de usuário em uma implantação híbrida do RemoteApp.   

Você pode usar essa tabela como uma referência rápida sobre qual identidade tem suporte em sua coleção e quais são os requisitos do Active Directory.

| Contas de usuário | Nuvem | Híbrido |
| --- | --- | --- |
| Conta da Microsoft |Sim |Não |
| Active Directory do Azure (Azure AD) | | |
| Somente nuvem do Azure AD |Sim |Não |
| ADsync com sincronização de senha |Sim |Sim |
| ADsync sem sincronização de senha |Sim |Não |
| ADsync com o AD FS |Sim |sim |
| [Provedores de identidade de terceiros com suporte do Azure](https://msdn.microsoft.com/library/azure/jj679342.aspx) (por exemplo, Ping) |Sim |Sim |
| Multi-Factor Authentication |Sim |Sim |

Verifique [obter mais informações](remoteapp-ad.md) sobre a configuração do Active Directory para o RemoteApp.

> [!NOTE]
> Os usuários do Azure Active Directory devem ser do locatário que está associado à sua assinatura. (Você pode exibir e modificar a sua assinatura na guia **Configurações** no portal. Consulte [Alterar o locatário do Active Directory do Azure usado pelo RemoteApp](remoteapp-changetenant.md) para obter mais informações.)
> 
> 

## <a name="office-365-proplus-user-account-information"></a>Informações de conta de usuário do Office 365 ProPlus
Se você estiver usando a imagem do modelo do Office 365 ProPlus em sua coleção *ou* se você criou uma imagem personalizada que usa o Office 365, será possível adicionar apenas usuários do Azure Active Directory que têm assinaturas do Office 365 para o domínio padrão da sua assinatura. Consulte [Usando o Office 365 com o Azure RemoteApp](remoteapp-o365.md) para obter mais informações.




<!--HONumber=Nov16_HO3-->


