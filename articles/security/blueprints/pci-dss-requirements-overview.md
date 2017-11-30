---
title: "Diagrama de Processamento de Pagamento do Azure - Visão geral de alto nível"
description: "Diagrama de processamento de pagamento do Azure - matriz de responsabilidade do cliente (visão geral)"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 23cf68d8-bebd-4ac4-a194-39e052281c0e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 49acce706f09fe08b257ce8a8554de5da20060a1
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="pci-dss-requirements---high-level-overview"></a>Requisitos de PCI DSS - visão geral de alto nível

O Payment Card Industry Data Security Standard (PCI DSS) foi desenvolvido para incentivar e aumentar a segurança de dados do titular do cartão, e facilitar a ampla adoção global de medidas de segurança de dados consistente. O PCI DSS fornece uma linha de base de requisitos técnicos e operacionais criada para proteger os dados da conta. O PCI DSS se aplica a todas as entidades envolvidas no processamento de cartões de pagamento, inclusive comerciantes, processadores, consumidores, emissores e provedores de serviços. O PCI DSS também se aplica a todas as outras entidades que armazenam, processam ou transmitem dados de titulares de cartões(CHD) e/ou dados de autenticação confidenciais (SAD). Veja abaixo uma visão geral de alto nível dos 12 requisitos de PCI DSS.

> [!NOTE]
> Esses requisitos são definidos pelo [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) como parte do [Padrão de Segurança de Dados PCI (PCI DSS), Versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Confira o PCI DSS para obter informações sobre os procedimentos de teste e diretrizes para cada requisito.

|   |   |
|---|---|
| **Criar e manter a segurança em <br/>redes e sistemas** | 1. [Instalar e manter uma configuração de firewall para proteger os dados do titular do cartão](pci-dss-requirement-1-firewall.md)<br/><br/> 2. [Não usar padrões de fornecedores para senhas do sistema e outros parâmetros de segurança](pci-dss-requirement-2-password.md) |  
| **Proteger os Dados do Titular do Cartão** | 3. [Proteger os dados do titular do cartão armazenados](pci-dss-requirement-3-chd.md)<br/><br/> 4. [Criptografar a transmissão de dados do titular do cartão em redes abertas e públicas](pci-dss-requirement-4-encryption.md) |
| **Manter um <br/>Programa de Gerenciamento de** Vulnerabilidades | 5. [Proteger todos os sistemas contra malware e atualizar regularmente softwares ou programas antivírus](pci-dss-requirement-5-malware.md)<br/><br/> 6. [Desenvolver e manter os aplicativos e sistemas em segurança](pci-dss-requirement-6-secure-system.md) |
| **Implementar Medidas Fortes de <br/> Controle de Acesso** | 7. [Restringir o acesso aos dados do titular do cartão segundo a necessidade de conhecimento](pci-dss-requirement-7-access.md)<br/><br/> 8. [Identificar e autenticar o acesso aos componentes do sistema](pci-dss-requirement-8-identity.md) <br/><br/> 9. [Restringir o acesso físico aos dados do titular do cartão](pci-dss-requirement-9-physical-access.md) |
| **Monitorar e <br/>Testar Regularmente as Redes** | 10. [Acompanhar e monitorar todo o acesso aos recursos da rede e aos dados do titular do cartão](pci-dss-requirement-10-monitoring.md) <br/><br/> 11. [Testar regularmente os sistemas e processos de segurança](pci-dss-requirement-11-testing.md) |
| **Manter uma<br/>Política de Segurança de**Informações | 12. [Manter uma política direcionada à segurança de informações para todos os funcionários](pci-dss-requirement-12-policy.md) |

