---
title: Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos de nível inferior | Microsoft Docs
description: Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos de nível inferior.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: d41e83c11f33b0bcbe4ea632332f2cd8bb12313f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714105"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos de nível inferior 

Este aplicativo é aplicável apenas aos seguintes dispositivos: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Para Windows 10 ou Windows Server 2016, confira [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos do Windows 10 e do Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md).

Este artigo pressupõe que você tenha [dispositivos configurados e ingressados no Azure Active Directory híbrido](device-management-hybrid-azuread-joined-devices-setup.md) para dar suporte aos seguintes cenários:

- Acesso condicional com base em dispositivo

- [Roaming corporativo de configurações](active-directory-windows-enterprise-state-roaming-overview.md)

- [Configurar o Hello for Business](active-directory-azureadjoin-passport-deployment.md) 





Este artigo fornece orientação para solução de possíveis problemas.  

**O que você deve saber:** 

- O número máximo de dispositivos por usuário é centrado no dispositivo. Por exemplo, se *jdoe* e *jharnett* entrarem no dispositivo, um registro separado (DeviceID) será criado para cada um desses usuários na guia de informações do **USUÁRIO**.  

- O registro inicial/ingresso de dispositivos está configurado para executar uma tentativa de logon ou de bloqueio/desbloqueio. Pode haver um atraso de cinco minutos disparado por uma tarefa do agendador de tarefas. 

- Uma reinstalação do sistema operacional ou a realização de novos registros manuais podem criar um novo registro no Azure AD, o que resulta em várias entradas na guia de informações do USUÁRIO no portal do Azure. 

## <a name="step-1-retrieve-the-registration-status"></a>Etapa 1: Recuperar o status do registro 

**ara verificar o status do registro:**  

1. Logon com a conta de usuário que executou uma associação do Microsoft Azure Active Directory híbrido.

2. Abra o prompt de comando como administrador 

3. Digite `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Esse comando exibe uma caixa de diálogo que fornece mais detalhes sobre o status do ingresso.

![Workplace Join para Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Etapa 2: Avaliar o status do ingresso do Azure AD híbrido 

Se o ingresso no Azure AD híbrido não tiver sido bem-sucedido, a caixa de diálogo fornecerá detalhes sobre o problema.

**As tarefas mais comuns são:**

- Um AD FS ou Azure AD configurado incorretamente

    ![Workplace Join para Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- Você não está conectado como um usuário de domínio

    ![Workplace Join para Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)
    
    Há algumas razões diferentes pelas quais isso pode ocorrer:
    
    - O usuário conectado não é um usuário de domínio (por exemplo, um usuário local). O ingresso no Azure AD Híbrido em dispositivos de nível inferior tem suporte apenas para usuários do domínio.
    
    - O Autoworkplace.exe não pode autenticar silenciosamente com o Microsoft Azure Active Directory ou o AD FS. Isso pode ser devido a um problemas de conectividade de rede de saída para as URLs do Azure AD. Também é possível que a MFA (autenticação multifator) esteja habilitada/configurada para o usuário e WIAORMUTLIAUTHN não esteja configurada no servidor de federação. Outra possibilidade é que a página de descoberta de domínio doméstico (HRD) está aguardando a interação do usuário, que evita que o **autoworkplace.exe** obtenha um token silenciosamente.
    
    - Sua organização usa o Logon Único Contínuo do Microsoft Azure Active Directory `https://autologon.microsoftazuread-sso.com` ou `https://aadg.windows.net.nsatc.net` não está presente nas configurações de intranet do Internet Explorer do dispositivo, e **Permitir atualizações à barra de status por meio de script** não está habilitada para a zona da Intranet.

- Você atingiu uma cota

    ![Workplace Join para Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- O serviço não está respondendo 

    ![Workplace Join para Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

Também é possível encontrar informações de status no log de eventos em: **Log de Aplicativos e Serviços\Microsoft-Workplace Join**
  
**As causas mais comuns para a falha do ingresso do Azure AD híbrido são:** 

- O computador não está conectado à rede interna da organização nem a uma VPN com conexão ao controlador de domínio do AD local.

- Você está conectado ao computador com uma conta de computador local. 

- Problemas de configuração do serviço: 

  - O servidor de Federação foi configurado para oferecer suporte a **WIAORMULTIAUTHN**. 

  - A floresta do seu computador não tem objeto de Ponto de Conexão de Serviço que aponta o seu nome de domínio verificado no Microsoft Azure Active Directory 

  - Um usuário atingiu o limite de dispositivos. 

## <a name="next-steps"></a>Próximas etapas

Para perguntas, consulte as [Perguntas frequentes sobre o gerenciamento de dispositivos](device-management-faq.md)  
