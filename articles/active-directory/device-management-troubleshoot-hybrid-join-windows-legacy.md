---
title: "Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos de nível inferior | Microsoft Docs"
description: "Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos de nível inferior."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 5657df412b1f2b7d4d43d7551289620ae4d77de2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos de nível inferior 

Este tópico é aplicável apenas aos seguintes dispositivos: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Para Windows 10 ou Windows Server 2016, confira [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos do Windows 10 e do Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md).

Este tópico pressupõe que você tenha [dispositivos configurados e ingressados no Azure Active Directory híbrido](device-management-hybrid-azuread-joined-devices-setup.md) para dar suporte aos seguintes cenários:

- Acesso condicional com base em dispositivo

- [Roaming corporativo de configurações](active-directory-windows-enterprise-state-roaming-overview.md)

- [Configurar o Hello for Business](active-directory-azureadjoin-passport-deployment.md) 





Este tópico fornece orientação para solução de possíveis problemas.  

**O que você deve saber:** 

- O número máximo de dispositivos por usuário é centrado no dispositivo. Por exemplo, se *jdoe* e *jharnett* entrarem no dispositivo, um registro separado (DeviceID) será criado para cada um desses usuários na guia de informações do **USUÁRIO**.  

- O registro inicial/ingresso de dispositivos está configurado para executar uma tentativa de logon ou de bloqueio/desbloqueio. Pode haver um atraso de cinco minutos disparado por uma tarefa do agendador de tarefas. 

- Uma reinstalação do sistema operacional, ou um cancelamento manual do registro seguido por um novo registro, pode criar um novo registro no Azure AD, e resultará em várias entradas na guia Informações do USUÁRIO no Portal do Azure. 


## <a name="step-1-retrieve-the-registration-status"></a>Etapa 1: Recuperar o status do registro 

**ara verificar o status do registro:**  

1. Abra o prompt de comando como administrador 

2. Digite `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`

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
    
    1. Se o usuário conectado não for um usuário de domínio (por exemplo, um usuário local). O ingresso no Azure AD Híbrido em dispositivos de nível inferior tem suporte apenas para usuários do domínio.
    
    2. Se, por qualquer motivo, o Autoworkplace.exe não puder autenticar silenciosamente com o Azure Active Directory ou o AD FS. Algumas razões possíveis podem ser problemas de conectividade de rede de saída para URLs do Azure Active Directory (verifique os pré-requisitos) ou se a MFA está habilitada/configurada para o usuário, mas WIAORMUTLIAUTHN não está configurado no servidor de federação (verifique as etapas de configuração). Outra possibilidade é que a página de HRD (descoberta de realm inicial) esteja aguardando interação do usuário, impedindo o Autoworkplace.exe obter silenciosamente 
    
    3. Se a organização estiver usando Logon Único Contínuo do Azure Active Directory, as URLs a seguir não estarão presentes nas configurações de intranet do IE do dispositivo:
    - https://autologon.microsoftazuread-sso.com
    - https://aadg.windows.net.nsatc.net
    
    e a configuração "Permitir atualizações da barra de status via script" deve estar habilitada para a zona da Intranet.

- Você atingiu uma cota

    ![Workplace Join para Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- O serviço não está respondendo 

    ![Workplace Join para Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

Também é possível encontrar informações de status no log de eventos em **Log de Aplicativos e Serviços\Microsoft-Workplace Join**.
  
**As causas mais comuns para a falha do ingresso do Azure AD híbrido são:** 

- O computador não está na rede interna da organização, ou está em uma VPN sem uma conexão com um controlador de domínio do AD local.

- Você está conectado ao computador com uma conta de computador local. 

- Problemas de configuração do serviço: 

  - O servidor de Federação foi configurado para oferecer suporte a **WIAORMULTIAUTHN**. 

  - Não há nenhum objeto de Ponto de Conexão de Serviço que aponta para o seu nome de domínio verificado no Azure AD na floresta do AD à qual o computador pertence.

  - Um usuário atingiu o limite de dispositivos. 

## <a name="next-steps"></a>Próximas etapas

Para perguntas, consulte as [Perguntas frequentes sobre o gerenciamento de dispositivos](device-management-faq.md)  
