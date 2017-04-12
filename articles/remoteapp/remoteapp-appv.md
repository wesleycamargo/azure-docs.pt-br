---
title: Usando aplicativos do App-V com o Azure RemoteApp| Microsoft Docs
description: Saiba como usar os aplicativos App-V no Azure RemoteApp.
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: e2292cb2-5c89-4b2b-ab11-74dbacd07c31
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 0e82c639ea7bfceb89f0cf9c59ab657a951f2d5f
ms.lasthandoff: 03/31/2017


---
# <a name="using-app-v-apps-in-azure-remoteapp"></a>Usando os aplicativos App-V no Azure RemoteApp
> [!IMPORTANT]
> O Azure RemoteApp será descontinuado até 31 de agosto de 2017. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Você pode usar os aplicativos do App-V em uma coleção híbrida do Azure RemoteApp, o que requer o ingresso no domínio.

Antes de começar, instale o cliente do App-V 5.1 com as atualizações mais recentes. Você precisará criar uma [imagem personalizada](remoteapp-create-custom-image.md) que inclua o cliente de App-V.  

É fácil usar sua infraestrutura existente do App-V com o Azure RemoteApp. Como uma coleção híbrida é implantada em uma Rede Virtual do Azure com acesso ao controlador de domínio e as VMs são ingressadas no domínio, você poderá aproveitar a infraestrutura existente do App-V e os métodos de implantação para hospedar com facilidade o aplicativo App-V no Azure RemoteApp. Veja algumas considerações que você deve fazer com base no tipo de implantação do App-V que possui no momento:

| Opções de configuração |  | Positivo | Negativo |
| --- | --- | --- | --- |
| Método de entrega |Streaming (por demanda) |O aplicativo é sempre a versão mais recente e está sempre atualizado |Latência da primeira vez |
| Montado |Mais rápido; o aplicativo já está presente na VM |Inchaço - ocupa o espaço da imagem (limite de 127 GB) | |
| Armazenamento de local de aplicativo |Conteúdo compartilhado |O aplicativo é executado na memória da instância do Azure RemoteApp |Consome memória e a conexão com o servidor de streaming (arquivo) em que o aplicativo reside |
| Disco (em cache) |Execução rápida. O aplicativo não depende de disponibilidade da Fonte de Conteúdo |Inchaço - ocupa o espaço da imagem (limite de 127 GB) | |
| Direcionamento |Usuário |Exige a infraestrutura completa do App-V autônomo | |
| Global (máquina) |Pré-publicar ou destinar usando o servidor de Publicação |Você precisará atualizar a imagem do Azure se quiser atualizar o aplicativo (enorme). Ocupa algum espaço na imagem. | |

 Depois de criar sua imagem personalizada e sua coleção híbrida, publique seu aplicativo, atribua usuários e aproveite seus aplicativos existentes do App-V hospedados no Azure RemoteApp entregues a qualquer dispositivo em qualquer lugar.


