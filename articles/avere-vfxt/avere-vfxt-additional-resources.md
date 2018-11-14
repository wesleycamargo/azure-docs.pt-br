---
title: Links adicionais sobre o Avere vFXT para Azure
description: Links para informações adicionais sobre o Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 65e764d843f9e87adee4cf94c1d22b02db80eda0
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958825"
---
# <a name="additional-documentation"></a>Documentação adicional

Este artigo contém links para documentação adicional sobre a interface de gerenciamento do painel de controle do Avere, bem como tópicos relacionados. 

## <a name="avere-cluster-documentation"></a>Documentação sobre o cluster do Avere

Documentação adicional sobre o cluster do Avere pode ser encontrada no site em <http://library.averesystems.com/>. Esses documentos podem ajudar você a compreender os recursos do cluster e como definir suas configurações. 

* O [Guia de Criação de Cluster FXT](<http://library.averesystems.com/#fxt_cluster>) foi projetado para clusters compostos por nós de hardware físico, mas algumas informações no documento são relevantes também para clusters do vFXT. Em particular, novos administradores de cluster do vFXT podem se beneficiar da leitura destas seções:
  * [Personaliza configurações de monitoramento e suporte](<http://library.averesystems.com/create_cluster/4_8/html/config_support.html#config-support>) explica como personalizar configurações de upload de suporte e habilitar o monitoramento remoto. 
  * [Configurar VServers e o Namespace Global](<http://library.averesystems.com/create_cluster/4_8/html/config_vserver.html#config-vserver>) tem informações sobre como criar um namespace voltado ao cliente.
  * [Configurar o DNS para o cluster do Avere](<http://library.averesystems.com/create_cluster/4_8/html/config_network.html#dns-overview>) explica como configurar o DNS com round robin.
  * [Adicionar armazenamento de back-end](<http://library.averesystems.com/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) documenta como adicionar arquivistas principais.

* O [Guia de Configuração do Cluster](<http://library.averesystems.com/#operations>) é uma referência completa de configurações e opções para um cluster do Avere. Um cluster do vFXT usa um subconjunto dessas opções, mas a maioria das mesmas páginas de configuração se aplica.

* O [Guia do Painel](<http://library.averesystems.com/#operations>) explica como usar o monitoramento de recursos de cluster do Painel de Controle do Avere.

## <a name="vfxt-creation-and-management-documentation"></a>documentação sobre criação e gerenciamento de vFXT

Um guia completo sobre o uso do vfxt.py, o utilitário de criação e gerenciamento de cluster de nuvem, é fornecido no GitHub: [Gerenciamento de Cluster na Nuvem com vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).  
