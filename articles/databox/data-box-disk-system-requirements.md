---
title: Requisitos de sistema de Disco do Microsoft Azure Data Box | Microsoft Docs
description: Aprender sobre os requisitos de software e de rede para seu Disco do Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 09/06/2018
ms.author: alkohli
ms.openlocfilehash: d10ca5d704892ae0a1494d729b46abf0fc06aa64
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092245"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Requisitos do sistema do Disco do Azure Data Box (versão prévia)

Este artigo descreve os requisitos de sistema importantes para a solução de Disco do Microsoft Azure Data Box e para os clientes se conectando ao Disco do Data Box. Recomendamos que você examine as informações com atenção antes de implantar o Disco do Data Box e consulte-as, quando necessário, durante a implantação e a subsequente operação.

> [!IMPORTANT]
> O Disco do Data Box está em versão prévia. Examine os [termos de uso para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de implantar essa solução. 

Os requisitos de sistema incluem as plataformas compatíveis com os clientes que se conectam a discos, contas de armazenamento compatíveis e tipos de armazenamento.


## <a name="supported-operating-systems-for-clients"></a>Sistemas operacionais compatíveis para clientes

Aqui está uma lista dos sistemas operacionais compatíveis para desbloqueio de disco e a operação de cópia de dados por meio de clientes conectados ao Disco do Data Box.

| **Sistema operacional** | **Versões testadas** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Outros softwares necessários para clientes do Windows

Para o cliente do Windows, o seguinte também deve ser instalado.

| **Software**| **Versão** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Outros softwares necessários para clientes do Linux

Para o cliente do Linux, o conjunto de ferramentas do Disco do Data Box instala o software necessário a seguir:

- dislocker
- OpenSSL

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

