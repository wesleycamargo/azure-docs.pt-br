---
title: Referência de serviço de Backup de infraestrutura de pilha do Azure | Microsoft Docs
description: Este artigo contém o material de referência para o serviço de Backup de infraestrutura do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/20/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 608f3043e0e4b851820274ca743cbc44d1c8c0f1
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242896"
---
# <a name="infrastructure-backup-service-reference"></a>Referência de serviço de Backup de infraestrutura

## <a name="azure-backup-infrastructure"></a>Infraestrutura de backup do Azure

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O Azure Stack consiste em vários serviços que compõem o portal, o Azure Resource Manager, e a experiência de gerenciamento da infraestrutura. A experiência de gerenciamento de tipo de ferramenta do Azure Stack se concentra na redução da complexidade exposta para o operador da solução.

Backup de infraestrutura é projetado para internalizar a complexidade de fazer backup e restaurar dados para serviços de infraestrutura, garantir que os operadores pode focar a solução de gerenciamento e manutenção de um SLA para os usuários.

Exportando os dados de backup para um compartilhamento externo é necessário para evitar o armazenamento de backups no mesmo sistema. Exigir que um compartilhamento externo fornece ao administrador a flexibilidade para determinar onde armazenar os dados com base nas políticas de recuperação de Desastre/continuidade de negócios da empresa existentes. 

### <a name="infrastructure-backup-components"></a>Componentes do Backup de infraestrutura

Backup de infraestrutura inclui os seguintes componentes:

 - **Controlador de Backup de infraestrutura**  
 O controlador de infraestrutura de Backup é instanciado com e reside em cada nuvem do Azure Stack.
 - **Provedor de recursos de backup**  
 O provedor de recursos de Backup (Backup RP) é composto do usuário interface e aplicativo programa APIs (interfaces) s expondo a funcionalidade básica de backup para infraestrutura do Azure Stack.

#### <a name="infrastructure-backup-controller"></a>Controlador de Backup de infraestrutura

O controlador de infraestrutura de Backup é um Service Fabric serviço é instanciado para uma nuvem do Azure Stack. Recursos de backup são criados em uma captura e nível de serviço específica da região dados regionais do AD, a autoridade de certificação, Azure Resource Manager, CRP, SRP, NRP, KeyVault, RBAC. 

### <a name="backup-resource-provider"></a>Provedor de recursos de backup

O provedor de recursos de Backup apresenta a interface do usuário no portal do Azure Stack para a configuração básica e a lista de recursos de backup. Operador pode realizar as seguintes operações na interface do usuário:

 - Habilitar o backup pela primeira vez, fornecendo o local de armazenamento externo, credenciais e chave de criptografia
 - Modo de exibição concluído criado os recursos de backup e recursos de status em criação
 - Modifique o local de armazenamento onde o Backup do controlador coloca os dados de backup
 - Modificar as credenciais usadas pelo Backup do controlador para acessar o local de armazenamento externo
 - Modificar a chave de criptografia que usa o controlador de Backup para criptografar backups 


## <a name="backup-controller-requirements"></a>Requisitos do controlador de backup

Esta seção descreve os requisitos importantes para o Backup de infraestrutura. É recomendável que você examine as informações com atenção antes de habilitar o backup para sua instância do Azure Stack e, em seguida, voltar a ela conforme necessário durante a implantação e operação subsequente.

Os requisitos incluem:

  - **Requisitos de software** – descreve os locais de armazenamento com suporte e diretrizes de dimensionamento. 
  - **Requisitos de rede** – descreve os requisitos de rede para locais de armazenamento diferentes.  

### <a name="software-requirements"></a>Requisitos de software

#### <a name="supported-storage-locations"></a>Locais de armazenamento compatíveis

