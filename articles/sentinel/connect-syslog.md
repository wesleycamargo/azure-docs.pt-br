---
title: Coletar dados de Syslog em versão prévia do Azure Sentinel | Microsoft Docs
description: Saiba como coletar dados de Syslog em Sentinel do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 55949da97f58f1d8c1670f69d25e92d6bb4e9eef
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447295"
---
# <a name="connect-your-external-solution-using-syslog"></a>Conectar-se a sua solução externa usando Syslog

> [!IMPORTANT]
> Sentinela do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode se conectar a qualquer dispositivo local com suporte para Syslog para Sentinel do Azure. Isso é feito por meio de um agente com base em uma máquina Linux entre o dispositivo e o Azure Sentinel. Se sua máquina Linux está no Azure, você pode transmitir os logs do seu dispositivo ou aplicativo para um espaço de trabalho dedicado criar no Azure e conectá-lo. Se sua máquina Linux não estiver no Azure, você pode transmitir os logs do seu dispositivo para um dedicado no local VM ou computador em que você instala o agente para Linux. 

> [!NOTE]
> Se seu dispositivo dá suporte a CEF Syslog, a conexão é mais completo e você deve escolher essa opção e siga as instruções em [coletando dados de CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Como ele funciona

Coleta de syslog é realizada usando um agente para Linux. Por padrão, o agente para Linux recebe eventos de Syslog daemon através de UDP, mas em casos em que uma máquina Linux é esperada para coletar um alto volume de eventos de Syslog, como quando um agente do Linux é receber eventos de outros dispositivos, a configuração seja alterada para Use o transporte TCP entre o daemon do Syslog e o agente.

## <a name="connect-your-syslog-appliance"></a>Conectar seu dispositivo de Syslog

1. No portal do Azure Sentinel, selecione **coleta de dados** e escolha o **Syslog** lado a lado.
2. Se sua máquina Linux não estiver dentro do Azure, baixe e instale ao Azure Sentinel **agente para Linux** em seu dispositivo. 
1. Se você estiver trabalhando no Azure, selecione ou crie uma VM que no Azure Sentinel espaço de trabalho que é dedicado ao recebimento de mensagens do Syslog. Selecione a VM no Azure Sentinel espaços de trabalho e clique em **Connect** na parte superior do painel esquerdo.
3. Clique em **configurar os logs a serem coletados** novamente na configuração de conector do Syslog. 
4. Clique em **pressione aqui para abrir a folha de configuração**.
1. Selecione **dados** e, em seguida **Syslog**.
   - Verifique se cada recurso que você está enviando a pelo Syslog está na tabela. Para cada recurso que você pretende monitorar, defina uma severidade. Clique em **Aplicar**.
1. Em sua máquina de Syslog, verifique se que você está enviando esses recursos fazem. 

3. Para usar o esquema relevante no Log Analytics para os logs de Syslog, pesquise **Syslog**.




## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como se conectar a dispositivos locais de Syslog a Sentinela do Azure. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).
