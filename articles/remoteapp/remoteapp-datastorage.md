
---
title: Nunca armazene dados confidenciais em imagens personalizadas para o Azure RemoteApp | Microsoft Docs
description: Saiba mais sobre as diretrizes para armazenar dados em imagens personalizadas no Azure RemoteApp
services: remoteapp
documentationcenter: ''
author: lizap
manager: mbaldwin

ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo

---
# Nunca armazene dados confidenciais em imagens personalizadas
> [!IMPORTANT]
> O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Ao hospedar seu aplicativo no Azure RemoteApp, a primeira etapa é criar uma imagem personalizada. Usamos essa imagem personalizada para criar instâncias de VM que fornecem seus aplicativos para seus usuários. A imagem personalizada deve conter APENAS aplicativos e nunca dados confidenciais que podem ser perdidos, como Bancos de Dados SQL, arquivos de pessoal ou arquivos de dados especiais, como arquivos da empresa do QuickBooks. Todos os dados confidenciais devem residir externamente ao Azure RemoteApp em um servidor de arquivos, outra VM do Azure ou no SQL Azure. A imagem deve hospedar apenas o aplicativo que se conecta à fonte de dados e apresenta os dados. Releia os [Requisitos para as imagens do Azure RemoteApp](remoteapp-imagereqs.md) para obter mais informações.

Para entender por que você não deve armazenar dados confidenciais, você precisa entender como o Azure RemoteApp funciona. Quando uma coleção é criada ou atualizada, nos bastidores, são criados vários clones ou cópias da imagem. Todas essas instâncias de VM são réplicas exatas da imagem personalizada. Quando os usuários iniciam os aplicativos eles são conectados a uma dessas instâncias de VM. Mas a mesma instância não é garantida e não deve importar porque não é persistente. As instâncias de VM hospedando os aplicativos são não persistentes e podem ser destruídas ou excluídas, por exemplo, durante a atualização da coleção.

Depois que a coleção é provisionada e os usuários começam a se conectar às VMs, os dados de usuário são persistentes e protegidos, pois eles são salvos no armazenamento separado dentro de um VHD que chamamos de um [UPD (disco de perfil de usuário)](remoteapp-upd.md), que é o perfil do usuário em c:\\users<userprofile>. Quando um aplicativo é iniciado, o UPD é montado e tratado como um perfil do usuário local pelo sistema operacional. Leia mais sobre como o [Azure RemoteApp salva configurações e dados de usuário](remoteapp-upd.md).

Dados de exemplo que não devem residir na imagem:

* Dados compartilhados para os usuários acessarem
* Banco de dados SQL ou banco de dados do QuickBooks
* Quaisquer dados em D:\\

Dados de exemplo que podem residir no perfil padrão a ser copiado para o UPD de todos os usuários:

* Arquivos de configuração por usuário
* Scripts que os usuários precisariam que fossem preservados no seu UPD

Pontos principais:

* Nunca armazene dados confidenciais que possam ser perdidos na imagem ao criar uma imagem personalizada.
* Dados confidenciais sempre devem residir em um servidor de arquivos separado, VM do Azure separada, na nuvem e sempre externos às instâncias de VM que hospedam seus aplicativos no Azure RemoteApp.
* Os dados de usuário são salvos e persistem no UPD (disco de perfil do usuário)

<!---HONumber=AcomDC_0817_2016-->