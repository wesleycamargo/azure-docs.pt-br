---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 23587e617c62cfe4aa24256330c5f7673dd1c674
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684390"
---
Seu dispositivo está associado uma conta de armazenamento que é usada como um destino para seus dados no Azure. O acesso à conta de armazenamento é controlado pela assinatura e o armazenamento de 512 bits duas chaves associadas à conta de armazenamento de acesso.

Uma das chaves é usada para autenticação quando o dispositivo de borda da caixa de dados acessa a conta de armazenamento. A outra chave é mantida em reserva, permitindo alternar as chaves periodicamente.

Por motivos de segurança, muitos data centers exigem a rotação de chaves. Recomendamos seguir estas práticas recomendadas para a rotação de chaves:

- Sua chave de conta de armazenamento é semelhante para a senha raiz da sua conta de armazenamento. Protege com cuidado sua chave de conta. Não distribua a senha para outros usuários, duro codificá-lo ou salvá-lo em qualquer lugar em texto não criptografado que é acessível a outras pessoas.
- [Regenerar a chave de conta](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) usar o portal do Azure se você achar que ele pode ter sido comprometida.
- Periodicamente, o administrador do Azure deve alterar ou regenerar a chave primária ou secundária usando a seção Armazenamento do portal do Azure para acessar diretamente a conta de armazenamento.