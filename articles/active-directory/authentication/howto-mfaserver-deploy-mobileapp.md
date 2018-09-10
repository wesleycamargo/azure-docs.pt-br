---
title: Serviço Web de aplicativo móvel do servidor MFA do Azure | Microsoft Docs
description: Configure o servidor MFA para enviar notificações por push para usuários com o Aplicativo Autenticador da Microsoft.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: d31cf6569f642d2c7b61abe25148f5cfb199f0a2
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159433"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Habilitar a autenticação de aplicativo móvel com o Servidor de Autenticação Multifator do Azure

O aplicativo Microsoft Authenticator oferece uma opção de verificação adicional fora de banda. Em vez de enviar uma chamada telefônica automatizada ou um SMS para o usuário durante o logon, a Autenticação Multifator do Azure envia uma notificação por push ao aplicativo Microsoft Authenticator no smartphone ou tablet do usuário. O usuário simplesmente toca em **Verificar** (ou digita um PIN e toca em "Autenticar") no aplicativo para concluir a entrada.

É preferível a utilização de um aplicativo móvel para a verificação em duas etapas quando a recepção do telefone não é confiável. Se você usar o aplicativo como um gerador de token OATH, ele não exigirá nenhuma conexão de rede ou Internet.

> [!IMPORTANT]
> Se você tiver instalado o Azure Multi-Factor Authentication Server v8.x ou superior, a maioria das etapas a seguir não será necessária. A autenticação do aplicativo móvel pode ser configurada seguindo as etapas em [Configurar o aplicativo móvel](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).

## <a name="requirements"></a>Requisitos

Para usar o aplicativo Microsoft Authenticator, você deve estar executando o servidor Azure Multi-Factor Authentication v8.x ou superior

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configurar as definições do aplicativo móvel no Servidor de Autenticação Multifator do Azure

1. No console do Servidor de Autenticação Multifator, clique no ícone Portal do Usuário. Se os usuários têm permissão para controlar seus métodos de autenticação, marque **Aplicativo Móvel** na guia Configurações, em **Permitir que usuários selecionem o método**. Sem esse recurso habilitado, os usuários finais precisam entrar em contato com o Suporte Técnico para concluir a ativação para Aplicativos Móveis.
2. Marque a caixa **Permitir que usuários ativem o aplicativo móvel**.
3. Marque a caixa **Permitir registro de usuário**.
4. Clique no ícone **Aplicativos Móveis**.
5. Preencha o campo **Nome da conta** com o nome da empresa ou organização a ser exibido no aplicativo móvel para a conta.
   ![Configurações de Aplicativo Móvel de configuração do Servidor MFA](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>Próximas etapas

- [Cenários avançados com a Autenticação Multifator do Azure e VPNs de terceiros](howto-mfaserver-nps-vpn.md).
