---
title: O que é HSM Dedicado? – HSM Dedicado do Azure | Microsoft Docs
description: A visão geral do HSM dedicado do Azure apresenta recursos de armazenamento de chaves no Azure que atendem à certificação FIPS 140-2 Nível 3
services: dedicated-hsm
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 1eeafa33c8c1cdbcd7d0e55e3860dda1b8d451fe
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080071"
---
# <a name="what-is-azure-dedicated-hsm"></a>O que é o HSM dedicado do Azure?

O HSM dedicado do Azure é um serviço do Azure que fornece armazenamento de chave de criptografia no Azure. O HSM dedicado atende aos requisitos de segurança mais rigorosos. É a solução ideal para clientes que precisam de dispositivos certificados pelo padrão FIPS 140-2 Nível 3 e de controle total e exclusivo do dispositivo HSM. Os dispositivos HSM são implantados globalmente em várias regiões do Azure e podem ser provisionados facilmente como um par de dispositivos e configurados para alta disponibilidade. Os HSMs também podem ser provisionados entre regiões como proteção contra failover em nível regional. A Microsoft criou o serviço HSM Dedicado usando o dispositivo [SafeNet Luna Network HSM 7 (Modelo A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) da Gemalto. Esse dispositivo oferece os mais altos níveis de desempenho e opções de integração de criptografia. Quando provisionados, os HSMs são conectados diretamente à rede virtual do cliente e também podem ser acessados por ferramentas de gerenciamento e de aplicativos locais com a configuração de conectividade VPN ponto a site ou site a site. Os clientes podem adquirir o software e a documentação para configurar e gerenciar os dispositivos HSM no portal de suporte da Gemalto.

## <a name="why-use-azure-dedicated-hsm"></a>Por que usar o HSM Dedicado do Azure?

### <a name="fips-140-2-level-3-compliance"></a>Conformidade com o padrão FIPS 140-2 Nível 3

Muitas organizações têm normas rígidas do setor que exigem que o armazenamento de chaves de criptografia atenda aos requisitos do padrão [FIPS 140-2 Nível 3](https://csrc.nist.gov/publications/detail/fips/140/2/final). O Serviço Azure Key Vault multilocatário da Microsoft atualmente é certificado apenas pelo padrão FIPS 140-2 Nível 2. O HSM Dedicado do Azure atende a uma necessidade real dos setores de serviços financeiros, agências governamentais e outros que precisam atender aos requisitos do padrão FIPS 140-2 Nível 3.

### <a name="single-tenant-devices"></a>Dispositivos de único locatário

Muitos de nossos clientes são obrigados a ter um único locatário no dispositivo de armazenamento de criptografia. O serviço HSM Dedicado do Azure permite o provisionamento de um dispositivo físico em um dos datacenters da Microsoft distribuídos globalmente. Uma vez provisionado para um cliente, somente ele será capaz de acessar o dispositivo.

### <a name="full-administrative-control"></a>Controle administrativo total

Assim como dispositivos de único locatário, muitos clientes precisam de controle administrativo total e acesso único para fins administrativos. Uma vez provisionado, apenas o cliente tem acesso ao dispositivo no nível administrativo ou de aplicativos. A Microsoft não terá nenhum controle administrativo após o primeiro acesso do cliente, o que exige a alteração da senha. A partir daí, o cliente é realmente um locatário único com controle administrativo total e com capacidade de gerenciamento de aplicativos. A Microsoft mantém um acesso de nível de monitor (não uma função de administrador) para dados telemétricos, na conexão de porta serial, que abrangem monitores de hardware como temperatura, integridade da fonte de alimentação e integridade do ventilador. O cliente pode desabilitar esse monitoramento se quiser, mas não receberá alertas de integridade proativos da Microsoft.

### <a name="high-performance"></a>Alto Desempenho

O dispositivo Gemalto foi selecionado para esse serviço devido à ampla variedade de suporte a algoritmos de criptografia, a sistemas operacionais e a APIs. O modelo específico implantado oferece um desempenho excelente, com 10 mil operações por segundo para RSA-2048. Ele dá suporte a 10 partições que podem ser usadas para instâncias de aplicativo exclusivas. Ele é um dispositivo de baixa latência, alta capacidade e com alta taxa de transferência.

### <a name="unique-cloud-based-offering"></a>Oferta exclusiva com base em nuvem

A Microsoft percebeu uma necessidade específica de um conjunto exclusivo de clientes e é a única provedora de nuvem que oferece um serviço HSM dedicado com certificação FIPS 140-2 Nível 3 e oferece uma ampla gama de integração de aplicativos locais e baseados em nuvem.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>O HSM Dedicado do Azure é a opção certa para você?

O HSM Dedicado do Azure é um serviço especializado que atende a requisitos particulares de um tipo específico de organização de grande porte. Como resultado, espera-se que a maior parte dos clientes do Azure não se encaixe no perfil de uso do serviço. Muitos concluirão que o serviço Azure Key Vault é mais apropriado e mais econômico. Para ajudá-lo a decidir o que é melhor para suas necessidades, identificamos os critérios a seguir.

### <a name="best-fit"></a>Mais adequado

Mais adequado para cenários de "lift-and-shift" que exigem acesso direto e exclusivo a dispositivos HSM. Os exemplos incluem:

- Migração de aplicativos locais para Máquinas Virtuais do Azure
- Migração de aplicativos do Amazon AWS EC2 para Máquinas Virtuais do Azure que usam o serviço clássico de HSM AWS Cloud (a Amazon não está oferecendo esse serviço para novos clientes)
- Execução de software compactado em Máquinas Virtuais do Azure, como Apache/Ngnix SSL Offload, Oracle TDE e ADCS

### <a name="not-a-fit"></a>Inadequado

Os serviços de nuvem da Microsoft que dão suporte à criptografia com chaves gerenciadas pelo cliente (por exemplo, Proteção de Informações do Azure, Azure Disk Encryption, Azure Data Lake Store, Armazenamento do Azure, SQL Azure, Chave de Cliente do Office 365) não estão integrados ao HSM Dedicado do Azure.

### <a name="it-depends"></a>Depende

Muitos cenários dependem de uma combinação possivelmente complexa de requisitos e se eles podem ser contornados de alguma forma ou não. Um exemplo é o requisito do padrão FIPS 140-2 Nível 3, que geralmente é obrigatório e, portanto, o HSM Dedicado é a única opção atualmente.  Se esses requisitos obrigatórios não forem relevantes, a escolha normalmente será entre o Azure Key Vault e o HSM Dedicado com base na avaliação de uma combinação de requisitos. Os exemplos incluem:

- Novo código em execução na Máquina Virtual Azure do cliente
- TDE do SQL Server em uma Máquina Virtual do Azure
- Criptografia do Armazenamento do Azure no lado do cliente
- SQL Server e Banco de Dados SQL do Azure Always Encrypted

## <a name="next-steps"></a>Próximas etapas

Considerando a natureza altamente especializada desse serviço, é recomendável que alguns dos principais conceitos encontrados neste conjunto de documentação sejam totalmente compreendidos, bem como os preços, o suporte e os contratos de nível de serviço; um tutorial está disponível para facilitar o provisionamento de HSMs em um ambiente de rede virtual existente. Os [Guias de integração da Gemalto](https://safenet.gemalto.com/partners/microsoft/) e os guias de instruções para decidir a arquitetura de implantação também são ótimos recursos.

* [Alta disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Rede](networking.md)
* [Capacidade de suporte](supportability.md)
* [Monitoramento](monitoring.md)
