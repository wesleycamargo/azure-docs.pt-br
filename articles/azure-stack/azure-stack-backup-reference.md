---
title: "Referência de serviço de Backup de infraestrutura de pilha do Azure | Microsoft Docs"
description: "Este artigo contém o material de referência para o serviço de Backup do Azure pilha de infraestrutura."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 4e6e0a52b2c55239e38757223f54e5e94dc98c42
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="infrastructure-backup-service-reference"></a>Referência da infraestrutura de serviço de Backup

## <a name="azure-backup-infrastructure"></a>Infraestrutura de backup do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Pilha do Azure consiste em vários serviços que compõem o portal, o Gerenciador de recursos do Azure, e a experiência de gerenciamento da infraestrutura. A experiência de gerenciamento de tipo de ferramenta da pilha do Azure se concentra na redução da complexidade exposta para o operador da solução.

Backup da infra-estrutura foi projetado para internalizar a complexidade do backup e restaurando dados para serviços de infraestrutura, garantir operadores pode se concentrar na solução de gerenciamento e manutenção de um SLA para os usuários.

Exportar os dados de backup para um compartilhamento externo é necessária para evitar o armazenamento de backups no mesmo sistema. Necessidade de um compartilhamento externo dá ao administrador a flexibilidade para determinar onde armazenar os dados com base nas políticas de continuidade de negócios/recuperação de desastres existentes da empresa. 

### <a name="infrastructure-backup-components"></a>Componentes de Backup de infraestrutura

Backup da infra-estrutura inclui os seguintes componentes:

 - **Controlador de Backup de infraestrutura**  
 O controlador de infraestrutura de Backup é instanciado com e residem em cada nuvem de pilha do Azure.
 - **Provedor de recursos de backup**  
 O provedor de recursos de Backup (Backup RP) é composto do usuário interface e aplicativo programa APIs (interfaces) s expondo a funcionalidade básica de backup para a infraestrutura de pilha do Azure.

#### <a name="infrastructure-backup-controller"></a>Controlador de Backup de infraestrutura

O controlador de infraestrutura de Backup é um serviço é instanciado para uma nuvem de pilha do Azure do Service Fabric. Recursos de backup são criados em um nível e a captura de serviço específico de região de dados regionais do Gerenciador de recursos do AD, Canadá, Azure, CRP, SRP, NRP, KeyVault, RBAC. 

### <a name="backup-resource-provider"></a>Provedor de recursos de backup

O provedor de recursos de Backup apresenta a interface de usuário no portal do Azure pilha de configuração básica e lista de recursos de backup. Operador pode executar as seguintes operações na interface do usuário:

 - Habilitar o backup pela primeira vez, fornecendo o local de armazenamento externo, credenciais e chave de criptografia
 - Modo de exibição concluído criado recursos de backup e recursos de status em criação
 - Modificar o local de armazenamento onde o Backup do controlador coloca os dados de backup
 - Modificar as credenciais que o controlador de Backup usa para acessar o local de armazenamento externo
 - Modificar a chave de criptografia que usa o controlador de Backup para criptografar backups 


## <a name="backup-controller-requirements"></a>Requisitos de backup de controlador

Esta seção descreve os requisitos importantes para o Backup de infraestrutura. É recomendável que você examine as informações cuidadosamente antes de habilitar o backup para a instância de pilha do Azure e, em seguida, referência conforme necessário durante a implantação e operação subsequente.

Os requisitos incluem:

  - **Requisitos de software** – descreve os locais de armazenamento com suporte e diretrizes de dimensionamento. 
  - **Requisitos de rede** – descreve os requisitos de rede para locais de armazenamento diferentes.  

### <a name="software-requirements"></a>Requisitos de software

#### <a name="supported-storage-locations"></a>Locais de armazenamento com suporte

