---
title: "Azure Active Directory Domain Services: Atualizar as configurações do DNS para a rede virtual do Azure | Microsoft Docs"
description: "Introdução aos Serviços de Domínio do Active Directory do Azure"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: abb27292d4b5533fe6f3d66d6921fea8c82f18dd
ms.lasthandoff: 04/12/2017


---
# <a name="update-dns-settings-for-the-azure-virtual-network"></a>Atualizar as configurações do DNS para a rede virtual do Azure
## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Tarefa 4: atualizar as configurações do DNS para a rede virtual do Azure
Nas tarefas de configuração anteriores, você habilitou o Azure Active Directory Domain Services para seu diretório com êxito. A próxima tarefa é garantir que os computadores na rede virtual possam se conectar e consumir esses serviços. Neste artigo, você atualiza as configurações do servidor DNS para sua rede virtual para apontar para os dois endereços IP em que o Azure Active Directory Domain Services fica disponível na rede virtual.

> [!NOTE]
> Depois de habilitar o Azure Active Directory Domain Services para o diretório, observe os endereços IP para o Azure Active Directory Domain Services que são exibidos na guia **Configurar** de seu diretório.
>
>

Para atualizar uma configuração do servidor DNS para a rede virtual na qual você tiver habilitado o Azure Active Directory Domain Services, faça o seguinte:

1. Vá para o [portal clássico do Azure](https://manage.windowsazure.com).
2. No painel esquerdo, selecione **Redes**.  
    A janela **redes** é aberta.

    ![Janela Redes virtuais](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. Na guia **Redes Virtuais** , selecione a rede virtual na qual você habilitou o Azure Active Directory Domain Services para exibir suas propriedades.
4. Clique na guia **Configurar** .

    ![Janela Redes virtuais](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. Na seção **Servidores DNS**, insira os endereços IP que foram exibidos na seção **Serviços de Domínio** da guia **Configurar** do seu diretório.
6. Para salvar as configurações do servidor DNS para essa rede virtual, clique em **Salvar** no painel de tarefas na parte inferior da janela.

   ![Atualizar as configurações do servidor DNS para a rede virtual](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
> Depois de atualizar as configurações do servidor DNS para a rede virtual, pode levar algum tempo para as máquinas virtuais na rede obterem a configuração de DNS atualizada. Se uma máquina virtual não puder se conectar ao domínio, você poderá liberar o cache DNS ('ipconfig /flushdns') na máquina virtual. Esse comando força uma atualização das configurações de DNS na máquina virtual.
>
>

## <a name="next-steps"></a>Próximas etapas
Tarefa 5: [habilitar a sincronização de senhas para o Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)

