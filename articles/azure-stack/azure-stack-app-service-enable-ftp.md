---
title: "Habilitar o FTP no Serviço de Aplicativo no Azure Stack | Microsoft Docs"
description: "Etapas a serem concluídas para habilitar o FTP no Serviço de Aplicativo no Azure Stack"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: erikje
ms.openlocfilehash: 9cadc57831ac7f7e5d32b10a4a87dab3fac02958
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-ftp-in-app-service-on-azure-stack"></a>Habilitar o FTP no Serviço de Aplicativo no Azure Stack

Depois de implantar com êxito o Serviço de Aplicativo no Azure Stack, se você quiser habilitar a publicação do FTP para que seus locatários possam carregar os respectivos arquivos e conteúdo de aplicativo, há algumas etapas adicionais que precisam ser concluídas.  Em futuras versões, essas etapas serão automatizadas.

> [!NOTE]
> Essas etapas são para Administradores de Serviços ou Corporativos que configuram um Serviço de Aplicativo no Provedor de Recursos do Azure Stack.

## <a name="enable-ftp"></a>Habilitar o FTP

1.  Faça logon no Portal do Azure Stack como administrador de serviços.
2.  Navegue até **Interfaces de rede** e selecione **FTP-NIC** em **Grupo de Recursos** - **AppService-LOCAL**. ![Interfaces de rede do Azure Stack][1]
3.  Anote o **Endereço IP Público** de **FTP-NIC**. 
![Detalhes da interface de rede do Azure Stack][2]
4.  Em seguida, navegue até **Máquinas Virtuais** e selecione **FTP0-VM**. ![Máquinas Virtuais do Azure Stack][3]
5.  Abra uma sessão de área de trabalho remota para a VM usando o botão **Conectar** e faça logon na sessão usando as credenciais de Administrador definidas durante a implantação do Serviço de Aplicativo.  
![Detalhes da Máquina Virtual do Azure Stack][4]
6.  Abra **Gerenciador do IIS (Serviço de Informações da Internet)** na VM do FTP (FTP0-VM).
7.  Em **Sites**, selecione **Hospedando Site FTP**.
8.  Abra **Suporte ao Firewall FTP**. ![Gerenciador do IIS no FTP0-VM do Serviço de Aplicativo][5]
9.  Insira o Endereço IP Público de FTP-NIC e clique em **Aplicar** ![Suporte ao Firewall FTP do Gerenciador do IIS][6]

## <a name="validate-the-enabling-of-ftp"></a>Validar a habilitação do FTP

1.  Faça logon no Portal do Azure Stack como o administrador de serviços ou um locatário.
2.  Navegue até **Serviços de Aplicativos** e selecione um Aplicativo de API, Web ou Móvel que você criou. ![Serviços de Aplicativos][7]
3.  Nos detalhes do aplicativo, anote o **Nome de host do FTP** e o **Nome de usuário de FTP/implantação**. ![Detalhes do aplicativo no Serviço de Aplicativo][8]
> [!NOTE]
> Caso não veja uma entrada em **Nome de usuário de FTP/implantação**, você precisará definir as Credenciais de implantação primeiro usando a folha **Credenciais de Implantação**.

4.  Abra o Windows Explorer, insira o nome de host do FTP na barra de endereço do arquivo, por exemplo, ftp://ftp.appservice.azurestack.local
5.  Quando solicitado, insira as **Credenciais de implantação** que você anotou na etapa 3. Se o recurso tiver sido habilitado, você verá uma listagem de diretório do conteúdo do aplicativo do Serviço de Aplicativo. ![Listagem de arquivos do FTP][9]
<!--Image references-->
[1]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interfaces.png
[2]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interface-details.png
[3]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines.png
[4]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines-FTP0-VM.png
[5]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager.png
[6]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager-FTP-Firewall-Support.png
[7]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-services.png
[8]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-service-app-detail.png
[9]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-ftp-file-listing.png
