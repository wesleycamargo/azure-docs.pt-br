---
title: Certificações do Microsoft Azure para SAP | Microsoft Docs
description: Lista atualizada de configurações e certificações atuais do SAP na plataforma do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/12/2018
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: d1d2bd74a5118b48f7ed79a77ca2d7b6545607c6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072373"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certificações e configurações do SAP em execução no Microsoft Azure

O SAP e a Microsoft têm um histórico de longa data de trabalho em conjunto em uma forte parceria que tem benefícios mútuos para seus clientes. A Microsoft está constantemente atualizando sua plataforma e enviando novos detalhes de certificação para o SAP para garantir que o Microsoft Azure seja a melhor plataforma para executar suas cargas de trabalho do SAP. As tabelas a seguir descrevem configurações com suporte do Azure e listam as certificações SAP em extensão. 

## <a name="sap-hana-certifications"></a>Certificações de SAP HANA
Referências:

- [Plataformas de IaaS certificadas pelo SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) para suporte de SAP HANA para VMs nativas do Azure e Instâncias Grandes do HANA.

| Produto SAP | SO com suporte | Ofertas do Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (incluindo o software cliente HANA formado por drivers SQLODBC, ODBO somente Windows, ODBC e JDBC, HANA Studio e banco de dados HANA) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Família de VMs D-Series |
| Business One on HANA | SUSE Linux Enterprise | DS14_v2 |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Disponibilidade controlada para GS5, M64ms, M128s, M128ms, SAP HANA no Azure (instâncias grandes) |
| Pacote no HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 para cenários que não sejam de produção, M64ms, M128s, M128ms, SAP HANA no Azure (instâncias grandes) |
| HANA Enterprise para BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64ms, M128s, M128ms, SAP HANA no Azure (instâncias grandes) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64ms, M128s, M128ms, SAP HANA no Azure (instâncias grandes) |

Todas as VMs do Azure são certificadas para dimensionamento do SAP HANA até o momento.

## <a name="sap-netweaver-certifications"></a>Certificações do SAP NetWeaver
O Microsoft Azure está certificado para os produtos SAP a seguir, com total suporte da Microsoft e da SAP.
Referências:

- [1928533 - Aplicativos SAP no Azure: Produtos com suporte e tipos de VM do Azure](https://launchpad.support.sap.com/#/notes/1928533) para todos os aplicativos baseados no SAP NetWeaver, incluindo SAP TREX, SAP LiveCache e o servidor de conteúdo do SAP. E todos os bancos de dados, exceto o SAP HANA.


| Produto SAP | SO convidado | RDBMS | Tipos de máquina virtual |
| --- | --- | --- | --- |
| Software SAP Business Suite | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (somente Windows e Oracle Linux), DB2, SAP ASE |A5 para A11, D11 para D14, DS11 para DS14, DS11_v2 para DS15_v2, GS1 para GS5, D2s_v3 para D64s_v3, E2s_v3 para E64s_v3, M64s para M128ms |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (somente Windows e Oracle Linux), DB2, SAP ASE |A5 para A11, D11 para D14, DS11 para DS14, DS11_v2 para DS15_v2, GS1 para GS5, D2s_v3 para D64s_v3, E2s_v3 para E64s_v3, M64s para M128ms |
| SAP BusinessObjects BI | Windows |N/D |A5 para A11, D11 para D14, DS11 para DS14, DS11_v2 para DS15_v2, GS1 para GS5, D2s_v3 para D64s_v3, E2s_v3 para E64s_v3, M64s para M128ms |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (somente Windows e Oracle Linux), DB2, SAP ASE |A5 para A11, D11 para D14, DS11 para DS14, DS11_v2 para DS15_v2, GS1 para GS5, D2s_v3 para D64s_v3, E2s_v3 para E64s_v3, M64s para M128ms |

## <a name="other-sap-workload-supported-on-azure"></a>Outra carga de trabalho do SAP com suporte no Azure

| Produto SAP | SO convidado | RDBMS | Tipos de máquina virtual |
| --- | --- | --- | --- |
| SAP Business One no SQL Server | Windows  | SQL Server | Todos os tipos de VM certificadas pelo NetWeaver<br /> [Nota SAP #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows e Linux | | Todos os tipos de VM certificadas pelo NetWeaver<br /> Nota SAP nº 2451795 |
| Plataforma BI do SAP Business Objects | Windows e Linux | | Nota SAP nº 2145537 |
| SAP Data Services 4.2 | | | Nota SAP nº 2288344 |
| Plataforma de comércio Hybris SAP 5.x e 6.x | Windows | SQL Server, Oracle | Todos os tipos de VM certificadas pelo NetWeaver<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
