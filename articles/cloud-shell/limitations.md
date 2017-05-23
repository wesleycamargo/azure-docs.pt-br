---
title: "Limitações do Azure Cloud Shell (Visualização) | Microsoft Docs"
description: "Visão geral das limitações do Azure Cloud Shell"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: c007b73375c8c82248228f4e549c0ac95640d7ec
ms.contentlocale: pt-br
ms.lasthandoff: 05/17/2017

---

# <a name="limitations-for-azure-cloud-shell"></a>Limitações do Azure Cloud Shell
O Azure Cloud Shell tem as seguintes limitações conhecidas.

## <a name="system-state-and-persistence"></a>Estado do sistema e persistência
O computador que está fornecendo a sessão do Cloud Shell é temporário e é reciclado após a sessão ficar inativa por 10 minutos. O Cloud Shell exige a montagem de um compartilhamento de arquivos.
* Com o armazenamento montado, apenas as modificações em seu diretório `$Home` ou `clouddrive` são persistidas
  * Os compartilhamentos de arquivo só podem ser montados em sua [região atribuída](persisting-shell-storage.md#pre-requisites-for-manual-mounting)
  * Os Arquivos do Azure oferecem suporte apenas a contas de armazenamento LRS e GRS

## <a name="user-permissions"></a>Permissões de usuário
As permissões são definidas como usuários regulares sem acesso sudo. Qualquer instalação fora do seu $Home não serão persistidas.
Alguns comandos como `git clone` dentro do diretório `clouddrive` não têm permissões adequadas, no entanto, seu diretório $Home tem.

## <a name="browser-support"></a>Suporte ao navegador
O Cloud Shell oferece suporte às versões mais recentes do Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox e Apple Safari. Não há suporte para o Safari no modo particular.

## <a name="copy-and-paste"></a>Copiar e colar
Ctrl+V e Ctrl+C não funcionam como copiar/colar em máquinas com Windows, use Ctrl+Insert e Shift+Insert para copiar/colar.
Opções de copiar e colar com o botão direito também estão disponíveis, no entanto, isso está sujeito ao acesso específico de área de transferência de um navegador.

## <a name="usage-limits"></a>Limites de uso
O Cloud Shell é destinado a casos de uso interativos, como resultado quaisquer sessões não interativo e de longa execução são finalizadas sem aviso.

## <a name="network-connectivity"></a>Conectividade de rede
Qualquer latência no Cloud Shell está sujeita à conectividade de internet local, o Cloud Shell continuará tentando funcionar com as instruções enviadas.

## <a name="next-steps"></a>Próximas etapas
[Início rápido do Cloud Shell](quickstart.md)
