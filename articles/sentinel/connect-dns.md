---
title: Conectar-se os dados DNS na visualização do Azure Sentinel | Microsoft Docs
description: Saiba como se conectar a dados DNS no Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: e34db2bdc78eb846cf4885b1ef083fd3b21e21b5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792044"
---
# <a name="connect-your-domain-name-server"></a>Connect your domain name server

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode se conectar a qualquer servidor DNS (Domain Name) em execução no Windows para o Azure Sentinel. Isso é feito ao instalar um agente no computador do DNS. Registra em log usando o DNS, você pode obter insights de operações relacionadas à infra-estrutura do DNS da sua organização, desempenho e segurança ao coletar, analisar, e correlacionar analítica e logs de auditoria e outros dados relacionados dos servidores DNS.

Quando você habilita a conexão do log DNS, você pode:
- Identificar clientes que tentam resolver nomes de domínio mal-intencionados
- Identificar registros de recursos obsoletos
- Identificar os nomes de domínio consultados com frequência e clientes DNS comunicativos
- Exibir carga de solicitação em servidores DNS
- Exibir falhas de registro de DNS dinâmico

## <a name="how-it-works"></a>Como ele funciona

Conexão do DNS é feito pela instalação de um agente no computador do DNS. O agente recebe eventos do DNS e os passa para o Log Analytics.

## <a name="connect-your-dns-appliance"></a>Conectar seu dispositivo de DNS

1. No portal do Azure Sentinel, selecione **conectores de dados** e escolha o **DNS** lado a lado.
1. Se suas máquinas DNS estão no Azure:
    1. Clique em **baixar e instalar o agente para máquinas virtuais de Windows**.
    1. No **máquinas virtuais** , selecione a máquina DNS que você deseja transmitir para o Azure Sentinel. Verifique se que esta é uma VM do Windows.
    1. Na janela que é aberta para a VM, clique em **Connect**.  
    1. Clique em **habilitar** na **conector DNS** janela. 

2. Se sua máquina DNS não for uma VM do Azure:
    1. Clique em **baixar e instalar o agente para máquinas do Windows Azure não**.
    1. No **agente direto** janela, selecione **agente baixar o Windows (64 bits)** ou **agente baixar o Windows (32 bits)**.
    1. Instale o agente em seu computador DNS. Cópia de **ID do espaço de trabalho**, **chave primária**, e **chave secundária** e usá-los quando solicitado durante a instalação.

3. Para usar o esquema relevante no Log Analytics para os logs DNS, pesquise **DnsEvents**.

## <a name="validate"></a>Validar 

No Log Analytics, procure o esquema **DnsEvents** e verifique se há eventos.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar dispositivos de locais DNS ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).