| Local de armazenamento                                                                 | Detalhes                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Compartilhamento de arquivos SMB hospedado em um dispositivo de armazenamento dentro do ambiente de rede confiável | Compartilhamento SMB no mesmo datacenter onde o Azure Stack é implantado ou em um datacenter diferente. Várias instâncias do Azure Stack podem usar o mesmo compartilhamento de arquivos. |
| Compartilhamento de arquivos SMB no Azure                                                          | Não há suporte no momento.                                                                                                                                 |
| Armazenamento de BLOBs do Azure                                                            | Não há suporte no momento.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Versões com suporte do SMB

| SMB | Versão |
|-----|---------|
| SMB | 3.x     |

#### <a name="storage-location-sizing"></a>Dimensionamento do armazenamento local 

Controlador de infraestrutura de Backup fará backup de dados sob demanda. A recomendação é fazer backup de pelo menos duas vezes por dia e manter no máximo sete dias de backups. 

| Escala do ambiente | Tamanho projetado do backup | Quantidade total de espaço necessário |
|-------------------|--------------------------|--------------------------------|
| 12 de 4 nós        | 10 GB                     | 140 GB                          |

### <a name="network-requirements"></a>Requisitos de rede
| Local de armazenamento                                                                 | Detalhes                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Compartilhamento de arquivos SMB hospedado em um dispositivo de armazenamento dentro do ambiente de rede confiável | A porta 445 será necessária se a instância do Azure Stack reside em um ambiente protegido por firewall. Controlador de infraestrutura de Backup iniciará uma conexão ao servidor de arquivos SMB na porta 445. |
| Para usar o FQDN do servidor de arquivos, o nome deve ser resolvível do PEP             |                                                                                                                                                                                         |

> [!Note]  
> Nenhuma porta de entrada precisa ser abertas.


## <a name="infrastructure-backup-limits"></a>Limites de Backup de infraestrutura

Considere esses limites, como planejar, implantar e operar suas instâncias do Microsoft Azure Stack. A tabela a seguir descreve esses limites.

### <a name="infrastructure-backup-limits"></a>Limites de infraestrutura Backup
| Identificador de limite                                                 | Limite        | Comentários                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Tipo de backup                                                      | Somente completo    | Controlador de infraestrutura de Backup só oferece suporte a backups completos. Não há suporte para backups incrementais.                                          |
| Backups agendados                                                | Apenas manual  | Controlador de backup atualmente suporta apenas backups sob demanda                                                                                 |
| Máximo de trabalhos de backup simultâneos                                   | 1            | Apenas um trabalho de backup ativo é tem suporte por instância do controlador de Backup.                                                                  |
| Configuração do comutador de rede                                     | Não está no escopo | Administrador deve fazer backup de configuração de comutador de rede usando as ferramentas de OEM. Consulte a documentação para o Azure Stack fornecidos por cada fornecedor OEM. |
| Host de ciclo de vida do hardware                                          | Não está no escopo | Administrador deve fazer backup de Host do ciclo de vida do Hardware usando ferramentas de OEM. Consulte a documentação para o Azure Stack fornecidos por cada fornecedor OEM.      |
| Número máximo de compartilhamentos de arquivos                                    | 1            | Compartilhamento de arquivos apenas uma pode ser usado para armazenar dados de backup                                                                                        |
| Fazer backup de serviços de aplicativos, função, SQL, dados de provedor de recursos do mysql | Não está no escopo | Veja as orientações publicadas para a implantação e gerenciamento de valor agregado RPs criado pela Microsoft.                                                  |
| Provedores de recursos de terceiros de backup                              | Não está no escopo | Veja as orientações publicadas para a implantação e gerenciamento de valor agregado RPs criado por fornecedores de terceiros.                                          |

## <a name="next-steps"></a>Próximas etapas

 - Para saber mais sobre o serviço de Backup de infraestrutura, consulte [Backup e recuperação de dados para o Azure Stack com o serviço de Backup de infraestrutura](azure-stack-backup-infrastructure-backup.md).