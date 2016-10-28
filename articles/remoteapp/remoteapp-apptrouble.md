<properties 
    pageTitle="Solucionar problemas do Azure RemoteApp - falhas de conexão e inicialização do aplicativo | Microsoft Azure" 
    description="Saiba como solucionar problemas com a inicialização e a conexão a aplicativos no Azure RemoteApp." 
    services="remoteapp" 
	documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



#Solucionar problemas do Azure RemoteApp - falhas de conexão e inicialização do aplicativo 

> [AZURE.IMPORTANT]
O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Aplicativos hospedados no Azure RemoteApp podem falhar ao inicializar por alguns motivos diferentes. Este artigo descreve vários motivos e mensagens de erro que os usuários podem receber quando tentam inicializar aplicativos. Ele também fala sobre falhas de conexão. (Mas o artigo não descreve problemas ao entrar no cliente do Azure RemoteApp).

Continue lendo para obter informações sobre mensagens de erro comuns devido a falhas de conexão e inicialização do aplicativo.

##Estamos configurando para você... Tente novamente em 10 minutos.

Este erro significa que o Azure RemoteApp está ampliando sua capacidade para atender às necessidades de seus usuários. Em segundo plano, mais instâncias de VM do Azure RemoteApp estão sendo criadas para atender às necessidades de capacidade de seus usuários. Normalmente, isso leva cerca de cinco, mas pode levar até 10 minutos. Às vezes, isso não acontece rápido o suficiente e os recursos são necessários imediatamente. Por exemplo, um cenário às 9h em que vários usuários precisam usar seu aplicativo no Azure RemoteAppn ao mesmo tempo. Se isso ocorrer, poderemos habilitar o **modo de capacidade** no back-end. Para fazer isso, abra um tíquete de suporte do Azure e/ou envie um email para [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com). Certifique-se de incluir sua ID de assinatura na solicitação.

![Estamos configurando para você](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## Não foi possível se reconectar automaticamente aos seus aplicativos, inicialize novamente seu aplicativo  

Esta mensagem de erro normalmente é vista se estava usando o Azure RemoteApp, colocou seu computador em suspensão por mais de 4 horas e, em seguida, ativou o computador e o cliente do Azure RemoteApp tentou se reconectar automaticamente e o tempo limite foi excedido. Instrua os usuários a navegar de volta para o aplicativo e tentar abri-lo no cliente do Azure RemoteApp.

![Não foi possível reconectar automaticamente aos seus aplicativos](./media/remoteapp-apptrouble/ra-apptrouble2.png)

## Problemas com o perfil temporário 

Este erro ocorre quando seu perfil de usuário (disco de perfil do usuário) não foi montado e o usuário recebeu um perfil temporário. Os administradores devem navegar para a coleção no portal do Azure e ir até a guia **Sessões** e tentar **Fazer logoff** do usuário. Isso forçará o logoff completo da sessão de usuário - em seguida, peça que o usuário tente inicializar um aplicativo novamente. Se isso falhar, entre em contato com o suporte do Azure ou envie um email para [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

## O Azure RemoteApp parou de funcionar

Esta mensagem de erro significa que o cliente do Azure RemoteApp está tendo um problema e precisa ser reiniciado. Instrua os usuários a fechar: selecione **Fechar programa** e reinicie o cliente do Azure RemoteApp. Se o problema persistir, abra um tíquete de suporte do Azure e/ou envie um email para [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

![O Azure RemoteApp parou de funcionar](./media/remoteapp-apptrouble/ra-apptrouble3.png)

## Ocorreu um erro enquanto a Conexão de Área de Trabalho Remota estava acessando este recurso. Tente conectar novamente ou entre em contato com o administrador do sistema

Esta é uma mensagem de erro genérica - entre em contato com o suporte do Azure e ou envie um email para [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com) para que possamos investigar.

![Mensagem genérica do Azure RemoteApp](./media/remoteapp-apptrouble/ra-apptrouble4.png)

<!---HONumber=AcomDC_0817_2016-->