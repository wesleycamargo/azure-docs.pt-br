---
title: "Solução de problemas com o registro automático de computadores ingressados no domínio do Azure AD para clientes de nível inferior do Windows | Microsoft Docs"
description: "Solução de problemas com o registro automático de computadores ingressados no domínio do Azure AD para clientes de nível inferior do Windows."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 535badb5444fd4c29211f83556c0a750ee8adf70
ms.openlocfilehash: 0e031760b84aa59fefda2469606475b36dbc1ff7


---
# <a name="troubleshooting-the-auto-registration-of-azure-ad-domain-joined-computers-for-windows-down-level-clients"></a>Solução de problemas com o registro automático de computadores ingressados no domínio do Azure AD para clientes de nível inferior do Windows 

Este tópico é aplicável apenas aos seguintes clientes: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Para o Windows 10 ou o Windows Server 2016, confira [Solucionando problemas do registro automático de computadores ingressados no domínio do Azure AD para Windows 10 e Windows Server 2016](active-directory-conditional-access-automatic-device-registration-troubleshoot-windows.md).

Este tópico pressupõe que você tenha configurado o registro automático de dispositivos ingressados no domínio conforme descrito em [Como configurar o registro automático de dispositivos ingressados no domínio do Windows com o Azure Active Directory](active-directory-conditional-access-automatic-device-registration-get-started.md).
 
Este tópico fornece orientação para solução de possíveis problemas.  
Para ter resultados bem-sucedidos, fique atento ao seguinte: 

- O registro desses clientes no Azure AD ocorre por dispositivo/usuário. Por exemplo: se jdoe e jharnett efetuarem logon no dispositivo, um registro separado (DeviceID) será criado para cada um desses usuários na guia Informações do USUÁRIO.  

- O registro desses clientes prontos para uso é configurado para ser tentado durante o logon ou durante um bloqueio/desbloqueio, e pode haver atraso de cinco minutos desde o disparo por meio de uma tarefa do Agendador de Tarefas. 

- Uma reinstalação do sistema operacional, ou um cancelamento manual do registro seguido por um novo registro, pode criar um novo registro no Azure AD e resultará em várias entradas na guia Informações do USUÁRIO no Portal do Azure. 


## <a name="step-1-retrieve-the-registration-status"></a>Etapa 1: Recuperar o status do registro 

**ara verificar o status do registro:**  

1. Abra o prompt de comando como administrador 

2. Digite `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`

Esse comando exibe uma caixa de diálogo que fornece mais detalhes sobre o status do ingresso.

![Workplace Join para Windows](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-registration-status"></a>Etapa 2: Avaliar o status do registro 

Se o ingresso não tiver sido bem-sucedido, a caixa de diálogo fornecerá detalhes sobre o problema.

**As tarefas mais comuns são:**

- Um AD FS ou Azure AD configurado incorretamente

    ![Workplace Join para Windows](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/02.png)

- Você não está conectado como um usuário de domínio

    ![Workplace Join para Windows](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/03.png)

- Você atingiu uma cota

    ![Workplace Join para Windows](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/04.png)

- O serviço não está respondendo 

    ![Workplace Join para Windows](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/05.png)

Também é possível encontrar informações de status no log de eventos em **Log de Aplicativos e Serviços\Microsoft-Workplace Join**.
  
**As causas mais comuns para um registro com falha são:** 

- O computador não está na rede interna da organização, ou está em uma VPN sem uma conexão com um controlador de domínio do AD local.

- Você está conectado ao computador com uma conta de computador local. 

- Problemas de configuração do serviço: 

  - O servidor de Federação foi configurado para oferecer suporte a **WIAORMULTIAUTHN**. 

  - Não há nenhum objeto de Ponto de Conexão de Serviço que aponta para o seu nome de domínio verificado no Azure AD na floresta do AD à qual o computador pertence.

  - Um usuário atingiu o limite de dispositivos. Consulte Introdução ao Registro de Dispositivo do Azure Active Directory.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, veja [Perguntas frequentes sobre o registro de dispositivo automático](active-directory-conditional-access-automatic-device-registration-faq.md) 


<!--HONumber=Feb17_HO1-->


