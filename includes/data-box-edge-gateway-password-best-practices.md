---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: a3b6616edbe1678cb18f78ec9025fc0286bf124f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684396"
---
Considere as seguintes práticas recomendadas:

- O serviço de gerenciamento não pode recuperar senhas existentes: ele só poderá redefini-las por meio do portal do Azure. É recomendável armazenar todas as senhas em um local seguro para não precisar redefinir uma senha se ela for esquecida. Se você redefinir uma senha, certifique-se de notificar todos os usuários antes de você redefini-lo.
- Você pode acessar a interface do Windows PowerShell de seu dispositivo remotamente por HTTP. Como prática recomendada de segurança, você deve usar HTTP apenas em redes confiáveis.
- Certifique-se de que as senhas do dispositivo são fortes e bem protegidas. Siga as [práticas recomendadas de senha](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management).