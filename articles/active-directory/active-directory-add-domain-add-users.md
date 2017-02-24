---
title: "Atribuir usuários a um domínio personalizado no Azure Active Directory | Microsoft Docs"
description: "Como preencher um domínio personalizado no Active Directory do Azure com contas de usuário."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 717b5a7c-7bc3-4ab1-98b5-4740b53338fe
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ccfa8d72b51c43e04d924d2b096e3cbc407af043


---
# <a name="assign-users-to-a-custom-domain"></a>Atribuir usuários a um domínio personalizado
Após ter adicionado seu domínio personalizado ao Active Directory do Azure, você deve adicionar as contas de usuário a esse domínio para que possa começar a autenticá-los.

## <a name="users-synced-in-from-a-directory-on-your-corporate-network"></a>Usuários sincronizados de um diretório em sua rede corporativa
Se você já configurou uma conexão entre seu Active Directory local e o Active Directory do Azure, a sincronização poderá preencher as contas. Para obter mais informações sobre como sincronizar o Active Directory do Azure com o Active Directory local, consulte [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).

## <a name="users-added-and-managed-in-the-cloud"></a>Usuários adicionados e gerenciados na nuvem
Para alterar o domínio para uma conta de usuário:

1. Abra o portal clássico do Azure usando uma conta que seja um administrador global ou administrador de usuário.
2. Abra seu diretório.
3. Clique a guia **Usuários** .
4. Selecione o usuário na lista.
5. Altere o domínio para o usuário e selecione **Salvar**.

Isso também pode ser feito usando o [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) ou a [API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## <a name="select-a-custom-domain-when-creating-a-new-user"></a>Selecione um domínio personalizado ao criar um novo usuário
1. Abra o portal clássico do Azure usando uma conta que seja um administrador global ou administrador de usuário.
2. Abra seu diretório.
3. Clique a guia **Usuários** .
4. Na barra de comandos, selecione **Adicionar**.
5. Quando adicionar o nome de usuário, escolha o domínio personalizado na lista de domínios.
6. Selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas
* [Como usar nomes de domínio personalizados para simplificar a experiência de conexão para os usuários](active-directory-add-domain.md)
* [Gerenciar nomes de domínio personalizados](active-directory-add-manage-domain-names.md)
* [Saiba mais sobre os conceitos de gerenciamento de domínio no AD do Azure](active-directory-add-domain-concepts.md)




<!--HONumber=Dec16_HO5-->


