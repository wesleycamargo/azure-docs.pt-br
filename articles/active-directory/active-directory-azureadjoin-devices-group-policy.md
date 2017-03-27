---
title: "Conectar dispositivos ingressados no domínio ao Azure AD para experiências com Windows 10 | Microsoft Docs"
description: "Explica como os administradores podem configurar a Política de Grupo para permitir que dispositivos ingressem no domínio da rede corporativa."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: 2ff29f3e-5325-4f43-9baa-6ae8d6bad3e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 39bd4fcbe1b9a197bcf5b8bb33bf6fffae2063fc
ms.lasthandoff: 03/11/2017


---
# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10
O ingresso no domínio é a maneira tradicional usada pelas organizações para conectar dispositivos de trabalho pelo menos nos últimos 15 anos. Esse recurso permitiu que os usuários entrassem em seus dispositivos usando suas contas corporativas ou de estudante do Windows Server Active Directory (Active Directory) e permitiu que a TI gerenciasse totalmente esses dispositivos. As organizações geralmente dependem de métodos de geração de imagens para provisionar dispositivos aos usuários e geralmente usam o SCCM (System Center Configuration Manager) ou política de grupo para gerenciá-los.


O ingresso no domínio no Windows 10 fornece os seguintes benefícios depois de conectar os dispositivos ao Azure Active Directory do Azure (Azure AD):

* SSO (logon único) para recursos do AD do Azure em qualquer lugar
* Acesso à Windows Store corporativa usando contas corporativas ou de estudante (sem a exigência de conta da Microsoft)
* Roaming de configurações de usuário entre dispositivos compatível com a empresa usando a conta corporativa ou de estudante (sem a exigência de conta da Microsoft)
* Autenticação forte e entrada conveniente para a conta corporativa ou de estudante com o Windows Hello for Business e o Windows Hello
* Capacidade de restringir o acesso apenas aos dispositivos que estão em conformidade com as configurações da Política de Grupo do dispositivo organizacional

## <a name="prerequisites"></a>Pré-requisitos
O ingresso no domínio continua a ser útil. No entanto, para obter os benefícios que o AD do Azure pode oferecer, por exemplo, SSO, roaming de configurações com conta corporativa ou de estudante, acesso ao Windows Store com conta corporativa ou de estudante, você precisará do seguinte:

* Assinatura do Azure AD
* Azure AD Connect para estender o diretório local ao AD do Azure
* Política definida para conectar dispositivos ingressados no domínio ao AD do Azure
* Compilação do Windows 10 (compilação 10551 ou mais recente) para dispositivos

Para habilitar o Windows Hello for Business e o Windows Hello, você precisará também do seguinte:

- **Infraestrutura de chave pública (PKI)** para a emissão de certificados de usuário.

- **Ramificação atual do System Center Configuration Manager** - Você precisa instalar a versão 1606 ou superior.  
Para obter mais informações, confira: 
    - [Documentação para o System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx)
    - [Blog da Equipe do System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)
    - [Configurações do Windows Hello for Business no System Center Configuration Manager](https://docs.microsoft.com/sccm/protect/deploy-use/windows-hello-for-business-settings)

Como alternativa ao requisito de implantação de PKI, você pode fazer o seguinte:

* Ter alguns controladores de domínio com os Serviços de Domínio do Active Directory do Windows Server 2016.

Para habilitar o acesso condicional, você pode criar configurações de Política de Grupo que permitam acesso a dispositivos ingressados no domínio sem implantações adicionais. Para gerenciar o controle de acesso baseado na conformidade do dispositivo, você precisará do seguinte:

* Ramificação Atual do System Center Configuration Manager (1606 ou posterior) para cenários do Windows Hello for Business

## <a name="deployment-instructions"></a>Instruções de implantação

Para implantar, siga as etapas listadas em [Registro de dispositivo automático com o Azure Active Directory para dispositivos ingressados no domínio do Windows](active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="next-step"></a>Próxima etapa
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)