| Local de armazenamento                                                                 | Detalhes                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Compartilhamento de arquivos SMB hospedado em um dispositivo de armazenamento no ambiente de rede confiável | Compartilhamento SMB no mesmo data center em que a pilha do Azure é implantada ou em um datacenter diferente. Várias instâncias de pilha do Azure podem usar o mesmo compartilhamento de arquivo. |
| Compartilhamento de arquivos SMB no Azure                                                          | Não há suporte no momento.                                                                                                                                 |
| Armazenamento de blob no Azure                                                            | Não há suporte no momento.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Versões com suporte do SMB

| SMB | Versão |
|-----|---------|
| SMB | 3.x     |

#### <a name="storage-location-sizing"></a>Dimensionamento de local de armazenamento 

Controlador de infraestrutura de Backup fará backup de dados sob demanda. A recomendação é fazer backup de pelo menos duas vezes por dia e manter no máximo sete dias de backups. 

| Escala do ambiente | Tamanho projetado do backup | Quantidade total de espaço necessário |
|-------------------|--------------------------|--------------------------------|
| 12 de 4 nós        | 10 GB                     | 140 GB                          |

### <a name="network-requirements"></a>Requisitos de rede
| Local de armazenamento                                                                 | Detalhes                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Compartilhamento de arquivos SMB hospedado em um dispositivo de armazenamento no ambiente de rede confiável | A porta 445 é necessária se a instância do Azure pilha reside em um ambiente protegido por firewall. Controlador de infraestrutura de Backup irá iniciar uma conexão ao servidor de arquivos SMB pela porta 445. |
| Para usar o FQDN do servidor de arquivos, o nome deve ser resolvido por meio do PEP             |                                                                                                                                                                                         |

> [!Note]  
> Nenhuma porta de entrada precisa ser abertas.


## <a name="infrastructure-backup-limits"></a>Limites de Backup de infraestrutura

Considere esses limites, como planejar, implantar e operar suas instâncias de pilha do Microsoft Azure. A tabela a seguir descreve esses limites.

### <a name="infrastructure-backup-limits"></a>Limites de Backup de infraestrutura
| Identificador de limite                                                 | Limite        | Comentários                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Tipo de backup                                                      | Somente completo    | Controlador de infraestrutura de Backup só oferece suporte a backups completos. Não há suporte para backups incrementais.                                          |
| Backups agendados                                                | Somente manual  | Controlador de backup atualmente suporta apenas backups sob demanda                                                                                 |
| Trabalhos de backup simultâneos máximo                                   | 1            | Somente um trabalho de backup ativo é suportado por instância do controlador de Backup.                                                                  |
| Configuração do comutador de rede                                     | Não está no escopo | Administrador deve fazer backup de configuração de comutador de rede usando as ferramentas de OEM. Consulte a documentação para o Azure pilha cada fornecedor OEM. |
| Host de ciclo de vida de hardware                                          | Não está no escopo | Administrador faça backup do Host de ciclo de vida de Hardware usando as ferramentas de OEM. Consulte a documentação para o Azure pilha cada fornecedor OEM.      |
| Número máximo de compartilhamentos de arquivos                                    | 1            | Compartilhamento de arquivos apenas uma pode ser usado para armazenar dados de backup                                                                                        |
| Serviços de aplicativos, função, SQL, mysql dados de provedor de recursos de backup | Não está no escopo | Consulte a orientação publicada para a implantação e gerenciamento de valor agregado RPs criado pela Microsoft.                                                  |
| Provedores de recursos de backup de terceiros                              | Não está no escopo | Consulte a orientação publicada para a implantação e gerenciamento de valor agregado RPs criado por fornecedores de terceiros.                                          |

## <a name="next-steps"></a>Próximas etapas

 - Para saber mais sobre a infraestrutura de serviço de Backup, consulte [Backup e recuperação de dados para a pilha do Azure com o serviço de Backup de infraestrutura](azure-stack-backup-infrastructure-backup.md).