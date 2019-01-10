---
title: O que fazer no caso de uma interrupção de serviço do Azure afetar o Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Saiba o que fazer no caso de uma interrupção de serviço do Azure afetar o Cofre de Chaves do Azure.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
editor: ''
ms.assetid: 19a9af63-3032-447b-9d1a-b0125f384edb
ms.service: key-vault
ms.workload: key-vault
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: a8f9b1a9ac72498e2e8374926ba3ae47a3eaff09
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54078330"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Redundância e disponibilidade de Cofre de Chaves do Azure

O Cofre de Chaves do Azure tem várias camadas de redundância, a fim de garantir que seus segredos e chaves permaneçam disponíveis para seu aplicativo até mesmo se os componentes individuais do serviço falharem.

O conteúdo de seu cofre de chaves é replicado na região e em uma região secundária a pelo menos 150 milhas de distância, mas na mesma região geográfica. Isso mantém a alta durabilidade de seus segredos e chaves. Consulte o documento [Regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) para obter detalhes sobre pares de regiões específicos.

Se os componentes individuais dentro do serviço de cofre de chaves falharem, os componentes alternativos dentro da região interferirão para atender à sua solicitação, de modo a garantir que não haja degradação da funcionalidade. Você não precisa executar qualquer ação para disparar esse recurso. Ele ocorre automaticamente de modo transparente para você.

No eventual caso de uma região inteira do Azure ficar indisponível, as solicitações que você faz do Cofre de Chaves do Azure nessa região são roteadas automaticamente (*failover*) para uma região secundária. Quando a região primária estiver disponível novamente, as solicitações serão roteadas de volta (*failback*) para a região primária. Novamente, não é necessário executar nenhuma ação, pois isso acontecerá de modo automático.

Há algumas advertências que você deve conhecer:

* No caso de um failover de região, pode levar alguns minutos para o serviço executar failover. Solicitações feitas durante esse período podem falhar até que o failover seja concluído.
* Após a conclusão de um failover, o cofre de chaves estará no modo somente leitura. As solicitações permitidas nesse modo são:
  * Listar cofres de chave
  * Obter propriedades de cofres de chave
  * Listar segredos
  * Obter segredos
  * Listar chaves
  * Obter (propriedades das) chaves
  * Criptografar
  * Descriptografar
  * Encapsular
  * Desencapsular
  * Verificar
  * Assinar
  * Backup
* Após o failback de um failover, todos os tipos de solicitação (ou seja, solicitações de leitura *e* de gravação) são disponibilizados.

