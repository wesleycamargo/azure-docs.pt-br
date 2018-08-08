---
title: Restrições de IP do Serviço de Aplicativo do Azure | Microsoft Docs
description: Como usar as restrições de IP do Serviço de Aplicativo do Azure
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 7/30/2018
ms.author: ccompy
ms.openlocfilehash: fb26d91ae772c4da1055da80366d6e8c6b80a6ac
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364301"
---
# <a name="azure-app-service-static-ip-restrictions"></a>Restrições de IP estático do Serviço de Aplicativo do Azure #

As restrições de IP permitem definir uma lista de prioridade ordenada de permissão/negação dos endereços IP que são permitidos a acessar o seu aplicativo. A lista de permissões pode incluir endereços IPv4 e IPv6. Quando há uma ou mais entradas, há, em seguida, uma negação implícita de tudo o que existe no final da lista. 

O recurso de restrições de IP funciona com todas as cargas de trabalho hospedadas no Serviço de Aplicativo, que incluem; aplicativos web, aplicativos api, aplicativos linux, aplicativos de contêiner linux e Funções. 

Quando uma solicitação é feita ao seu aplicativo, o endereço de IP é buscado na lista de restrições de IP. Se o endereço não é permitido o acesso com base nas regras na lista, o serviço responderá com um código de status [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

O recurso de restrições de IP é implementado de funções de front-end do Serviço de Aplicativo do Azure que são upstream dos hosts de trabalho em que seu código é executado. Restrições de IP, portanto, são efetivamente as ACLs de rede.  

![Fluxo de restrições de IP](media/app-service-ip-restrictions/ip-restrictions-flow.png)

Por um tempo, o recurso de Restrições IP no portal foi uma camada na parte superior do recurso ipSecurity em IIS. O recurso de restrições de IP atual é diferente. Você ainda pode configurar o ipSecurity dentro de seu aplicativo Web. config, mas serão aplicadas as regras de restrições de IP front-end com base em antes que qualquer tráfego alcance o IIS.

## <a name="adding-and-editing-ip-restriction-rules-in-the-portal"></a>Adicionar e editar regras de restrição de IP no portal ##

Para adicionar uma regra de restrição de IP ao aplicativo, use o menu para abrir **Rede**>**Restrições de IP** e clique em **Configurar Restrições de IP**

![Opções de rede do Serviço de Aplicativo do Azure](media/app-service-ip-restrictions/ip-restrictions.png)  

A partir da interface do usuário de IP, é possível analisar a lista de regras de restrição de IP definida para o aplicativo.

![lista de restrições de IP](media/app-service-ip-restrictions/ip-restrictions-browse.png)

Se as regras foram configuradas como nesta imagem, seu aplicativo só deve aceitar o tráfego do 131.107.159.0/24 e seria negado de qualquer outro endereço IP.

Clique em **[+] Adicionar** para adicionar uma nova regra de restrição de IP. Depois de adicionar uma regra, entrará em vigor imediatamente. As regras são impostas em ordem de prioridade a partir do número mais baixo e para cima. Há uma negação implícita tudo o que está em vigor depois que você adicionar até mesmo uma única regra. 

![adicionar uma regra de restrição de IP](media/app-service-ip-restrictions/ip-restrictions-add.png)

Notação de endereço IP deve ser especificada na notação de CIDR de endereços IPv4 e IPv6. Para especificar um endereço exato, você pode usar algo como 1.2.3.4/32 onde os quatro primeiros octetos representam o endereço IP e /32 é a máscara. A notação de CIDR de IPv4 para todos os endereços é 0.0.0.0/0. Para saber mais sobre a notação CIDR, leia [Roteamento entre domínios sem classe](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).  

Você pode clicar em qualquer linha para editar uma regra de restrição de IP existente. As edições são eficazes imediatamente, incluindo as alterações na ordem de prioridade.

![editar uma regra de restrição de IP](media/app-service-ip-restrictions/ip-restrictions-edit.png)

Para excluir uma regra, clique em **...**  em sua regra e clique em **remover**. 

![excluir uma regra de restrição de IP](media/app-service-ip-restrictions/ip-restrictions-delete.png)

## <a name="programmatic-manipulation-of-ip-restriction-rules"></a>Manipulação programática de regras de restrição de IP ##

Atualmente, não há nenhuma CLI ou o PowerShell para o novo recurso de restrições de IP, mas os valores podem ser definidos manualmente com uma operação PUT na configuração de aplicativo no Gerenciador de Recursos. Por exemplo, você pode usar resources.azure.com e editar o bloco ipSecurityRestrictions para adicionar o JSON necessário. 

O local para obter essas informações no Gerenciador de Recursos é:

management.azure.com/subscriptions/**subscription ID**/resourceGroups/**resource groups**/providers/Microsoft.Web/sites/**web app name**/config/web?api-version=2018-02-01

A sintaxe JSON para o exemplo anterior é:

    "ipSecurityRestrictions": [
      {
        "ipAddress": "131.107.159.0/24",
        "action": "Allow",
        "tag": "Default",
        "priority": 100,
        "name": "allowed access"
      }
    ],
