---
title: "Solução de problemas de implantação do OpenShift no Azure | Microsoft Docs"
description: "Solução de problemas de implantação do OpenShift no Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 35e554d3a9c7e7d56546ae9723c33eb59e906472
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2017
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Solução de problemas de implantação do OpenShift no Azure

Se o cluster do OpenShift não for implantado com êxito, teste estas tarefas de solução de problemas que podem ser executadas para solucionar o problema. Exiba o status da implantação e compare com a seguinte lista de Códigos de Saída:

- Código de Saída 3: Nome/senha de usuário de assinatura do Red Hat ou ID/chave de ativação estão incorretos
- Código de Saída 4: a ID do Pool do Red Hat está incorreta ou não há direitos disponíveis
- Código de Saída 5: não foi possível provisionar o Volume do pool dinâmico do Docker
- Código de Saída 6: falha na instalação do cluster do OpenShift
- Código de Saída 7: instalação do cluster do OpenShift bem-sucedida, mas a configuração do provedor de nuvem do Azure falhou – problema na configuração mestre do Nó mestre
- Código de Saída 8: instalação do cluster do OpenShift bem-sucedida, mas a configuração do provedor de nuvem do Azure falhou – problema na configuração do Nó mestre
- Código de Saída 9: instalação do cluster do OpenShift bem-sucedida, mas a configuração do provedor de nuvem do Azure falhou – problema na configuração no Infra ou Aplicativo do Nó mestre
- Código de Saída 10: instalação do cluster do OpenShift bem-sucedida, mas a configuração do provedor de nuvem do Azure falhou – corrigindo Nós mestres ou não é possível definir o Mestre como não agendável
- Código de Saída 11: falha na implantação de métricas
- Código de Saída 12: falha na implantação de registro em log

Para Códigos de Saída 7-10, o cluster do OpenShift foi instalado, mas a configuração do provedor de nuvem do Azure falhou. Você pode realizar o SSH para o Nó mestre (Origem) ou o Nó de bastião (Plataforma de contêiner) e, de lá, realizar o SSH para cada um dos nós no cluster e corrigir os problemas.

Uma causa comum para as falhas com Códigos de Saída 7-9 é que a Entidade de Serviço não tem as permissões adequadas para a Assinatura ou o Grupo de Recursos. Se esse for realmente o problema, atribua as permissões corretas e execute manualmente de novo o script que falhou e todos os scripts subsequentes.

Certifique-se de reiniciar o serviço que falhou (por exemplo, reinicialização de systemctl atomic-openshift-node.service) antes de executar os scripts novamente.

Para solução de mais problemas, realize o SSH em seu Nó Mestre na porta 2200 (Origem) ou Nó de Bastião na porta 22 (Contêiner de plataforma). Você precisa estar na raiz (sudo su-) e, em seguida, navegar até o seguinte diretório: /var/lib/waagent/custom-script/download.

Aqui você vê pastas denominadas "0" e "1." Em cada uma dessas pastas, você verá dois arquivos, “stdout” e “stderr." Examine esses arquivos para determinar onde a falha ocorreu.
