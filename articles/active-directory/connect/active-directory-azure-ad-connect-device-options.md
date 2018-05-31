---
title: 'Azure AD Connect: opções de dispositivo| Microsoft Docs'
description: Este documento detalha as opções de dispositivo disponíveis no Azure AD Connect
services: active-directory
documentationcenter: ''
author: anandy
manager: samueld
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: anandy
ms.openlocfilehash: d592cbb55643099b6b628a44f182d0a079a6c71e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354496"
---
#<a name="azure-ad-connect-device-options"></a>Do Azure AD Connect: opções de dispositivo

A documentação a seguir fornece informações sobre as várias opções de dispositivo disponíveis no Azure AD Connect. Você pode usar o Azure AD Connect para configurar as duas operações a seguir: 
* **Ingresso no Azure AD híbrido**: se seu ambiente tiver um volume local do AD e você também quiser se beneficiar dos recursos fornecidos pelo Azure Active Directory, é possível implementar dispositivos adicionados ao Azure AD híbrido. Esses dispositivos são adicionados ao Active Directory local e ao Azure Active Directory.
* **Write-back de dispositivo**: usado para habilitar o acesso condicional baseado em dispositivos para os dispositivos protegidos do AD FS (2012 R2 ou superior)

## <a name="configure-device-options-in-azure-ad-connect"></a>Opções de logon único do usuário no Azure AD Connect

1.  Executar o Azure AD Connect. Na página **Tarefas adicionais**, selecione **Configurar opções de dispositivo**.
    ![Configurar opções do dispositivo](./media/active-directory-aadconnect-device-options/deviceoptions.png) 

    Quando você clicar em Avançar, uma página **visão geral** é exibida, que fornece detalhes sobre as operações que podem ser executadas.
    ![Visão geral](./media/active-directory-aadconnect-device-options/deviceoverview.png)

    >[!NOTE]
    > As novas opções de dispositivo de configuração estão disponíveis somente na versão 1.1.819.0 e mais recente.

2.  Depois de fornecer as credenciais do Microsoft Azure Active Directory, você pode escolher a operação a ser executada na página de opções do dispositivo.
    ![Operações do Dispositivo](./media/active-directory-aadconnect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Próximas etapas

* [Configurar associação Híbrida do Microsoft Azure Active Directory](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Configurar/desativar o write-back do dispositivo](./active-directory-aadconnect-feature-device-writeback.md)

