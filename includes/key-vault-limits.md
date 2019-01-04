---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: efa367157a8fd896cdc9680bf2ab6ba6a9e3dbb0
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429914"
---
Transações principais (Máximo de transações permitidas em 10 segundos, por cofre, por região<sup>1</sup>):

|Tipo de chave|Chave HSM<br>Chave CREATE|Chave HSM<br>Todas as outras transações|Chave de software<br>Chave CREATE|Chave de software<br>Todas as outras transações|
|:---|---:|---:|---:|---:|
|RSA de 2048 bits|5|1000|10|2000|
|RSA de 3072 bits|5|250|10|500|
|RSA de 4096 bits|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
|

> [!NOTE]
> Se você examinar a tabela a seguir, verá que para chaves protegidas por software, permitimos 2000 transações por 10 segundos e para HSM com suporte a chaves, permitimos 1000 transações por 10 segundos. A taxa de transações de software com suporte para 3072 chaves às chaves de 2048 é 500/2000 ou 0,4. Isso significa que, se um cliente fizer 500 3072 transações de chave em 10 segundos, atingirá seu limite máximo e não poderá fazer outras operações de chave. 
   
|Tipo de chave  | Chave de software |Chave HSM  |
|---------|---------|---------|
|RSA de 2048 bits     |    2000     |   1000    |
|RSA de 3072 bits     |     500    |    250     |
|RSA de 4096 bits     |    125     |    250     |
|ECC P-256     |    2000     |  1000     |
|ECC P-384     |    2000     |  1000     |
|ECC P-521     |    2000     |  1000     |
|ECC SECP256K1     |    2000     |  1000     |


Transações de cofre, Segredos e Chaves de Conta de Armazenamento Gerenciadas:
| Tipo de transação | Máximo de transações permitidas em 10 segundos, por cofre, por região<sup>1</sup> |
| --- | --- |
| Todas as transações |2000 |
|

Consulte [diretrizes de limitação do Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md) para obter informações sobre como lidar com a limitação quando esses limites são excedidos.

<sup>1</sup> Há um limite que afeta toda a assinatura para todos os tipos de transação, que é 5 vezes por limite do cofre de chaves. Por exemplo, HSM- outras transações por assinatura são limitadas a 5000 transações em 10 segundos por assinatura.
