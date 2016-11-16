---
title: "Obtenha a mesma experiência do Office 365 em qualquer dispositivo com Azure RemoteApp | Microsoft Docs"
description: "Aprenda a compartilhar qualquer aplicativo de Office 365 com os usuários usando o RemoteApp do Azure."
services: remoteapp
documentationcenter: 
author: guscatalano
manager: mbaldwin
editor: 
ms.assetid: 0c971ce9-7d45-4cfb-9737-15b6706047e8
ms.service: remoteapp
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 08/15/2016
ms.author: guscatal;elizapo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0146ee4516ee54c3507567c25183e212db1ba7d2


---
# <a name="get-the-same-office-365-experience-on-any-device-with-azure-remoteapp"></a>Obter a mesma experiência de Office 365 em qualquer dispositivo com RemoteApp do Azure
> [!IMPORTANT]
> O RemoteApp do Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Este artigo abordará como implantar o Office 365 em qualquer dispositivo em sua empresa. Os usuários possam obter as mesmas capacidades e a experiência de interface de usuário em Android, Apple e Windows.

Faremos isso usando o RemoteApp do Azure hospedando o Office 365 em máquinas virtuais com capacidade de dimensionamento no Azure, às quais os usuários possam se conectar. Chamamos esse conjunto de máquinas virtuais de uma “coleção nuvem”.

## <a name="create-a-cloud-collection"></a>Criar uma coleção na nuvem
Pela primeira vez depois de criar uma conta do Azure, navegue até **RemoteApp** clicando no link no lado esquerdo.
![Mostrando o Azure RemoteApp no Portal do Azure](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

Depois, prossiga clicando em **novo** na parte inferior e fazendo a "criação rápida" de uma coleção. Forneça um nome, região, assinatura, plano e imagem "Office Professional 2013" que fornecemos.
![Caixa de diálogo Criar](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

Depois de você concluir o formulário, o processo de criação de coleção deve começar. Isso pode levar até uma hora ou mais.

![Aguardando](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

Depois que o processo for concluído, ele terá a aparência a seguir. Se clicarmos em **Publicação** , podemos ver que a maioria dos aplicativos do Office já foram publicados para nós.
![Coleção criada](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![Aplicativos publicados](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

Nesse ponto você também pode adicionar mais usuários que têm acesso a esta coleção, clicando em **Acesso de usuário**.
![Configurar o acesso do usuário](./media/remoteapp-tutorial-o365anywhere/6-user.png)

Agora vamos experimentar conectar ao Office 365!

## <a name="connect-to-office-365"></a>Conectar ao Office 365
Vamos acessar [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/), rolar para baixo e clicar em **Baixar clientes** para instalar o cliente do Azure RemoteApp no dispositivo no qual você está. As capturas de tela abaixo são para o Windows.

Depois que o aplicativo for iniciado, você será solicitado a entrar com a conta da Microsoft (chamada anteriormente de “Live ID”), insira a mesma usada na sua conta do Azure por enquanto. Quando você tiver feito logon, você deve receber uma notificação sobre novos convites, clique nos mesmos e você deve ver uma lista como a mostrada abaixo. Aceite o convite que corresponde ao seu e-mail de proprietário de conta do Azure.

![Novo convite](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

Abaixo, sua aparência quando há novos convites.

![Aceite um aplicativo](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

Depois de aceitar o convite, você deve ver todos os aplicativos do Office no cliente do RemoteApp do Azure.

![Lista de aplicativos](./media/remoteapp-tutorial-o365anywhere/9-work.png)

Quando você clica em qualquer um desses, o aplicativo deve ser iniciado na máquina virtual do Azure e você deve estar com tudo pronto! Aproveite!

![iniciando](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![powerpoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)




<!--HONumber=Nov16_HO2-->


