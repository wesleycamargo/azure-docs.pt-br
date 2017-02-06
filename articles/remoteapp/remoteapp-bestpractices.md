---
title: "Práticas recomendadas do RemoteApp do Azure | Microsoft Docs"
description: "Práticas recomendadas para configurar e usar o RemoteApp do Azure"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: b851865b-bec4-4f29-82c0-7b9770c1a520
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 9af9f95e9638eda71fbc4508bea8e35ce5191734
ms.openlocfilehash: 3caabe366db4def4f8332c64d08f86f271c31bb2


---
# <a name="best-practices-for-configuring-and-using-azure-remoteapp"></a>Práticas recomendadas para configurar e usar o RemoteApp do Azure
> [!IMPORTANT]
> O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://blogs.technet.microsoft.com/enterprisemobility/2016/08/12/application-remoting-and-the-cloud/) para obter detalhes.
> 
> 

As informações a seguir podem ajudá-lo a configurar e usar o RemoteApp do Azure de forma produtiva.

## <a name="connectivity"></a>Conectividade
* Sempre use a versão mais recente do cliente. Usar clientes mais antigos pode resultar em problemas de conectividade e outras experiências degradadas. A habilitação de atualizações automáticas do aplicativo para o dispositivo irá garantir que o cliente mais recente esteja sempre instalado.
* Sempre use a conexão de internet mais estável e confiável disponível para você.  
* Use apenas conexões de proxy suportadas para desempenho ideal de conectividade.  Não há suporte para o proxy SOCKS.

## <a name="applications"></a>Aplicativos
* Salve e feche os aplicativos do RemoteApp ao terminar com o aplicativo. O não fechamento do aplicativo pode resultar em perda de dados.
* Valide aplicativos personalizados antes de usá-los no RemoteApp do Azure. Isso inclui garantir que eles funcionam em uma plataforma de várias sessões e não consomem recursos desnecessários, como memória e CPU que podem enfraquecer outro usuário na mesma coleção. Para obter informações, baixe e analise as [Práticas recomendadas de compatibilidade de aplicativos para os serviços de área de trabalho remota](http://www.dabcc.com/resources/Application%20Compatibility%20Best%20Practices%20for%20Remote%20Desktop%20Services.pdf).

## <a name="configuration-and-management"></a>Configuração e gerenciamento
* Mantenha suas imagens de modelo atualizadas, instale atualizações de software e outras correções críticas, conforme necessário. Isso garante que, como o RemoteApp do Azure se redimensiona automaticamente para atender sua capacidade, cada instância é corrigida.  
* Verifique se a sua implantação de serviços de Federação do Active Directory (AD FS) está segura e confiável. Caso contrário, as autenticações do cliente podem falhar, impedindo que os usuários acessem o RemoteApp do Azure.
* Configure imagens de modelo com aplicativos instalados, funções ou recursos que estão sem monitoramento do estado. Elas não devem confiar em todas as instâncias das máquinas virtuais em um serviço do RemoteApp estando em um estado persistente.
  * Armazene todos os dados do usuário em perfis de usuário ou outros locais de armazenamento externos para o serviço, como compartilhamentos de arquivo local ou OneDrive.
  * Armazene dados compartilhados em locais de armazenamento externos para o serviço, como compartilhamentos de arquivo local ou OneDrive.
  * Configure quaisquer configurações gerais do sistema na imagem do modelo em vez de em máquinas virtuais individuais em um serviço.
  * Desabilite atualizações automáticas de software para aplicativos publicados - em vez disso, aplique-as manualmente à imagem do modelo e teste-as antes da implantação do modelo.




<!--HONumber=Jan17_HO2-->


