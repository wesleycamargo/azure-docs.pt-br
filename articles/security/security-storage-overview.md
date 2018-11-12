---
title: Recursos de segurança que podem ser usados com o Armazenamento do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos recursos de segurança centrais do Azure que podem ser usados com o Armazenamento do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 63967face89c12f6769d397bd5be78c23bc9f851
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259132"
---
# <a name="azure-storage-security-overview"></a>Visão geral de segurança do Armazenamento do Azure

O Armazenamento do Azure é a solução de armazenamento em nuvem para aplicativos modernos que dependem de durabilidade, disponibilidade e escalabilidade para atender às necessidades dos clientes. O Armazenamento do Azure fornece um conjunto abrangente de funcionalidades de segurança. Você pode:

* Proteger a conta de armazenamento usando o Controle de Acesso Baseado em Função (RBAC) e o Azure Active Directory.
* Proteja os dados em trânsito entre um aplicativo e o Azure usando a criptografia do cliente, HTTPS ou SMB 3.0.
* Os dados definidos podem ser criptografados automaticamente quando gravados no Armazenamento do Azure usando a Criptografia do Serviço de Armazenamento.
* Defina os discos do sistema operacional e de dados usados pelas máquinas virtuais (VMs) para serem criptografados usando o Azure Disk Encryption.
* Conceda acesso delegado aos objetos de dados no Armazenamento do Microsoft Azure usando assinaturas de acesso compartilhado (SASs).
* Use a análise para controlar o método de autenticação que alguém está usando ao acessar o Armazenamento.

Para obter uma visão mais detalhada sobre a segurança no Armazenamento do Azure, confira o [Guia de segurança do Armazenamento do Azure](../storage/common/storage-security-guide.md). Este guia aborda detalhadamente os recursos de segurança do Armazenamento do Azure. Esses recursos incluem chaves de conta de armazenamento, criptografia de dados em trânsito e em repouso e análise de armazenamento.


Este artigo fornece uma visão geral dos recursos de segurança do Azure que podem ser usados com o Armazenamento do Azure. Os links para artigos fornecem detalhes de cada recurso para que você saiba mais sobre eles.

## <a name="role-based-access-control"></a>Controle de Acesso Baseado em Função

