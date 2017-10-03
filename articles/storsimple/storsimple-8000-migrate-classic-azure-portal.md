---
title: "Migrar contas de armazenamento e assinaturas do serviço Gerenciador de Dispositivos do StorSimple | Microsoft Docs"
description: "Saiba como migrar assinaturas, contas de armazenamento do serviço Gerenciador de Dispositivos do StorSimple 8000."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: a64cf0ba2a4646a03c864193fb93de4f54f38039
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrar assinaturas e contas de armazenamento associadas ao serviço Gerenciador de Dispositivos do StorSimple

Talvez seja necessário mover o serviço StorSimple para um novo registro ou uma nova assinatura. Esses cenários de migração são alterações na conta ou no datacenter. Use a tabela a seguir para entender para qual desses cenários há suporte, incluindo as etapas detalhadas para a movimentação.

## <a name="account-changes"></a>Alterações na conta

| Você pode mover...| Suportado| Tempo de inatividade| Processo de Suporte do Azure| Abordagem|
|-----|-----|-----|-----|-----|
| Uma assinatura inteira (inclui o serviço StorSimple e contas de armazenamento) para outro registro? | Sim       | Não       | **Transferência de Registro**<br>Use:<li>Quando você adquire um novo compromisso do Azure nos termos de um novo contrato.</li><li>Você deseja migrar todas as contas e assinaturas do registro antigo para o novo. Isso inclui todos os serviços do Azure na assinatura antiga.</li> | **Etapa 1: Abrir um tíquete de Suporte de Operação do Azure Enterprise.**<li>Acesse [http://aka.ms/AzureEnt](http://aka.ms/AzureEnt).</li><li> Selecione **Administração do Registro** e, em seguida, selecione **Transferir de um registro para um novo registro**.<br>**Etapa 2: fornecer as informações solicitadas**<br>Inclua:<li>número de registro de origem</li><li> número de registro de destino</li><li>data de efetivação de transferência|
| Um serviço StorSimple de uma conta existente para um novo registro?    | Sim       | Não       | **Transferência de Conta**<br>Use:<li>Quando você não deseja fazer uma transferência completa de registro.</li><li>Você deseja apenas mover contas específicas para um novo registro.</li>| **Etapa 1: Abrir um tíquete de Suporte de Operação do Azure Enterprise.**<li>Acesse [http://aka.ms/AzureEnt](http://aka.ms/AzureEnt).</li><li>Selecione **Administração do Registro** e, em seguida, selecione **Transferir uma Conta do EA para um novo registro**.<br>**Etapa 2: fornecer as informações solicitadas**<br>Inclua:<li>número de registro de origem</li><li> número de registro de destino</li><li>data de efetivação de transferência|
| Um serviço StorSimple de uma assinatura para outra assinatura?      | Não        |    Sim         | Nenhum, processo manual|<li>Migre dados para fora do dispositivo StorSimple.</li><li>Execute uma redefinição de fábrica do dispositivo. Isso excluirá todos os dados locais do dispositivo.</li><li>Registre o dispositivo com a nova assinatura para um serviço Gerenciador de Dispositivos do StorSimple.</li><li>Migre os dados novamente para o dispositivo.|
| Um dispositivo StorSimple de um serviço Gerenciador de Dispositivos do StorSimple para outro serviço em outra região?      | Não        | Sim            | Nenhum, processo manual |Mesmo que acima.|

## <a name="datacenter-changes"></a>Alterações no datacenter

| Você pode mover...| Suportado|Tempo de inatividade| Processo de Suporte do Azure| Abordagem|
|-----|-----|-----|-----|-----|-----|
| Um serviço StorSimple de um datacenter do Azure para outro? | Não | Sim |Nenhum, processo manual  |<li>Migre dados para fora do dispositivo StorSimple.</li><li>Execute uma redefinição de fábrica do dispositivo. Isso excluirá todos os dados locais do dispositivo.</li><li>Registre o dispositivo com a nova assinatura para um novo serviço Gerenciador de Dispositivos do StorSimple.</li><li>Migre os dados novamente para o dispositivo.|
| Uma conta de armazenamento de um datacenter do Azure para outro? | Não |Sim  |Nenhum, processo manual  | Mesmo que acima.|

## <a name="next-steps"></a>Próximas etapas

* [Implantar um serviço do Gerenciador de Dispositivos do StorSimple](storsimple-8000-manage-service.md)
* [Implantar um dispositivo StorSimple da série 8000 no portal do Azure](storsimple-8000-deployment-walkthrough-u2.md)
