---
title: "Recursos do Azure Cloud Shell (visualização) | Microsoft Docs"
description: "Visão geral dos recursos do Azure Cloud Shell"
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
ms.openlocfilehash: e789283ab9b25f634c50b341ca882bbf9c70a225
ms.contentlocale: pt-br
ms.lasthandoff: 05/17/2017

---

# <a name="features--tools-for-azure-cloud-shell"></a>Recursos e ferramentas para o Azure Cloud Shell
Azure Cloud Shell é uma experiência de shell baseada em navegador para gerenciar e desenvolver recursos do Azure.

O Cloud Shell oferece uma experiência de shell acessível ao navegador e pré-configurada para gerenciar recursos do Azure sem a sobrecarga da instalação, controle de versão e manutenção de uma máquina por conta própria.

O Cloud Shell provisiona máquinas com base em solicitação e, consequentemente, o estado da máquina não persistirá nas sessões. Como o Cloud Shell é compilado para sessões interativas, os shells finalizam automaticamente após 10 minutos de inatividade do shell.

## <a name="tools"></a>Ferramentas
|Categoria   |Nome   |
|---|---|
|Intérprete de shell do Linux|Bash<br> sh               |
|Ferramentas do Azure            |CLI 1.0 e 2.0 do Azure     |
|Editores de texto           |vim<br> nano<br> emacs       |
|Controle do código-fonte         |git                    |
|Ferramentas de build            |make<br> maven<br> npm<br> pip         |
|Contêineres             |Docker<br> Kubectl<br> CLI do DC/OS         |
|Bancos de dados              |Cliente do MySQL<br> Cliente do PostgreSql<br> Utilitário sqlcmd      |
|Outros                  |Cliente do iPython |

## <a name="language-support"></a>Suporte ao idioma
|idioma   |Versão   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Node.js    |6.9.4      |
|Python     |2.7 e 3.5|

## <a name="secure-automatic-authentication"></a>Autenticação automática segura
O Cloud Shell protege e autentica automaticamente o acesso de conta da CLI 2.0 do Azure.

## <a name="azure-files-persistence"></a>Persistência de arquivos do Azure
Como o Cloud Shell é alocado com base em solicitação usando um computador temporário, os arquivos locais fora de $Home e o estado da máquina não persistem entre as sessões.
Para persistir arquivos entre as sessões, o Cloud Shell orienta você pela anexação de um compartilhamento de arquivos do Azure na primeira inicialização.
Após a conclusão, o Cloud Shell anexará automaticamente o armazenamento para todas as sessões futuras.

[Saiba mais sobre como anexar os compartilhamentos de arquivos do Azure ao Cloud Shell. ](persisting-shell-storage.md).

## <a name="next-steps"></a>Próximas etapas
[Início rápido do Cloud Shell](quickstart.md) 
[Saiba mais sobre a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/)
