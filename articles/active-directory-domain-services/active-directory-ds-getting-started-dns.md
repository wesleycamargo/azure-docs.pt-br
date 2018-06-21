---
title: 'Azure Active Directory Domain Services: Atualizar as configurações do DNS para a rede virtual do Azure | Microsoft Docs'
description: Introdução aos Serviços de Domínio do Active Directory do Azure
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/30/2018
ms.author: maheshu
ms.openlocfilehash: d4ff41e51622adb7776df5e053025911bfa3ee16
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215402"
---
# <a name="enable-azure-active-directory-domain-services"></a>Habilitar o Azure Active Directory Domain Services

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Tarefa 4: atualizar as configurações do DNS para a rede virtual do Azure
Nas tarefas de configuração anteriores, você habilitou o Azure Active Directory Domain Services para seu diretório com êxito. Em seguida, permita que os computadores na rede virtual possam se conectar e consumir esses serviços. Neste artigo, você atualiza as configurações do servidor DNS para sua rede virtual para apontar para os dois endereços IP em que o Azure Active Directory Domain Services fica disponível na rede virtual.

Para atualizar as configurações do servidor DNS para a rede virtual na qual você tiver habilitado o Azure Active Directory Domain Services, conclua as seguintes etapas:


1. A guia **Visão Geral** lista um conjunto de **etapas de configuração necessárias** que deverão ser executadas depois que o domínio gerenciado for totalmente provisionado. A primeira etapa de configuração é **Atualizar configurações do servidor DNS para sua rede virtual**.

    ![Serviços de Domínio - guia Visão Geral](./media/getting-started/domain-services-provisioned-overview.png)

    > [!TIP]
    > Não está vendo essa etapa da configuração? Se as configurações do servidor DNS de sua rede virtual estiverem atualizadas, você não verá o bloco "Atualizar configurações do servidor DNS de sua rede virtual" na guia.
    >
    >

2. Clique no botão **Configurar** para atualizar as configurações do servidor DNS da rede virtual.

> [!NOTE]
> As máquinas virtuais na rede só recebem as novas configurações de DNS após uma reinicialização. Se você precisar deles para obter as configurações de DNS atualizadas imediatamente, dispare uma reinicialização do portal, do PowerShell ou da CLI.
>
>

## <a name="next-step"></a>Próxima etapa
[Tarefa 5: Habilitar a sincronização de hashes de senha para o Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)