Você pode ajudar a proteger a conta de armazenamento usando o Controle de Acesso Baseado em Função. Restringir o acesso com base nos princípios de segurança de [divulgação restrita àqueles diretamente interessados](https://en.wikipedia.org/wiki/Need_to_know) e no [privilégio mínimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege) é fundamental para as organizações que desejam impor políticas de segurança para acesso a dados. Esses direitos de acesso são concedidos atribuindo a função de RBAC apropriada a grupos e aplicativos em determinado escopo. Você pode usar [funções RBAC internas](../role-based-access-control/built-in-roles.md), como Colaborador da Conta de Armazenamento, para atribuir privilégios aos usuários.

Saiba mais:

* [Controle de Acesso Baseado em Função do Azure Active Directory](../role-based-access-control/role-assignments-portal.md)

## <a name="delegated-access-to-storage-objects"></a>Acesso delegado aos objetos de armazenamento

Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento. A SAS significa que você pode conceder a um cliente permissões limitadas para objetos em sua conta de armazenamento por determinado período e com um conjunto específico de permissões. Você pode conceder essas permissões limitadas sem precisar compartilhar as chaves de acesso da conta. 

A SAS é um URI que engloba em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a SAS, o cliente só precisa fornecer a SAS ao construtor apropriado ou ao método apropriado.

Saiba mais:

* [Noções básicas sobre o modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Criar e usar um SAS com o armazenamento de Blobs](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Criptografia em trânsito

A criptografia em trânsito é um mecanismo de proteção de dados quando eles são transmitidos entre redes. Com o Armazenamento do Azure, você pode proteger dados usando:

* [Criptografia de nível de transporte](../storage/common/storage-security-guide.md#encryption-in-transit), como HTTPS, ao transferir dados dentro ou fora do Armazenamento do Azure.
* [Criptografia na transmissão](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), como a criptografia SMB 3.0, para compartilhamentos de arquivo do Azure.
* [Criptografia do cliente](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), que permite criptografar os dados antes que eles sejam transferidos para o Armazenamento e descriptografá-los após serem transferidos para fora do Armazenamento.

Saiba mais sobre a criptografia do cliente:

* [Client-Side Encryption for Microsoft Azure Storage (Criptografia do cliente para o Armazenamento do Microsoft Azure)](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Cloud security controls series: Encrypting Data in Transit (Série sobre controles de segurança de nuvem: criptografando dados em trânsito)](https://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Criptografia em repouso

Para muitas organizações, a [criptografia de dados em repouso](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é uma etapa obrigatória no sentido de garantir a soberania, a privacidade e a conformidade dos dados. Há três recursos do Azure que fornecem criptografia de dados que estão em repouso:

* [Criptografia do Serviço de Armazenamento](../storage/common/storage-security-guide.md#encryption-at-rest) permite solicitar que o serviço de armazenamento criptografe automaticamente os dados ao gravá-los no Armazenamento do Azure.
* [Criptografia do cliente](../storage/common/storage-security-guide.md#client-side-encryption) também fornece o recurso de criptografia em repouso.
* [Azure Disk Encryption](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) permite criptografar os discos do sistema operacional e os discos de dados usados por uma máquina virtual IaaS.

Saiba mais sobre a Criptografia do Serviço de Armazenamento:

* A [Criptografia do Serviço de Armazenamento do Azure](https://azure.microsoft.com/services/storage/) está disponível para [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/). Para obter detalhes sobre outros tipos de armazenamento do Azure, consulte [Arquivos do Azure](https://azure.microsoft.com/services/storage/files/), [Disco (Armazenamento Premium)](https://azure.microsoft.com/services/storage/premium-storage/), [Armazenamento de tabelas](https://azure.microsoft.com/services/storage/tables/) e [Armazenamento de filas](https://azure.microsoft.com/services/storage/queues/).
* [Criptografia do Serviço de Armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

O Azure Disk Encryption para máquinas virtuais ajuda a abordar os requisitos de conformidade e segurança organizacional. Ele criptografa os discos de VM (incluindo os discos de inicialização e de dados) usando chaves e políticas que você controla no [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

A criptografia de disco para VMs funciona para sistemas operacionais Windows e Linux. Ela também usa o Cofre de Chaves para ajudá-lo a proteger, gerenciar e auditar o uso de suas chaves de criptografia de disco. Todos os dados nos discos de VM são criptografados em repouso usando a tecnologia de criptografia padrão do setor em suas contas de armazenamento do Azure. A solução de Criptografia de Disco para Windows é baseada na [Criptografia de Unidade de Disco BitLocker da Microsoft](https://technet.microsoft.com/library/cc732774.aspx) e a solução para Linux é baseada no [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Saiba mais

* [Azure Disk Encryption for Windows and Linux IaaS Virtual Machines (Azure Disk Encryption para máquinas virtuais IaaS Windows e Linux)](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="firewalls-and-virtual-networks"></a>Firewalls e redes virtuais

O armazenamento do Azure permite que você ative regras de firewall para suas contas de armazenamento. Quando habilitadas, elas bloquearão as solicitações de entrada para dados, incluindo solicitações de outros serviços do Azure. É possível configurar exceções para permitir o tráfego. Regras de firewall podem ser habilitadas em contas de armazenamento existentes ou durante o tempo da criação.

Você deve usar essa funcionalidade para proteger as contas de armazenamento em um conjunto específico de redes permitidas.

Para obter mais informações sobre as redes virtuais e os firewalls do armazenamento do Azure, examine o artigo [Configurar Redes Virtuais e Firewalls do Armazenamento do Microsoft Azure](../storage/common/storage-network-security.md)

## <a name="azure-data-box"></a>Azure Data Box

Os dispositivos Data Box, Data Box Disk e Data Box Heavy ajudam a transferir grandes quantidades de dados para o Azure quando a rede não é uma opção. Esses dispositivos de transferência de dados off-line são enviados entre sua organização e o data center do Azure. Eles usam criptografia AES para ajudar a proteger seus dados em trânsito e passam por um processo completo de sanitização pós-upload para excluir seus dados do dispositivo.

O Data Box Edge e o Data Box Gateway são produtos de transferência de dados on-line que atuam como gateways de armazenamento de rede para gerenciar dados entre seu site e o Azure. O Data Box Edge, um dispositivo de rede local, transfere dados de e para o Azure e usa computação de borda habilitada por inteligência artificial (AI) para processar dados. Gateway de caixa de dados é uma solução de virtualização com recursos de gateway de armazenamento.

Saiba mais:

* [Azure Data Box](https://azure.microsoft.com/services/storage/databox/)
* [Borda da caixa de dados do Azure](../databox-online/data-box-edge-overview.md)
* [Gateway de caixa de dados do Azure](..//databox-online/data-box-gateway-overview.md)

## <a name="advanced-threat-protection"></a>Proteção Avançada contra Ameaças

O Armazenamento do Azure fornece Proteção Avançada contra Ameaças para uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar sua conta de armazenamento. A Proteção Avançada contra Ameaças monitora os logs de diagnóstico do Armazenamento do Azure para solicitações suspeitas de leitura, gravação ou exclusão no armazenamento do Blob. 

Os alertas da Proteção Avançada contra Ameaças podem ser visualizados no [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/). A Central de Segurança do Azure fornece detalhes sobre qualquer atividade suspeita detectada e recomenda ações para investigar e remediar a ameaça em potencial. 

Saiba mais:

* [Armazenamento do Azure avançada de visão geral da proteção de ameaças](../storage/common/storage-advanced-threat-protection.md)

## <a name="azure-key-vault"></a>Cofre da Chave do Azure

O Azure Disk Encryption usa o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para ajudá-lo a controlar e gerenciar os segredos e chaves de criptografia de disco em sua assinatura do Key Vault. Ele também garante que todos os dados nos discos da máquina virtual sejam criptografados em repouso no Armazenamento do Azure. Você deve usar o Cofre de Chaves para auditar as chaves e o uso de políticas.

Saiba mais

* [O que é o Cofre da Chave do Azure?](../key-vault/key-vault-whatis.md)
* [Introdução ao Cofre da Chave do Azure](../key-vault/key-vault-get-started.md)
