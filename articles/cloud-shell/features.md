---
title: "Recursos do Azure Cloud Shell (visualização) | Microsoft Docs"
description: "Visão geral dos recursos do Azure Cloud Shell"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 3414957a67d67603fdb597b3715a902e8cc3c5bd
ms.contentlocale: pt-br
ms.lasthandoff: 07/18/2017

---

# <a name="features-and-tools-for-azure-cloud-shell"></a>Recursos e ferramentas para o Azure Cloud Shell
Azure Cloud Shell é uma experiência de shell baseada em navegador para gerenciar e desenvolver recursos do Azure.

O Cloud Shell oferece uma experiência de shell acessível ao navegador e pré-configurada para gerenciar recursos do Azure sem a sobrecarga da instalação, controle de versão e manutenção de uma máquina por conta própria.

O Cloud Shell provisiona máquinas com base em solicitação e, consequentemente, o estado da máquina não persistirá nas sessões. Como o Cloud Shell é compilado para sessões interativas, os shells são encerrados automaticamente após 20 minutos de inatividade do shell.

## <a name="bash-in-cloud-shell"></a>Bash no Cloud Shell
### <a name="tools"></a>Ferramentas
|Categoria   |Nome   |
|---|---|
|Intérprete de shell do Linux|Bash<br> sh               |
|Ferramentas do Azure            |CLI do Azure [1.0](https://github.com/Azure/azure-cli) e [2.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Shipyard de lote](https://github.com/Azure/batch-shipyard)     |
|Editores de texto           |vim<br> nano<br> emacs       |
|Controle do código-fonte         |git                    |
|Ferramentas de build            |make<br> maven<br> npm<br> pip         |
|Contêineres             |[CLI do Docker](https://github.com/docker/cli)/[Computador do Docker](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Escalação](https://github.com/Azure/draft)<br> [CLI DO DC/OS](https://github.com/dcos/dcos-cli)         |
|Bancos de dados              |Cliente do MySQL<br> Cliente do PostgreSql<br> [Utilitário SQLCMD](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|Outros                  |Cliente do iPython<br> [CLI do Cloud Foundry](https://github.com/cloudfoundry/cli)<br> |

### <a name="language-support"></a>Suporte ao idioma
|idioma   |Versão   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|Python     |2.7 e 3.5 (padrão)|

## <a name="secure-automatic-authentication"></a>Autenticação automática segura
O Cloud Shell protege e autentica automaticamente o acesso de conta da CLI 2.0 do Azure.

## <a name="azure-files-persistence"></a>Persistência de arquivos do Azure
Como o Cloud Shell é alocado com base em solicitação usando um computador temporário, os arquivos fora de $Home e o estado do computador não persistem entre as sessões.
Para persistir arquivos entre as sessões, o Cloud Shell orienta você pela anexação de um compartilhamento de arquivos do Azure na primeira inicialização.
Após a conclusão, o Cloud Shell anexará automaticamente o armazenamento para todas as sessões futuras.

[Saiba mais sobre como anexar os compartilhamentos de arquivos do Azure ao Cloud Shell.](persisting-shell-storage.md)

## <a name="next-steps"></a>Próximas etapas
[Início rápido do Cloud Shell](quickstart.md) <br>
[Saiba mais sobre a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/) <br>
