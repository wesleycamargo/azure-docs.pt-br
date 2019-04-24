---
title: Dimensionamento do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Dimensionamento do SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d403c284eefc1351aabb632977832f2bf11757a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60477115"
---
# <a name="sizing"></a>Dimensionamento

O dimensionamento do HANA em Instâncias Grandes não é diferente do dimensionamento do HANA em geral. Para sistemas existentes e implantados que você deseja migrar de outro RDBMS para o HANA, o SAP fornece vários relatórios que são executados nos sistemas SAP existentes. Se o banco de dados é movido para o HANA, esses relatórios verificam os dados e calculam os requisitos de memória para a instância do HANA. Para obter mais informações sobre como executar esses relatórios e obter os patches ou versões mais recentes, leia as seguintes Notas SAP:

- [Nota SAP nº 1793345 – Dimensionamento do SAP Suite no HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [Nota SAP nº 1872170 – Relatório de dimensionamento do Suite no HANA e S/4 HANA](https://launchpad.support.sap.com/#/notes/1872170)
- [Nota da SAP #2121330 - perguntas Frequentes: SAP BW no HANA relatório de dimensionamento](https://launchpad.support.sap.com/#/notes/2121330)
- [Nota SAP nº 1736976 – Relatório de dimensionamento do BW no HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [Nota SAP nº 2296290 – Novo relatório de dimensionamento do BW no HANA](https://launchpad.support.sap.com/#/notes/2296290)

Para implementações de campo verde Sizer rápida SAP está disponível para calcular os requisitos de memória da implementação de software SAP sobre HANA.

Os requisitos de memória para o HANA aumentam conforme o volume de dados aumenta. Esteja ciente do seu consumo atual de memória para ajudar a prever o que será no futuro. Com base nos requisitos de memória, é possível e mapear a demanda em uma das SKUs do SAP HANA em Instâncias Grandes.

**Próximas etapas**
- Veja [Requisitos de integração](hana-onboarding-requirements.md)