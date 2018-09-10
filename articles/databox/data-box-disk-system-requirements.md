---
title: Requisitos de sistema de Disco do Microsoft Azure Data Box | Microsoft Docs
description: Aprender sobre os requisitos de software e de rede para seu Disco do Azure Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/28/2018
ms.author: alkohli
ms.openlocfilehash: 2fdd574adf3587f11984bee2a2549d9bcd0c4c0d
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125998"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Requisitos do sistema do Disco do Azure Data Box (versão prévia)

Este artigo descreve os requisitos de sistema importantes para a solução de Disco do Microsoft Azure Data Box e para os clientes se conectando ao Disco do Data Box. Recomendamos que você examine as informações com atenção antes de implantar o Disco do Data Box e consulte-as, quando necessário, durante a implantação e a subsequente operação.

> [!IMPORTANT]
> O Disco do Data Box está em versão prévia. Examine os [termos de uso para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de implantar essa solução. 

Os requisitos de sistema incluem as plataformas compatíveis com os clientes que se conectam a discos, contas de armazenamento compatíveis e tipos de armazenamento.


## <a name="supported-operating-systems-for-clients"></a>Sistemas operacionais compatíveis para clientes

Aqui está uma lista dos sistemas operacionais compatíveis para desbloqueio de disco e a operação de cópia de dados por meio de clientes conectados ao Disco do Data Box.

| **Sistema operacional/plataforma** | **Versões** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
| Windows PowerShell |4,0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |4,0|

> [!NOTE] 
> O BitLocker precisa ser habilitado nos clientes que executam a ferramenta de desbloqueio de disco e são usados para copiar os dados.


## <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

Aqui está uma lista dos tipos de armazenamento compatíveis com o Disco do Data Box.

| **Conta de armazenamento** | **Observações** |
| --- | --- |
| Clássico | Standard |
| Uso geral  |Standard; tanto V1 quanto V2 são compatíveis. Tanto a camada de acesso frequente quanto a camada de acesso esporádico são compatíveis. |


## <a name="supported-storage-types"></a>Tipos de armazenamento com suporte

Aqui está uma lista dos tipos de armazenamento compatíveis com o Disco do Data Box.

| **Formato de arquivo** | **Observações** |
| --- | --- |
| Blob de blocos do Azure | |
| Blob de páginas do Azure  | |


## <a name="next-step"></a>Próxima etapa

* [Implante seu Disco do Azure Data Box](data-box-disk-deploy-ordered.md)

