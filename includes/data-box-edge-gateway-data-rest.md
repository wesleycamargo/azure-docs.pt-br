---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: d8570c0be6b64d4e289575ce3f3f1721c4a65074
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684395"
---
Para os dados em repouso:

- Para os dados em repouso, criptografia de BitLocker XTS AES-256 é usada para proteger os dados locais.
- Para os dados que residem nos compartilhamentos, o acesso aos compartilhamentos é restrito.

    - Para clientes SMB que acessam os dados de compartilhamento, eles precisam de credenciais de usuário associadas ao compartilhamento. Essas credenciais são definidas no momento da criação do compartilhamento.
    - Para clientes NFS que acessar os compartilhamentos, os endereços IP dos clientes precisarão ser adicionados no momento da criação do compartilhamento.