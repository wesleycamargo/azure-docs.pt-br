---
title: "Integração do SDK do Android do Azure Mobile Engagement"
description: "Atualizações e procedimentos mais recentes para o SDK do Android do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 585341f9-3f39-459a-af42-864e400a0128
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: af776804affea5abe599594d7b486bbead982f3e
ms.lasthandoff: 11/17/2016


---
# <a name="release-notes"></a>Notas de versão
## <a name="423-08102016"></a>4.2.3 (08/10/2016)
* Nenhum outro bloqueio de WIFI.
* Corrige um deadlock ao chamar getDeviceId antes de init (bug introduzido na versão 4.2.0).

## <a name="422-05172016"></a>4.2.2 (17/05/2016)
* Aprimoramentos de estabilidade.

## <a name="421-05102016"></a>4.2.1 (10/05/2016)
* Segurança: desabilite o acesso de arquivo local de exibição na Web.
* Segurança: remova a classe `EngagementPreferenceActivity` que estende a classe `PreferenceActivity` obsoleta e não segura.
* Segurança: as atividades de alcance agora estão documentadas para usar `exported="false"`; esse sinalizador também pode ser usado nas versões anteriores do SDK.

## <a name="420-03112016"></a>4.2.0 (03/11/2016)
* O SDK está licenciado sob MIT.
* Permita a especificação de um identificador de dispositivo personalizado no momento de inicialização do SDK.

## <a name="415-02012016"></a>4.1.5 (01/02/2016)
* Aprimoramentos de estabilidade.

## <a name="414-01262016"></a>4.1.4 (26/01/2016)
* Aprimoramentos de estabilidade.

## <a name="413-1292015"></a>4.1.3 (09/12/2015)
* Aprimoramentos de estabilidade.

## <a name="412-11252015"></a>4.1.2 (25/11/2015)
* Aprimoramentos de estabilidade.

## <a name="411-11042015"></a>4.1.1 (04/11/2015)
* Aprimoramentos de estabilidade.

## <a name="410-08252015"></a>4.1.0 (25/08/2015)
* Lidar com o novo modelo de permissão para Android M.
* Agora é possível configurar os recursos de localização no tempo de execução em vez de usar o `AndroidManifest.xml`.
* Corrija um bug de permissão: se você usar `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION` não será mais necessário.
* Aprimoramentos de estabilidade.

## <a name="400-07062015"></a>4.0.0 (07/06/2015)
* Alterações de protocolo interno para tornar a análise e o envio mais confiáveis.
* O push nativo (GCM/ADM) agora também é usado nas notificações de aplicativo para que você deve configurar as credenciais por push nativo para qualquer tipo de campanha de envio.
* Corrigir a notificação de visão geral: foram exibidas somente 10s depois de enviadas.
* Corrigir um bug no modo de exibição da web: ao clicar em um link também estava sendo executada a URL da ação padrão.
* Corrigir uma falha rara relacionada ao gerenciamento de armazenamento local.
* Corrigir o gerenciamento de cadeia de caracteres de configuração dinâmica.
* Atualizar EULA.

## <a name="300-02172015"></a>3.0.0 (17/02/2015)
* Versão Inicial do Mobile Engagement do Azure
* A configuração do appId é substituída por uma configuração de cadeia de conexão.
* Removida a API para enviar e receber mensagens XMPP arbitrárias de entidades XMPP arbitrárias.
* Removida a API para enviar e receber mensagens entre dispositivos.
* Aprimoramentos de segurança.
* Acompanhamento do Google Play e SmartAd removido.


