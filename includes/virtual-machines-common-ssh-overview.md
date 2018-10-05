---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 04/16/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0363b719e2587226282257e19e58e3c4e55c2219
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47454451"
---
## <a name="overview-of-ssh-and-keys"></a>Visão geral do SSH e das chaves

O SSH é um protocolo de conexão criptografada que permite entradas seguras em conexões não protegidas. SSH é o protocolo de conexão padrão para as VMs Linux hospedadas no Azure. Embora o SSH em si forneça uma conexão criptografada, usar senhas com conexões SSH ainda deixa a VM vulnerável a ataques de força bruta ou à adivinhação de senhas. Um método mais seguro e preferencial de se conectar a uma VM usando SSH é usar um par de chaves públicas e privadas, também conhecidas como *chaves SSH*. 

* A *chave pública* é colocada na VM Linux ou em qualquer outro serviço que você queira usar com criptografia de chave pública.

* A *chave privada* é o que é apresentado à VM Linux ao estabelecer uma conexão SSH para verificar sua identidade. Proteja essa chave privada. Não a compartilhe.

Dependendo das políticas de segurança da sua organização, você pode reutilizar um único par de chaves públicas e privadas para acessar vários serviços e VMs do Azure. Não é preciso um par de chaves separado para cada VM ou serviço que você deseja acessar. 

Sua chave pública pode ser compartilhada com qualquer pessoa, mas apenas você (ou sua infraestrutura de segurança local) possui sua chave privada.