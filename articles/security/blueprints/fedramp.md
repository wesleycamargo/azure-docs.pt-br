---
title: "Automação do Azure Blueprint - Aplicativos Web para FedRAMP"
description: "Automação do Azure Blueprint - Aplicativos Web para FedRAMP"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: d0521d68bab8bd0b7db53a512da6d37033abd85e
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="azure-blueprint-automation---web-applications-for-fedramp"></a>Automação do Azure Blueprint - Aplicativos Web para FedRAMP

## <a name="overview"></a>Visão geral

O [FedRAMP (Federal Risk and Authorization Management Program)](https://www.fedramp.gov) é um programa do governo dos EUA que fornece uma abordagem padronizada para avaliação de segurança, autorização e monitoramento contínuo de produtos e serviços de nuvem. Esta Automação do Azure Blueprint - Aplicativos Web para FedRAMP fornece orientação para a implantação de um ambiente de infraestrutura compatível com FedRAMP como um serviço (IaaS) adequado para um aplicativo Web simples voltado para a Internet. Essa solução automatiza a implantação e a configuração de recursos do Azure para uma arquitetura de referência comum, demonstrando maneiras pelas quais os clientes podem atender aos requisitos específicos de segurança e conformidade, e serve como base para os clientes criarem e Configurarem suas próprias soluções no Azure. A solução implementa um subconjunto de controles da linha de base FedRAMP Alta, com base em NIST SP 800-53. Para saber mais sobre os requisitos de FedRAMP Alta e a solução, confira [Requisitos de FedRAMP Alta - Visão geral de alto nível](fedramp-controls-overview.md). ***Observação: a solução é implantada no Azure Governamental.***

Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção. A implantação de um aplicativo nesse ambiente sem modificações não é suficiente para atender aos requisitos da linha de base de FedRAMP Alta totalmente. Observe o seguinte:
- Essa arquitetura fornece uma linha de base para ajudar os clientes a usar o Azure de forma compatível com o FedRAMP.
- Os clientes são responsáveis por realizar as devidas avaliações de segurança e conformidade de qualquer solução criada usando essa arquitetura, já que os requisitos podem variar com base nas particularidades da implementação de cada cliente. 

Para uma visão geral de como essa solução funciona, assista a este [vídeo](https://aka.ms/fedrampblueprintvideo) explicando e demonstrando sua implantação.

Clique [aqui](https://aka.ms/fedrampblueprintrepo) para obter instruções de implantação.

## <a name="solution-components"></a>Componentes da solução

Esta Automação do Azure Blueprint implanta automaticamente uma arquitetura de referência do aplicativo Web IaaS com controles de segurança pré-configurados para que os clientes fiquem em conformidade com os requisitos de FedRAMP. A solução consiste em modelos do Azure Resource Manager e scripts do PowerShell que guiam a implantação e a configuração dos recursos. É fornecida a [documentação de conformidade](#compliance-documentation) com o Azure Blueprint é fornecida, indicando a herança de controles de segurança do Azure e recursos e configurações implantadas que estão de acordo com controles de segurança de NIST SP800-53, assim permitindo que as organizações agilizem as obrigações de conformidade.

## <a name="architecture-diagram"></a>Diagrama da arquitetura

A solução implanta uma arquitetura de referência para um aplicativo Web IaaS com um back-end do banco de dados. A arquitetura inclui uma camada da Web, uma camada de dados, a infraestrutura do Active Directory, o gateway de aplicativo e o balanceador de carga. As máquinas virtuais implantadas nas camadas da Web e de dados são configuradas em um conjunto de disponibilidade, e as instâncias do SQL Server são configuradas em um grupo de disponibilidade AlwaysOn para alta disponibilidade. As máquinas virtuais são associadas ao domínio e as políticas de grupo do Active Directory são usadas para aplicar configurações de conformidade e segurança no nível do sistema operacional. Um jumpbox de gerenciamento (host bastião) fornece conexão segura para os administradores acessarem recursos implantados.

![texto alt](images/fedramp-architectural-diagram.png?raw=true "Automação do Blueprint de aplicativos Web IaaS para ambientes compatíveis com FedRAMP")

A solução usa os serviços do Azure a seguir. Os detalhes da arquitetura de implantação estão localizados na seção [Arquitetura de implantação](#deployment-architecture).

* **Máquinas Virtuais do Azure**
    - (1) gerenciamento/bastião (Windows Server 2016 Datacenter)
    - (2) controlador de domínio do Active Directory (Windows Server 2016 Datacenter)
    - (2) nó de cluster do SQL Server (SQL Server 2016 no Windows Server 2012 R2)
    - (1) testemunha do SQL Server (Windows Server 2016 Datacenter)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
* **Conjuntos de Disponibilidade**
    - (1) controladores de domínio do Active Directory
    - (1) testemunha e nós de cluster SQL
    - (1) Web/IIS
* **Rede Virtual do Azure**
    - (1)/16 redes virtuais
    - (5)/24 sub-redes
    - As configurações de DNS são definidas em ambos os controladores de domínio
* **Balanceador de carga do Azure**
    - (1) balanceador de carga SQL
* **Application Gateway do Azure**
    - (1) Gateway de Aplicativo WAF habilitado
      - Modo de firewall: prevenção
      - Conjunto de regras: OWASP 3.0
      - Ouvinte: Porta 443
* **Armazenamento do Azure**
    - (7) contas de armazenamento com redundância geográfica
* **Serviço de Backup do Azure**
    - (1) cofre dos Serviços de Recuperação
* **Cofre da Chave do Azure**
    - (1) Key Vault
* **Active Directory do Azure**
* **Gerenciador de Recursos do Azure**
* **Azure Log Analytics**
* **Automação do Azure**
    - (1) conta de automação
* **Operations Management Suite**
    - (1) espaço de trabalho do OMS

## <a name="deployment-architecture"></a>Arquitetura de implantação

A seção a seguir fornece detalhes sobre os elementos de desenvolvimento e implementação.

### <a name="network-segmentation-and-security"></a>Segmentação de rede e segurança

#### <a name="application-gateway"></a>Gateway de Aplicativo


A arquitetura reduz o risco de vulnerabilidades de segurança usando o Gateway de Aplicativo com WAF (firewall do aplicativo Web) e o conjunto de regras OWASP habilitado. Dentre outros recursos estão:

- [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Habilitar [Descarregamento SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Desabilitar [TLS versões 1.0 e 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall do aplicativo Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo WAF)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3.0

#### <a name="virtual-network"></a>Rede virtual

A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

Esta solução implanta recursos em uma arquitetura com uma sub-rede separada e Web, sub-rede do banco de dados, sub-rede do Active Directory e sub-rede de gerenciamento em uma rede virtual. Sub-redes são separadas logicamente pelas regras do grupo de segurança de rede aplicadas a sub-redes individuais para restringir o tráfego entre as sub-redes para somente o que é necessário para a funcionalidade de gerenciamento e de sistema.

Consulte a configuração dos [Grupos de Segurança de Rede](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) implantada com esta solução. As organizações podem configurar Grupos de Segurança de Rede editando o arquivo acima usando [esta documentação](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) como guia.

Cada uma das camadas de rede tem um NSG (grupo de segurança de rede dedicado):
- 1 NSG para o Gateway de Aplicativo (LBNSG)
- 1 NSG para Jumpbox (MGTNSG)
- 1 NSG para controladores de domínio primário e de backup (ADNSG)
- 1 NSG para SQL Servers e testemunha de compartilhamento de arquivo (SQLNSG)
- 1 NSG para a camada da Web (WEBNSG)

#### <a name="subnets"></a>Sub-redes

Cada sub-rede está associada ao seu NSG correspondente.

### <a name="data-at-rest"></a>Dados em repouso

A arquitetura protege dados em repouso usando várias medidas de criptografia.

#### <a name="azure-storage"></a>Armazenamento do Azure

Para atender aos requisitos de criptografia de dados em repouso, todas as contas de armazenamento usam [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

#### <a name="sql-database"></a>Banco de dados SQL

O Banco de Dados SQL está configurado para usar [TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), que executa criptografia e descriptografia de dados e arquivos de log em tempo real para proteger as informações em repouso. O TDE fornece a garantia de que os dados armazenados não tenham ficado sujeitos a acesso não autorizado. 

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

O Azure Disk Encryption é usado para criptografar discos de máquinas virtuais IaaS Windows. O [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para o sistema operacional e os discos de dados. A solução é integrada ao Azure Key Vault para ajudar a controlar e a gerenciar as chaves de criptografia de disco.

### <a name="logging-and-auditing"></a>Registro em log e auditoria

O [OMS (Operations Management Suite)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) fornece amplo registro em log de atividades do sistema e do usuário, bem como da integridade do sistema. 

- **Logs de atividades:** os [logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas em recursos em sua assinatura.
- **Logs de Diagnóstico: os** [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) são todos os logs emitidos por cada recurso. Esses logs são logs de eventos do sistema Windows, logs de armazenamento do Azure, logs de auditoria do Key Vault e logs de acesso e firewall do Gateway de Aplicativo.
- **Arquivamento de logs:** os logs de atividades do Azure e os logs de diagnóstico podem ser conectados ao Azure Log Analytics para processamento, armazenamento e criação de painéis. A retenção é configurável pelo usuário em até 730 dias para aos requisitos de retenção específicos da organização.

### <a name="secrets-management"></a>Gerenciamento de segredos

A solução usa o Azure Key Vault para gerenciar chaves e segredos.

- O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ajuda a proteger segredos e chaves criptográficas usadas por aplicativos e serviços em nuvem. 
- A solução é integrada ao Azure Key Vault para gerenciar as chaves de criptografia de disco e segredos da máquina virtual IaaS.

### <a name="identity-management"></a>Gerenciamento de identidades

As tecnologias a seguir oferecem recursos de gerenciamento de identidade no ambiente do Azure.
- O [Azure AD (Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft.
- A autenticação para um aplicativo Web implantado pelo cliente pode ser executada usando o Azure AD. Para saber mais, confira [Integrando aplicativos com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- O [RBAC (Controle De Acesso Baseado Em Função) do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite o gerenciamento de acesso detalhado para o Azure. O acesso à assinatura é limitado ao administrador da assinatura, e o acesso aos recursos pode ser limitado com base na função de usuário.
- Uma instância do Active Directory IaaS implantada fornece gerenciamento de identidade no nível do sistema operacional para máquinas virtuais IaaS implantadas.
   
### <a name="compute-resources"></a>Recursos de computação

#### <a name="web-tier"></a>Camada da Web

A solução implanta máquinas virtuais de camada da Web em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Os conjuntos de disponibilidade fazem com que as máquinas virtuais sejam distribuídas entre vários clusters de hardware isolados para melhorar a disponibilidade.

#### <a name="database-tier"></a>Camada de banco de dados

A solução é implantada máquinas virtuais de camada de banco de dados em um conjunto de disponibilidade como um [grupo de disponibilidade AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). O recurso de grupo de disponibilidade AlwaysOn fornece recursos de alta disponibilidade e recuperação de desastre. 

#### <a name="active-directory"></a>Active Directory

Todas as máquinas virtuais implantadas pela solução são associadas ao domínio e as políticas de grupo do Active Directory são usadas para aplicar configurações de conformidade e segurança no nível do sistema operacional. As máquinas virtuais do Active Directory são implantadas em um conjunto de disponibilidade.


#### <a name="jumpbox-bastion-host"></a>Jumpbox (host bastião)

Um jumpbox de gerenciamento (host bastião) fornece conexão segura para os administradores acessarem recursos implantados. O NSG associado à sub-rede de gerenciamento onde se encontra a máquina virtual jumpbox permite conexões apenas na porta TCP 3389 para RDP. 

### <a name="malware-protection"></a>Proteção contra malware

O [antimalware da Microsoft](https://docs.microsoft.com/azure/security/azure-security-antimalware) para Máquinas Virtuais oferece uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados, com alertas configuráveis quando se sabe que software mal-intencionado ou indesejado tenta se instalar ou executar em máquinas virtuais protegidas.

### <a name="patch-management"></a>Gerenciamento de patch

Máquinas virtuais do Windows implantadas por esta Automação do Azure são configuradas por padrão para receberem atualizações automáticas do serviço Windows Update. Essa solução também implanta a solução Automação do Azure OMS por meio da qual implantações de atualizações podem ser criadas para implantar patches em servidores Windows quando necessário.

### <a name="operations-management"></a>Gerenciamento de operações

#### <a name="log-analytics"></a>Log Analytics

O [Log Analytics](https://azure.microsoft.com/services/log-analytics/) é um serviço do OMS (Operations Management Suite) que permite a coleta e a análise dos dados gerados pelos recursos no Azure e em seus ambientes locais.

#### <a name="oms-solutions"></a>Soluções do OMS

As seguintes soluções OMS são instaladas previamente como parte da solução:
- [Avaliação do AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)
- [Avaliação antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)
- [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)
- [Segurança e Auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)
- [Gerenciamento de atualizações](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)
- [Integridade do Agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)
- [Logs de atividades do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)
- [Controle de alterações](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)

## <a name="compliance-documentation"></a>Documentação de conformidade

### <a name="customer-responsibility-matrix"></a>Matriz de responsabilidades do cliente

A [Matriz de Responsabilidades do Cliente](https://aka.ms/blueprinthighcrm) (pasta de trabalho do Excel) lista todos os controles de segurança exigidos pela linha de base FedRAMP Alta. A matriz indica, para cada controle (ou subitem de controle), se a implementação com responsabilidade sobre o controle é de responsabilidade da Microsoft, do cliente ou compartilhada entre os dois. 

### <a name="control-implementation-matrix"></a>Matriz de implementação de controles

A [matriz de implementação de controles](https://aka.ms/blueprintwacim) (pasta de trabalho do Excel) lista todos os controles de segurança exigidos pela linha de base FedRAMP Alta. A matriz indica, para cada controle (ou subitem controle) que recebe uma responsabilidade de cliente na matriz de responsabilidades de cliente, 1) se a Automação do Blueprint implementa o controle e 2) uma descrição de como a implementação se alinha com o requisitos de controle. Este conteúdo também está disponível [aqui](fedramp-controls-overview.md).

## <a name="deploy-the-solution"></a>Implantar a solução

Esta solução Azure Blueprint é composta de arquivos de configuração JSON e scripts do PowerShell tratados pelo serviço de API do Azure Resource Manager para implantar recursos no Azure. As instruções detalhadas de implantação estão disponíveis [aqui](https://aka.ms/fedrampblueprintrepo). ***Observação: a solução é implantada no Azure Governamental.***

#### <a name="quickstart"></a>Início rápido
1. Clone ou baixe [este](https://aka.ms/fedrampblueprintrepo) repositório GitHub na sua estação de trabalho local.

2. Execute o script de pré-implantação do PowerShell: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Clique no botão abaixo, entre no portal do Azure, insira os parâmetros de modelo do ARM necessários e clique em **Comprar**.

    [![Implantar no Azure](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="disclaimer"></a>Isenção de responsabilidade

- Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.  
- Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.  
- Os clientes podem copiar e usar este documento para fins de consulta interna.  
- Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.  
- Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
- Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
