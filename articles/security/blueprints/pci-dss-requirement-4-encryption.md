---
title: Diagrama de processamento de pagamento do Azure - Requisitos de criptografia
description: Requisito 4 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 43f75ba9-cb4e-49ab-b3f4-09e48310bc18
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 3eb5b663558c2a68c13368b179ff942dd3c53716
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="encryption-requirements-for-pci-dss-compliant-environments"></a>Requisitos de criptografia para ambientes em conformidade com o PCI DSS 
## <a name="pci-dss-requirement-4"></a>Requisito 4 de PCI DSS

**Criptografar a transmissão de dados do titular do cartão em redes abertas e públicas**

> [!NOTE]
> Esses requisitos são definidos pelo [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) como parte do [Padrão de Segurança de Dados PCI (PCI DSS), Versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Confira o PCI DSS para obter informações sobre os procedimentos de teste e diretrizes para cada requisito.

As informações confidenciais devem ser criptografadas durante a transmissão em redes que sejam facilmente acessadas por pessoas mal-intencionadas. As redes sem fios mal configuradas e vulnerabilidades na criptografia herdada e protocolos de autenticação continuam sendo os alvos de indivíduos mal-intencionados que exploram essas vulnerabilidades para obter acesso privilegiado a ambientes de dados de titulares de cartão.

## <a name="pci-dss-requirement-41"></a>Requisito 4.1 de PCI DSS

**4.1** Use uma criptografia forte e protocolos de segurança (por exemplo, TLS, IPSEC, SSH, etc.) para proteger dados confidenciais de titulares de cartão durante a transmissão por redes abertas e públicas, incluindo o seguinte:
- Apenas são aceitas chaves e certificados confiáveis.
- O protocolo em uso somente suporta versões ou configurações seguras.
- O nível da criptografia é adequado para a metodologia de criptografia em uso. 

> [!NOTE]
> Quando se usa SSL/TLS hoje obsoleto, os requisitos no Apêndice A2 devem ser concluídos.
>
> Exemplos de redes abertas e públicas incluem, mas não estão limitados a:
> - A Internet
> - Tecnologias sem fio, incluindo 802.11 e Bluetooth
> - As tecnologias de rede celular, por exemplo, o sistema de comunicações Global System for Mobile (GSM), o Code Division Multiple Access (CDMA)
> - Serviço de Rádio de Pacotes Gerais (GPRS)
> - Comunicações por satélite


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(Diagrama PCI&#8209;DSS&nbsp;)** | A Contoso Webstore é uma solução PaaS que implanta uma criptografia forte para a implantação seguinte:<br /><br />Para atender aos requisitos de criptografia de dados em repouso, o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa o seguinte:<br /><br /><ul><li>[SSE (Criptografia de Serviço de Armazenamento) do Azure para dados em repouso](/azure/storage/storage-service-encryption)</li><li>Banco de Dados SQL: uma instância de banco de dados SQL de PaaS é usada para exibir as medidas de segurança de banco de dados. Para saber mais, confira [Diretrizes de PCI – Banco de Dados SQL do Azure](payment-processing-blueprint.md#azure-sql-database).</li><li>[Azure Disk Encryption (Bitlocker)](/azure/security/azure-security-disk-encryption)</li></ul>O uso do Azure Key Vault atende aos requisitos de HIPAA, PCI DSS e Azure Governamental.|



### <a name="pci-dss-requirement-411"></a>Requisito 4.1.1 de PCI DSS

**4.1.1** Verifique que as redes sem fios transmitem dados do titular do cartão, ou que estão conectados ao ambiente de dados do titular do cartão, use as práticas recomendadas do setor (por exemplo, IEEE 802.11 i) para implantar uma criptografia forte para autenticação e para transmissão.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(Diagrama PCI&#8209;DSS&nbsp;)** | O acesso sem fios e SNMP não estão implantados na solução.|



## <a name="pci-dss-requirement-42"></a>Requisito 4.2 de PCI DSS

**4.2** Nunca enviar PANs desprotegidas por meio de tecnologias de mensagens com base no usuário final (por exemplo: email, mensagens instantâneas, SMS, bate-papo, etc.).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(Diagrama PCI&#8209;DSS&nbsp;)** | A Contoso Webstore não tem nenhuma solução de mensagens implementada que possa enviar dados de número de conta primária (PAN) desprotegidos.|



## <a name="pci-dss-requirement-43"></a>Requisito 4.3 de PCI DSS

**4.3** Garantir que políticas de segurança e procedimentos operacionais para criptografar as transmissões de dados de titular do cartão armazenados estejam documentados, em uso e sejam do conhecimento de todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(Diagrama PCI&#8209;DSS&nbsp;)** | Os clientes são responsáveis por documentar e criptografar as transmissões que contêm os dados.|




