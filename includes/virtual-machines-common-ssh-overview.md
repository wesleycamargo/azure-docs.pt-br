---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/08/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: cff3d7bfb89d5b03f986da32edc148efcfb7e7bd
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51506258"
---
## <a name="overview-of-ssh-and-keys"></a>Visão geral do SSH e das chaves

O SSH é um protocolo de conexão criptografada que permite entradas seguras em conexões não protegidas. SSH é o protocolo de conexão padrão para as VMs Linux hospedadas no Azure. Embora o SSH em si forneça uma conexão criptografada, usar senhas com conexões SSH ainda deixa a VM vulnerável a ataques de força bruta ou à adivinhação de senhas. Um método mais seguro e preferencial de se conectar a uma VM usando SSH é usar um par de chaves públicas e privadas, também conhecidas como *chaves SSH*. 

* A *chave pública* é colocada na VM Linux ou em qualquer outro serviço que você queira usar com criptografia de chave pública.

* A *chave privada* no seu sistema local é usado por um cliente SSH para verificar sua identidade quando você se conectar à sua VM do Linux. Proteja essa chave privada. Não a compartilhe.

Dependendo das políticas de segurança da sua organização, você pode reutilizar um único par de chaves públicas e privadas para acessar vários serviços e VMs do Azure. Não é preciso um par de chaves separado para cada VM ou serviço que você deseja acessar. 

Sua chave pública pode ser compartilhada com qualquer pessoa, mas apenas você (ou sua infraestrutura de segurança local) possui sua chave privada.