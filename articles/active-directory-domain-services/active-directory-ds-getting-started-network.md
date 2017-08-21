---
title: "Azure Active Directory Domain Services: introdução | Microsoft Docs"
description: "Habilite o Azure Active Directory Domain Services usando o portal do Azure (Versão prévia)"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 7f420d60862adf61e4f21e5abac2932a742bd55d
ms.contentlocale: pt-br
ms.lasthandoff: 07/17/2017

---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal-preview"></a>Habilite o Azure Active Directory Domain Services usando o portal do Azure (Versão prévia)


## <a name="before-you-begin"></a>Antes de começar
Consulte as [Considerações de rede para o Azure Active Directory Domain Services](active-directory-ds-networking.md).


## <a name="task-2-configure-network-settings"></a>Tarefa 2: definir as configurações de rede
A próxima tarefa de configuração é criar uma rede virtual do Azure e uma sub-rede dedicada dentro dela. Você pode habilitar o Azure Active Directory Domain Services nessa sub-rede dentro de sua rede virtual. Você também pode escolher uma rede virtual existente e criar a sub-rede dedicada dentro dela.

1. Clique em **Rede virtual** para selecionar uma rede virtual.
2. Na folha **Escolher rede virtual**, você vê todas as redes virtuais desejadas. Você vê somente as redes virtuais que pertencem ao grupo de recursos e ao local do Azure que selecionou na página do assistente **Básico**.

3. Selecione a rede virtual na qual o Azure AD Domain Services deve ser habilitado. Clique em **Criar novo** se preferir criar uma nova rede virtual. É altamente recomendável usar uma sub-rede dedicada para o Azure AD Domain Services. Se você escolher uma rede virtual existente, [crie uma sub-rede dedicada usando a extensão de redes virtuais](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) e, em seguida, selecione essa sub-rede. 

    ![Escolher rede virtual](./media/getting-started/domain-services-blade-network-pick-vnet.png)

4. Clique em **Sub-rede** para escolher a sub-rede dedicada nessa rede virtual, na qual você deseja habilitar o novo domínio gerenciado. Na folha **Criar sub-rede**, especifique um nome para a sub-rede e clique em **OK** quando terminar. Por exemplo, crie uma sub-rede com o nome "DomainServices", tornando mais fácil para outros administradores entender o que está implantado dentro da sub-rede.

    ![Escolher sub-rede na rede virtual](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **Diretrizes para selecionar uma sub-rede**
  > 1. Use uma sub-rede dedicada para o Azure AD Domain Services. Não implante nenhuma outra máquina virtual nessa sub-rede. Essa configuração permite que você configure NSGs (grupos de segurança de rede) para suas máquinas virtuais/cargas de trabalho sem interromper seu domínio gerenciado. Para obter detalhes, consulte as [Considerações de rede do Azure Active Directory Domain Services](active-directory-ds-networking.md).
  2. Não selecione a sub-rede do Gateway para implantar o Azure AD Domain Services, pois essa não é uma configuração com suporte.
  3. Verifique se a sub-rede que você selecionou tem espaço de endereço disponível suficiente – pelo menos 3 a 5 endereços IP.
  >

5. Quando terminar, clique em **OK** para ir para a página **Grupo de administradores** do assistente.


## <a name="next-step"></a>Próxima etapa
[Tarefa 3: configurar o grupo administrativo e habilitar o Azure AD Domain Services](active-directory-ds-getting-started-admingroup.md)

