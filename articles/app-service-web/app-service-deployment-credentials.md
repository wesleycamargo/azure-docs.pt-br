---
title: "Credenciais de implantação do Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Saiba como usar as credenciais de implantação do Serviço de Aplicativo do Azure."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: wpickett
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/21/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 73753bb96d2c9680ed5d5bdf5eb3780e371c08d3
ms.openlocfilehash: 53f971624c2c7a7f64eed257aeb0c402461cc7ba


---
# <a name="azure-app-service-deployment-credentials"></a>Credenciais de implantação do Serviço de Aplicativo do Azure
A plataforma do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) é compatível com dois tipos de credencial para implantação de conteúdo.
* Credenciais de implantação: credenciais no escopo do usuário
* Perfil de publicação: credenciais no escopo do aplicativo 

## <a name="a-nameuserscopeauser-scoped-credentials"></a><a name="userscope"></a>Credenciais no escopo do usuário
As credenciais no escopo do usuário são criadas pelo usuário do Azure e mapeiam diretamente para uma conta da Microsoft, e não para qualquer aplicativo específico do Serviço de Aplicativo. As credenciais de implantação no escopo do usuário podem ser definidas ou redefinidas no [Portal do Azure](https://portal.azure.com), onde cada aplicativo do Serviço de Aplicativo tem um ponto de entrada de edição em **IMPLANTAÇÃO DE APLICATIVO > Credenciais de implantação**. Independentemente do ponto de entrada, as edições para essas credenciais no escopo do usuário se aplicam em toda a conta Microsoft. Essas credenciais são usadas frequentemente para implantação de FTP e Git.

![Credenciais de implantação do Serviço de Aplicativo do Azure](./media/app-service-deployment-credentials/deployment_credentials.png)
 
Ao delegar acesso a recursos do Azure por meio de RBAC (Controle de Acesso Baseado em Função) ou permissões de coadministrador, cada usuário do Azure que está recebendo acesso pode usar suas próprias credenciais no escopo do usuário até que o acesso seja revogado. Essas credenciais de implantação não devem ser compartilhadas com outros usuários do Azure.

## <a name="a-nameappscopeaapp-scoped-credentials"></a><a name="appscope"></a>Credenciais no escopo do aplicativo
As credenciais no escopo do aplicativo são criadas automaticamente pela plataforma do Serviço de Aplicativo. Elas são armazenadas no perfil de publicação XML para cada aplicativo do Serviço de Aplicativo. O perfil de publicação está disponível no [Portal do Azure](https://portal.azure.com) por meio da ação **Obter perfil de publicação** na folha **Visão geral** do aplicativo. Essas credenciais são usadas frequentemente para implantação baseada em WebDeploy. Elas também podem ser usadas para implantação de FTP ou Git. O Visual Studio, que é um ponto de entrada para implantação baseada em WebDeploy é capaz de analisar o perfil de publicação para autenticação.

![Perfil de publicação do Serviço de Aplicativo do Azure](./media/app-service-deployment-credentials/publish_profile.png)

Ao delegar acesso a recursos do Azure por meio de RBAC (Controle de Acesso Baseado em Função) ou permissões de coadministrador, cada usuário do Azure que está recebendo acesso pode baixar o mesmo perfil de publicação específico do aplicativo. O perfil de publicação pode ser redefinido a qualquer momento na folha **Visão geral** do aplicativo no Portal do Azure. A redefinição das credenciais no escopo do aplicativo pode ser uma boa ideia após revogar o acesso delegado.



<!--HONumber=Nov16_HO4-->


