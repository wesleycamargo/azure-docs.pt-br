---
title: "Certificações do Microsoft Azure para SAP | Microsoft Docs"
description: "Lista atualizada de configurações e certificações atuais do SAP na plataforma do Azure."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: rclaus
ms.custom: 
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8925de446b9dc7e17a2f2033426eb380329c8249
ms.lasthandoff: 04/03/2017


---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certificações e configurações do SAP em execução no Microsoft Azure

O SAP e a Microsoft têm um histórico de longa data de trabalho em conjunto em uma forte parceria que tem benefícios mútuos para seus clientes. A Microsoft está constantemente atualizando sua plataforma e enviando novos detalhes de certificação para o SAP para garantir que o Microsoft Azure seja a melhor plataforma para executar suas cargas de trabalho do SAP. As tabelas a seguir descrevem nossas configurações com suporte e listam as certificações em extensão. 

## <a name="sap-hana-certifications"></a>Certificações de SAP HANA

| Produto SAP | SO com suporte | Ofertas do Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (incluindo o software cliente HANA formado por drivers SQLODBC, ODBO somente Windows, ODBC e JDBC, HANA Studio e banco de dados HANA) |Red Hat Enterprise Linux, SUSE Linux Enterprise | Família de VMs D-Series |
| HANA One |Red Hat Enterprise Linux, SUSE Linux Enterprise |DS14_v2 (mediante disponibilidade geral) |
| SAP S/4 HANA |Red Hat Enterprise Linux, SUSE Linux Enterprise |Disponibilidade controlada para GS5, SAP HANA no Azure (instâncias grandes) |
| Pacote no HANA, OLTP |Red Hat Enterprise Linux, SUSE Linux Enterprise |GS5 para implantações de nó único para cenários que não são de produção, SAP HANA no Azure (instâncias grandes) |
| HANA Enterprise para BW, OLAP |Red Hat Enterprise Linux, SUSE Linux Enterprise |GS5 para implantações de nó único, SAP HANA no Azure (instâncias grandes) |
| SAP BW/4 HANA |Red Hat Enterprise Linux, SUSE Linux Enterprise |GS5 para implantações de nó único, SAP HANA no Azure (instâncias grandes) |

## <a name="sap-netweaver-certifications"></a>Certificações do SAP NetWeaver
O Microsoft Azure está certificado para os produtos SAP a seguir, com total suporte da Microsoft e da SAP.

| Produto SAP | SO convidado | RDBMS | Tipos de máquina virtual |
| --- | --- | --- | --- |
| Software SAP Business Suite |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle (somente Windows), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5 |
| SAP Business All-in-One |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle (somente Windows), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5 |
| SAP BusinessObjects BI |Windows |N/D |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5 |
| SAP NetWeaver |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle (somente Windows), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5 |

