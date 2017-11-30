---
title: "Custos de software do Windows para Instâncias de VM de Reserva do Azure | Microsoft Docs"
description: "Saiba quais medidores são usados para software do Windows na VM do Windows que se qualifica para a Instância Reservada."
services: billing
documentationcenter: 
author: manish-shukla01
manager: manshuk
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: b985e6e9575ffeedcac5bcb3f94a43d23fdbb85e
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="windows-software-costs-not-included-with-reserved-instances"></a>Os custos de software do Windows não estão incluídos nas Instâncias Reservadas

Se você não tiver o Benefício do Uso de Híbrido do Azure na sua VM de Instância Reservada, será cobrado pelos medidores de software do Windows listados na tabela a seguir:

| MeterId | MeterName no arquivo de utilização | Utilizado por VM |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Reservation-Windows Svr Intermitente (1 Núcleo) | Série B |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Reservation-Windows Svr Intermitente (2 Núcleos) | Série B |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Reservation-Windows Svr Intermitente (4 Núcleos) | Série B |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Reservation-Windows Svr Intermitente (8 Núcleos) | Série B |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Reservation-Windows Svr (1 Núcleo) | Todas, exceto Série B |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Reservation-Windows Svr (2 Núcleos) | Todas, exceto Série B |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Reservation-Windows Svr (4 Núcleos) | Todas, exceto Série B |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Reservation-Windows Svr (6 Núcleos) | Todas, exceto Série B |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Reservation-Windows Svr (6 Núcleos) | Todas, exceto Série B |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Reservation-Windows Svr (12 Núcleos) | Todas, exceto Série B |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Reservation-Windows Svr (16 Núcleos) | Todas, exceto Série B |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Reservation-Windows Svr (20 Núcleos) | Todas, exceto Série B |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Reservation-Windows Svr (24 Núcleos) | Todas, exceto Série B |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Reservation-Windows Svr (32 Núcleos) | Todas, exceto Série B |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Reservation-Windows Svr (40 Núcleos) | Todas, exceto Série B |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Reservation-Windows Svr (64 Núcleos) | Todas, exceto Série B |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Reservation-Windows Svr (72 Núcleos) | Todas, exceto Série B |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Reservation-Windows Svr (128 Núcleos) | Todas, exceto Série B |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Reservation-Windows Svr (256 Núcleos) | Todas, exceto Série B |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Reservation-Windows Svr (96 Núcleos) | Todas, exceto Série B |

Você pode obter o custo de cada um desses medidores por meio da API RateCard do Azure. Para obter informações sobre como obter as taxas de um medidor do Azure, consulte [Obter informações sobre preço e metadados para recursos usados em uma assinatura do Azure](https://msdn.microsoft.com/library/azure/mt219004).