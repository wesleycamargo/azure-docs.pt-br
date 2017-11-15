---
title: "Exemplos de modelos de política | Microsoft Docs"
description: Exemplos de JSON para o Azure Policy
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 10/30/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: bb0d996b950a31e3eaaaa9d90a6b96617b58192f
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2017
---
# <a name="templates-for-azure-policy"></a>Modelos para o Azure Policy

A tabela a seguir contém links para modelos json do Azure Policy.

| | |
|---|---|
|**Computação**||
| [Imagens de VM aprovadas](scripts/allowed-custom-images.md) | Exige que apenas imagens personalizadas aprovadas sejam implantadas no seu ambiente. Especifique uma matriz de IDs de imagens aprovadas. |
| [Criar VM usando o Disco Gerenciado](scripts/create-vm-managed-disk.md) | Audita quando é criada uma máquina virtual e não usa discos gerenciados.|
| [Auditar se a extensão não existir](scripts/audit-ext-not-exist.md) | Audita se uma extensão não estiver implantada com uma máquina virtual. Especifique o distribuidor da extensão e o tipo para verificar se ela foi implantada. |
| [Permitir imagem de VM personalizada de um Grupo de Recursos](scripts/allow-custom-vm-image.md) |  Requer que imagens personalizadas venha de um grupo de recursos aprovado. Especifique o nome do grupo de recursos aprovado. |
| [Negar benefício de uso híbrido](scripts/deny-hybrid-use.md) | Proíbe o uso do AHUB (Benefício Híbrido do Azure). Use quando você não desejar permitir o uso local de licenças. |
| [Extensões de VM não permitidas](scripts/not-allowed-vm-ext.md) | Proíbe o uso de extensões especificadas. Especifique uma matriz que contém os tipos de extensão proibidos. |
| [Permitir somente determinada imagem de plataforma de VM](scripts/allow-certain-vm-image.md) | Exige que máquinas virtuais usem uma versão específica do UbuntuServer. |
| [Criar VM usando o Disco Gerenciado](scripts/use-managed-disk-vm.md) | Exige que as máquinas virtuais usem discos gerenciados.|
|**Monitoramento**||
| [Configuração de diagnóstico de auditoria](scripts/audit-diag-setting.md) | Auditará se as configurações de diagnóstico não estiverem habilitadas para tipos de recursos especificados. Você especifica uma matriz de tipos de recursos para verificar se as configurações de diagnóstico estão habilitadas. |
|**Rede**||
| [SKUs de Gateway de Aplicativo aprovadas](scripts/allowed-app-gate-sku.md) | Exige que os gateways de aplicativo usem uma SKU aprovada. Especifique uma matriz de SKUs aprovados. |
| [Auditar se o Observador de Rede não está habilitado para a região](scripts/net-watch-not-enabled.md) | Auditará se o observador de rede não estiver habilitado para uma região especificada. Especifique o nome da região para verificar se o observador de rede está habilitado. |
| [NSG X em cada NIC](scripts/nsg-on-nic.md) | Exige que um grupo de segurança de rede específico seja usado com cada adaptador de rede virtual. Você especifica a ID do grupo de segurança de rede a ser usada. |
| [NSG X em cada sub-rede](scripts/nsg-on-subnet.md) | Exige que um grupo de segurança de rede específico seja usado em cada sub-rede virtual. Você especifica a ID do grupo de segurança de rede a ser usada. |
| [Largura de banda do Express Route permitida](scripts/allowed-er-band.md) | Exige que express routes usem um conjunto especificado de larguras de banda. Especifique uma matriz de SKUs que podem ser definidas para o Express Route. |
| [Local de Emparelhamento Permitido para Express Route](scripts/allowed-peering-er.md) | Exige que os Express Routes usem locais de emparelhamentos especificados. Especifique uma matriz de locais de emparelhamentos permitidos. |
| [SKUs de Express Route permitidas](scripts/allowed-er-skus.md) | Exige que os Express Routes usem uma SKU aprovada. Especifique uma matriz de SKUs permitidos. |
| [SKUs do Load Balancer permitidas](scripts/allowed-lb-skus.md) | Exige que os balanceadores de carga usem uma SKU aprovada. Especifique uma matriz de SKUs permitidos. |
| [Nenhum emparelhamento de rede para a rede ER](scripts/no-peering-er-net.md) | Proíbe associar um emparelhamento de rede a uma rede em um grupo de recursos especificado. Use para impedir a conexão à infraestrutura de rede gerenciada central. Especifique o nome do grupo de recursos para impedir a associação. |
| [Nenhuma Tabela de Rotas Definida pelo Usuário](scripts/no-user-def-route-table.md)  |Proíbe que redes virtuais sejam implantadas com uma tabela de rotas definida pelo usuário. |
| [SKUs de Gateway de Rede Virtual Aprovadas](scripts/no-user-def-route-table.md) | Exige que gateways de rede virtual usem um tipo de gateway e SKU aprovado. Especifique uma matriz de SKUs aprovadas e uma matriz de tipos de gateway aprovados. |
| [Usar sub-rede aprovada para adaptadores de rede de VM](scripts/use-approved-subnet-vm-nics.md) | Exige que os adaptadores de rede usem uma sub-rede aprovada. Especifique a ID da sub-rede aprovada. |
| [Usar vNet aprovada para adaptadores de rede de VM](scripts/use-approved-vnet-vm-nics.md) | Exige que os adaptadores de rede usem uma rede virtual aprovada. Especifique a ID da rede virtual aprovada. |
|**Marcas**||
| [Iniciativa de Política de Marcas de Cobrança](scripts/billing-tags-policy-init.md) | Exige valores de marca especificados para o nome do produto e o centro de custo. Usa políticas internas para aplicar e impor as marcas necessárias. Especifique os valores necessários para as marcas.  |
| [Impor marca e seu valor em grupos de recursos](scripts/enforce-tag-rg.md) | Exige uma marca e um valor em um grupo de recursos. Especifique o nome e o valor da marca exigidos.  |
|**SQL**||
| [Auditar a Configuração de Auditoria no Nível do BD SQL](scripts/audit-sql-db-audit-setting.md) | Audita as configurações de auditoria de banco de dados SQL se essas configurações não corresponderem a uma configuração especificada. Especifique um valor que indica se as configurações de auditoria devem ser habilitadas ou desabilitadas.  |
| [Auditar o status da Transparent Data Encryption](scripts/audit-trans-data-enc-status.md) | Auditará a Transparent Data Encryption do banco de dados SQL se ela não estiver habilitada.  |
| [Auditar a configuração de detecção de ameaças no nível do BD](scripts/audit-db-threat-det-setting.md) | Faz a auditoria de políticas de alerta de segurança do banco de dados SQL se essas políticas não estiverem definidas para o estado especificado. Especifique um valor que indica se a detecção de ameaças está habilitada ou desabilitada.  |
| [Auditar a Configuração da Auditoria no Nível do SQL Server](scripts/audit-sql-ser-leve-audit-setting.md) | Auditará as configurações de auditoria do SQL Server se elas não corresponderem a uma configuração especificada. Especifique um valor que indica se as configurações de auditoria devem ser habilitadas ou desabilitadas. |
| [Auditar a configuração de detecção de ameaças no nível do servidor](scripts/audit-sql-ser-threat-det-setting.md) | Faz a auditoria de políticas de alerta de segurança do banco de dados SQL se essas políticas não estiverem definidas para o estado especificado. Especifique um valor que indica se a detecção de ameaças está habilitada ou desabilitada.  |
| [SKUs do BD SQL permitidas](scripts/allowed-sql-db-skus.md) | Exige que bancos de dados SQL usem uma SKU aprovada. Especifique uma matriz de IDs de SKU permitidas ou uma matriz de nomes de SKU permitidos. |
|**Armazenamento**||
| [SKUs permitidas para Contas de Armazenamento e Máquinas Virtuais](scripts/allowed-skus-storage.md) | Exige que contas de armazenamento e máquinas virtuais usem SKUs aprovadas. Usa as políticas internas para garantir SKUs aprovadas. Especifique uma matriz de SKUs de máquinas virtuais aprovadas e uma matriz de SKUs de conta de armazenamento aprovadas. |
| [Assegurar tráfego https somente para contas de armazenamento](scripts/ensure-https-stor-acct.md) | Exige que contas de armazenamento usem tráfego HTTPS.  |
| [Negar camadas de acesso esporádico para contas de armazenamento](scripts/deny-cool-access-tiering.md) | Proíbe o uso de camadas de acesso esporádico para contas de armazenamento de blobs.  |
| [Assegurar a criptografia de arquivo de armazenamento](scripts/ensure-store-file-enc.md) | Exige que a criptografia de arquivo esteja habilitada para contas de armazenamento.  |
|**Política interna**||
| [Locais permitidos](scripts/allowed-locs.md) | Exige que todos os recursos sejam implantados em locais aprovados. Especifique uma matriz de locais aprovados.  |
| [Tipos de recursos permitidos](scripts/allowed-res-types.md) | Assegura que apenas os tipos de recurso aprovados sejam implantados. Especifique uma matriz de tipos de recursos permitidos.  |
| [SKUs de conta de armazenamento permitidas](scripts/allowed-stor-acct-skus.md) | Exige que contas de armazenamento usem uma SKU aprovada. Especifique uma matriz de SKUs aprovados. |
| [Aplicar marca e seu valor padrão](scripts/apply-tag-def-val.md) | Acrescentará um nome e um valor de marca especificados se a marca não for indicada. Especifique o nome e o valor da marca a serem aplicados.  |
| [Impor marca e seu valor](scripts/enforce-tag-val.md) | Exige um nome e um valor de marca especificados. Especifique o nome e o valor da marca a serem impostos.  |
| [Tipos de recursos não permitidos](scripts/not-allowed-res-type.md) | Proíbe a implantação dos tipos de recursos especificados. Especifique uma matriz de tipos de recursos a ser bloqueada.  |
| [Exigir SQL Server versão 12.0](scripts/req-sql-12.md) | Exige que servidores SQL usem a versão 12.0.  |
| [Exigir criptografia de conta de armazenamento](scripts/req-store-acct-enc.md) | Exige que a conta de armazenamento use criptografia de blob.  |
