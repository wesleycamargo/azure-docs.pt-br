---
title: "Solucionar problemas do Azure RemoteApp - falhas de conexão e inicialização do aplicativo | Microsoft Docs"
description: "Saiba como solucionar problemas com a inicialização e a conexão a aplicativos no Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: e5cf7171-d1c2-4053-a38b-5af7821305e1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 4e6c6571be85cd5fe374c7df644741ae017ccd19
ms.openlocfilehash: 6db4cf048147cb9559ca1a2e6921c679fe6ec9e5


---
# <a name="troubleshoot-azure-remoteapp---application-launch-and-connection-failures"></a>Solucionar problemas do Azure RemoteApp - falhas de conexão e inicialização do aplicativo
> [!IMPORTANT]
> O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Aplicativos hospedados no Azure RemoteApp podem falhar ao inicializar por alguns motivos diferentes. Este artigo descreve vários motivos e mensagens de erro que os usuários podem receber quando tentam inicializar aplicativos. Ele também fala sobre falhas de conexão. (Mas o artigo não descreve problemas ao entrar no cliente do Azure RemoteApp).  

Continue lendo para obter informações sobre mensagens de erro comuns devido a falhas de conexão e inicialização do aplicativo.

## <a name="were-getting-you-set-up-try-again-in-10-minutes"></a>Estamos configurando para você... Tente novamente em 10 minutos.
Este erro significa que o Azure RemoteApp está ampliando sua capacidade para atender às necessidades de seus usuários. Em segundo plano, mais instâncias de VM do Azure RemoteApp estão sendo criadas para atender às necessidades de capacidade de seus usuários. Normalmente, isso leva cerca de cinco, mas pode levar até 10 minutos. Às vezes, isso não acontece rápido o suficiente e os recursos são necessários imediatamente. Por exemplo, um cenário às 9h em que vários usuários precisam usar seu aplicativo no Azure RemoteAppn ao mesmo tempo. Se isso ocorrer, poderemos habilitar o **modo de capacidade** no back-end. Para fazer isso, abra um tíquete de suporte do Azure. Certifique-se de incluir sua ID de assinatura na solicitação.  

![Estamos configurando para você](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## <a name="could-not-auto-reconnect-to-your-applications-please-re-launch-your-application"></a>Não foi possível se reconectar automaticamente aos seus aplicativos, inicialize novamente seu aplicativo
Esta mensagem de erro normalmente é vista se estava usando o Azure RemoteApp, colocou seu computador em suspensão por mais de 4 horas e, em seguida, ativou o computador e o cliente do Azure RemoteApp tentou se reconectar automaticamente e o tempo limite foi excedido.  Instrua os usuários a navegar de volta para o aplicativo e tentar abri-lo no cliente do Azure RemoteApp.

![Não foi possível reconectar automaticamente aos seus aplicativos](./media/remoteapp-apptrouble/ra-apptrouble2.png) 

## <a name="problems-with-the-temp-profile"></a>Problemas com o perfil temporário
Este erro ocorre quando seu perfil de usuário (disco de perfil do usuário) não foi montado e o usuário recebeu um perfil temporário.  Os administradores devem navegar para a coleção no portal do Azure e ir até a guia **Sessões** e tentar **Fazer logoff** do usuário. Isso forçará o logoff completo da sessão de usuário - em seguida, peça que o usuário tente inicializar um aplicativo novamente. Se não funcionar, entre em contato com o suporte do Azure.

## <a name="azure-remoteapp-has-stopped-working"></a>O Azure RemoteApp parou de funcionar
Esta mensagem de erro significa que o cliente do Azure RemoteApp está tendo um problema e precisa ser reiniciado. Instrua os usuários a fechar: selecione **Fechar programa** e reinicie o cliente do Azure RemoteApp.  Se o problema persistir, abra um tíquete de suporte do Azure.

![O Azure RemoteApp parou de funcionar](./media/remoteapp-apptrouble/ra-apptrouble3.png)  

## <a name="an-error-occurred-while-remote-desktop-connection-was-accessing-this-resource-retry-the-connection-or-contact-your-system-administrator"></a>Ocorreu um erro enquanto a Conexão de Área de Trabalho Remota estava acessando este recurso. Tente conectar novamente ou entre em contato com o administrador do sistema
Esta é uma mensagem de erro genérica — entre em contato com o suporte do Azure para que possamos investigar. 

![Mensagem genérica do Azure RemoteApp](./media/remoteapp-apptrouble/ra-apptrouble4.png) 




<!--HONumber=Jan17_HO1-->


