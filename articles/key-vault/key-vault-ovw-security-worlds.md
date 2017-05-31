---
ms.assetid: 
title: "Universos de segurança do Azure Key Vault | Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 6477e8d91b71361ef91763418b22596deb216f02
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Universos de segurança e limites geográficos do Azure Key Vault

O Azure Key Vault é um serviço multilocatário e usa um pool de HSMs (Módulos de Segurança de Hardware) em cada localização do Azure. 

Todos os HSMs nas localizações do Azure da mesma região geográfica compartilham o mesmo limite de criptografia (Universo de Segurança da Thales). Por exemplo, o Leste e o Oeste dos EUA compartilham o mesmo universo de segurança porque pertencem à localização geográfica dos EUA. Da mesma forma, todas as localizações do Azure no Japão compartilham o mesmo universo de segurança e todas as localizações do Azure na Austrália, na Índia e assim por diante. 

Um backup feito de uma chave de um cofre de chaves em uma localização do Azure pode ser restaurado para um cofre de chaves em outra localização do Azure, desde que as seguintes condições sejam verdadeiras:

- Ambas as localizações do Azure pertencem à mesma localização geográfica
- Ambos os cofres de chaves pertencem à mesma assinatura do Azure

Por exemplo, um backup feito por determinada assinatura de uma chave em um cofre de chaves na Índia Ocidental só pode ser restaurado para outro cofre de chaves na mesma assinatura e localização geográfica: Índia Ocidental, Índia Central ou Sul da Índia. 



