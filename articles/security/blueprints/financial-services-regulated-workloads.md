---
title: "Projeto de Segurança e de Conformidade do Azure – cargas de trabalho regulamentadas pelos serviços financeiros FFIEC"
description: "Projeto de Segurança e de Conformidade do Azure – cargas de trabalho regulamentadas pelos serviços financeiros FFIEC"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 17794288-9074-44b5-acc8-1dacceb3f56c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: frasim
ms.openlocfilehash: a1167f56f595f905c6338868806351345c06b91a
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="azure-security-and-compliance-blueprint---ffiec-financial-services-regulated-workloads"></a>Projeto de Segurança e de Conformidade do Azure – cargas de trabalho regulamentadas pelos serviços financeiros FFIEC

## <a name="overview"></a>Visão geral

O Projeto de Segurança e Conformidade do Azure – cargas de trabalho regulamentadas pelos serviços financeiros FFIEC ajuda a implantar um aplicativo Web de PaaS (plataforma como serviço) seguro e em conformidade, projetado para lidar com os dados confidenciais na nuvem. O plano gráfico consiste em scripts automatizados e diretrizes que mostram uma arquitetura de referência simples e um design que ajuda a simplificar a adoção de soluções do Microsoft Azure. Este plano gráfico ilustra uma solução de ponta a ponta para atender às necessidades das organizações que buscam maneiras de reduzir a carga e o custo da implantação na nuvem.

Este plano gráfico foi projetado para atender aos requisitos de padrões de conformidade rigorosos definidos pelo Instituto Americano de Contadores Públicos Certificados como SOC 1, SOC 2, pelo DSS 3.2 do conselho do Payment Card Industry Data Security Standards e pelo FFIEC para coleta, armazenamento e recuperação de dados financeiros confidenciais. Ele demonstra a manipulação correta desses dados implantando uma solução que gerencia dados financeiros em um ambiente seguro, em conformidade e multicamadas. A solução é implantada como uma solução de PaaS baseada no Azure de ponta a ponta. 

O objetivo do plano gráfico é funcionar como base para os clientes criarem e entenderem os requisitos de gerenciamento de dados seguros na nuvem. A solução não deve ser usada em uma implantação de produção no estado em que se encontra, mas para entender, projetar e implantar os serviços do Azure. Ela foi projetada como uma linha de base para ajudar os clientes a usarem o Microsoft Azure de forma segura e em conformidade.

Um auditor autorizado deverá certificar qualquer solução de cliente de produção que seja baseada neste projeto. As soluções podem variar com base nas especificações da implementação de cada cliente e de cada geografia.

Para obter uma visão geral de como essa solução funciona, assista a este [vídeo](https://aka.ms/fsiblueprintvideo) que explica e demonstra sua implantação.

## <a name="solution-components"></a>Componentes da solução

A arquitetura é composta dos seguintes componentes e usa os recursos de implantação da solução de conformidade PCI DSS do Azure.

- **Diagrama de arquitetura**. O diagrama mostra a arquitetura de referência usada para a solução Contoso Webstore.
- **Modelos de implantação** Nessa implantação, os [modelos do Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) são usados para implantar os componentes da arquitetura automaticamente no Microsoft Azure especificando parâmetros de configuração durante a instalação.
- **Scripts de implantação automatizada**. Esses scripts ajudam a implantar a solução de ponta a ponta. Os scripts consistem em:
    - Uma instalação do módulo e um script de configuração de [administrador global](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) são usados para instalar e verificar se os módulos do PowerShell obrigatórios e as funções de administrador global foram configuradas corretamente. 
    - Um script de instalação do PowerShell é usada para implantar a solução completa, fornecida por meio de um arquivo .zip e um arquivo .bacpac que contêm um aplicativo Web de demonstração criado previamente com o [conteúdo de exemplo de banco de dados SQL](https://github.com/Microsoft/azure-sql-security-sample). conteúdo. O código-fonte desta solução está disponível para análise no [repositório de código do plano gráfico de processamento de pagamento][code-repo]. 

## <a name="architectural-diagram"></a>Diagrama de arquitetura

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>Cenário do usuário

O plano gráfico abrange o caso de uso abaixo.

> Esse cenário ilustra como um webstore fictício passou dados confidenciais para uma solução de PaaS em nuvem baseada no Azure. A solução de exemplo ilustra o tratamento e a coleta de informações básicas do usuário e dados confidenciais selecionados. Este trabalho faz uso de elementos dos ambientes do Projeto de Segurança e Conformidade do Azure – ambientes de processamento do pagamento em conformidade com PCI DSS. Para obter mais informações sobre a expansão deste trabalho, o documento ["Review and Guidance for Implementation"](https://aka.ms/pciblueprintprocessingoverview) (Revisão e diretrizes de implementação) fornece uma análise dos ambientes em conformidade com o PCI DSS.

### <a name="use-case"></a>Caso de uso
Uma pequeno webstore chamada *Contoso Webstore* está pronta para passar dados financeiros que incluem informações de pagamento de cliente para a nuvem. 

O administrador da Contoso Webstore está procurando uma solução que possa ser implantada rapidamente para atingir suas metas. Ele usará essa POC (prova de conceito) para discutir com os stakeholders como o Azure pode ser usado para coletar, armazenar e recuperar dados financeiros de acordo com requisitos de conformidade rigorosos.

> Você será responsável por realizar as devidas revisões de segurança e conformidade de qualquer solução criada com a arquitetura usada por essa POC, já que os requisitos podem variar com base nas particularidades da sua implementação e região geográfica. 

### <a name="elements-of-the-foundational-architecture"></a>Elementos da arquitetura fundamental

A arquitetura fundamental é criada com os seguintes elementos fictícios:

Site de domínio`contosowebstore.com`

As funções de usuário são usadas para explicar o caso de uso e fornecer informações sobre a interface do usuário.

#### <a name="role-site-and-subscription-admin"></a>Função: administração de site e assinatura

|item      |Exemplo|
|----------|------|
|Nome de Usuário: |`adminXX@contosowebstore.com`|
| Nome: |`Global Admin Azure PCI Samples`|
|Tipo de usuário:| `Subscription Administrator and Azure Active Directory Global Administrator`|

- A conta do administrador não pode ler informações financeiras sem máscaras. Todas as ações são registradas.
- A conta de administrador não pode gerenciar ou fazer logon no Banco de Dados SQL.
- A conta do administrador pode gerenciar o Active Directory e as assinaturas.

#### <a name="role-sql-administrator"></a>Função: administrador SQL

|item      |Exemplo|
|----------|------|
|Nome de Usuário: |`sqlAdmin@contosowebstore.com`|
| Nome: |`SQLADAdministrator PCI Samples`|
| Nome: |`SQL AD Administrator`|
|Sobrenome: |`PCI Samples`|
|Tipo de usuário:| `Administrator`|

- A conta sqladmin não pode exibir informações financeiras não filtradas. Todas as ações são registradas.
- A conta sqladmin pode gerenciar o Banco de Dados SQL.

#### <a name="role-clerk"></a>Função: atendente

|item      |Exemplo|
|----------|------|
|Nome de Usuário:| `receptionist_EdnaB@contosowebstore.com`|
| Nome: |`Edna Benson`|
| Nome:| `Edna`|
|Sobrenome:| `Benson`|
| Tipo de usuário: |`Member`|

Melissa Mello é a recepcionista e gerente de negócios. Ela é responsável por fazer com que as informações do cliente estejam corretas e a cobrança seja concluída. Melissa é a usuária conectada para todas as interações com o site de demonstração da Contoso Webstore. Ela tem os seguintes direitos: 

- Melissa pode criar e ler informações do cliente.
- Melissa pode modificar as informações do cliente.
- Melissa pode substituir as informações financeiras.
- A conta da Melissa não pode exibir informações financeiras não filtradas.



### <a name="contoso-webstore---estimated-pricing"></a>Contoso Webstore - Preços estimados

Essa arquitetura fundamental e o aplicativo Web de exemplo têm uma estrutura de valor mensal e um custo de uso por hora que devem ser considerados na hora de dimensionar a solução. Esses custos podem ser previstos usando a [Calculadora de custos do Azure](https://azure.microsoft.com/pricing/calculator/). A partir de setembro de 2017, o custo mensal estimado para essa solução é de aproximadamente US$ 2.500,00, que inclui uma taxa de uso de US$ 1.000/mês para ASE v2. Esses custos variam com base na quantidade de uso e estão sujeitos a alterações. Cabe ao cliente calcular os custos mensais estimados no momento da implantação para uma estimativa mais precisa. 

A solução usou os serviços do Azure a seguir. Os detalhes da arquitetura de implantação estão localizados na [Arquitetura de Implantação](#deployment-architecture).

>- Gateway de Aplicativo
>- Azure Active Directory
>- Ambiente do Serviço de Aplicativo v2
>- Log Analytics do OMS
>- Cofre da Chave do Azure
>- Grupos de segurança de rede
>- BD SQL do Azure
>- Azure Load Balancer
>- Application Insights
>- Central de Segurança do Azure
>- Aplicativo Web do Azure
>- Automação do Azure
>- Runbooks da Automação do Azure
>- DNS do Azure
>- Rede Virtual do Azure
>- Máquina Virtual do Azure
>- Grupo de recursos e políticas do Azure
>- Armazenamento do Blobs do Azure
>- RBAC (Controle de Acesso Baseado em Função) do Azure Active Directory

## <a name="deployment-architecture"></a>Arquitetura de implantação

A seção a seguir fornece detalhes sobre os elementos de desenvolvimento e implementação.

### <a name="network-segmentation-and-security"></a>Segmentação de rede e segurança

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>Gateway de Aplicativo

A arquitetura de base reduz o risco de vulnerabilidades de segurança usando o Gateway de Aplicativo com um WAF (firewall do aplicativo Web) e o conjunto de regras OWASP habilitado. Dentre outros recursos estão:

- [SSL de ponta a ponta](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Descarregamento de SSL](/azure/application-gateway/application-gateway-ssl-portal) habilitado
- [TLS 1.0 e 1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) desabilitado
- [Firewall do aplicativo Web](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) (modo WAF)
- [Modo de prevenção](/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3.0
- [Log de diagnóstico](/azure/application-gateway/application-gateway-diagnostics) habilitado
- [Investigações de integridade personalizadas](/azure/application-gateway/application-gateway-create-gateway-portal)
- A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center) e o [Assistente do Azure](/azure/advisor/advisor-security-recommendations), que fornecem proteção e notificações adicionais. A Central de Segurança do Azure também fornece um sistema de reputação.

#### <a name="virtual-network"></a>Rede virtual

A arquitetura fundamental define uma rede virtual privada com um espaço de endereço de 10.0.0.0/16.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

Cada uma das camadas de rede tem um NSG (grupo de segurança de rede dedicado):

- Um grupo de segurança de rede DMZ para firewall e WAF de Gateway de Aplicativo
- Um NSG para jumpbox de gerenciamento (host bastião)
- Um NSG para o ambiente do serviço de aplicativo

Cada NSG tem portas e protocolos específicos abertos para o funcionamento seguro e correto da solução. 

Além disso, as seguintes configurações estão habilitadas para cada NSG:

- Os [eventos e logs de diagnóstico](/azure/virtual-network/virtual-network-nsg-manage-log) habilitados são armazenados na conta de armazenamento 
- O OMS Log Analytics está conectado ao [diagnóstico do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

 
#### <a name="subnets"></a>Sub-redes
 Verifique se cada sub-rede está associada ao seu NSG correspondente.

#### <a name="custom-domain-ssl-certificates"></a>Certificados SSL de domínio personalizados
 O tráfego HTTPS é habilitado usando um certificado SSL de domínio personalizado.

### <a name="data-at-rest"></a>Dados em repouso

A arquitetura protege dados em repouso usando criptografia, auditoria de banco de dados e outras medidas.

#### <a name="azure-storage"></a>Armazenamento do Azure

Para atender aos requisitos de criptografia de dados em repouso, o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa [Criptografia do Serviço de Armazenamento](/azure/storage/storage-service-encryption).

#### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure

A instância do Banco de Dados SQL usa as seguintes medidas de segurança de banco de dados:

- [Autenticação e autorização do AD](/azure/sql-database/sql-database-aad-authentication)
- [Auditoria do Banco de Dados SQL](/azure/sql-database/sql-database-auditing-get-started)
- [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)
- [Regras de firewall](/azure/sql-database/sql-database-firewall-configure), permitindo pools de trabalho do ASE e gerenciamento de IP do cliente
- [Detecção de ameaças do SQL](/azure/sql-database/sql-database-threat-detection-get-started)
- [Colunas Always Encrypted](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)
- [Mascaramento de dados dinâmicos do Banco de Dados SQL](/azure/sql-database/sql-database-dynamic-data-masking-get-started) usando o script de pós-implantação do PowerShell

### <a name="logging-and-auditing"></a>Registro em log e auditoria

O [OMS (Operations Management Suite)](/azure/operations-management-suite/) pode fornecer à Contoso Webstore um registro em log extensivo de todas as atividades de sistema e de usuário, inclusive registro em log de dados financeiros. A precisão das alterações pode ser examinada e verificada. 

- **Logs de atividade.**  Os [Logs de atividades](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas em recursos em sua assinatura.
- **Logs de diagnóstico.**  Os [Logs de diagnóstico](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) são todos os logs emitidos por todos os recursos. Esses logs são logs do sistema de eventos do Windows, logs do armazenamento de blobs do Azure, tabelas e logs de fila.
- **Logs de firewall.**  O Gateway de Aplicativo fornece logs de diagnóstico e de acesso completos. Logs do firewall estão disponíveis para recursos do Gateway de Aplicativo que têm o WAF habilitado.
- **Arquivamento em log.**  Todos os logs de diagnóstico são configurados para gravar em uma conta de armazenamento do Azure centralizada e criptografada com arquivamento após um período de retenção definido (2 dias). Os logs são conectados ao Azure Log Analytics para processar, armazenar e usar painéis. O [Log Analytics](https://azure.microsoft.com/services/log-analytics) é um serviço do OMS que ajuda a coletar e analisar dados gerados pelos recursos em seus ambientes local e de nuvem.

### <a name="encryption-and-secrets-management"></a>Gerenciamento de criptografia e segredos

A Contoso Webstore criptografa todos os dados confidenciais e usa o Azure Key Vault para gerenciar chaves e impedir a recuperação de CHD.

- O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ajuda a proteger segredos e chaves criptográficas usadas por aplicativos e serviços em nuvem. 
- A [SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) é usada para criptografar todos os dados financeiros do cliente.
- Os dados são armazenados no disco usando [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) e BitLocker.

### <a name="identity-management"></a>Gerenciamento de identidades

As tecnologias a seguir oferecem recursos de gerenciamento de identidade no ambiente do Azure.

- O [Azure AD (Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários da solução foram criados no Azure Active Directory, incluindo os usuários que acessam o Banco de Dados SQL.
- A autenticação para o aplicativo é executada usando o Azure AD. Para saber mais, confira [Integrando aplicativos com o Active Directory do Azure](/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a criptografia de coluna do banco de dados também usa o Azure AD para autenticar o aplicativo no Banco de Dados SQL do Azure. Para obter mais informações, consulte [Always Encrypted: proteger dados confidenciais no Banco de Dados SQL](/azure/sql-database/sql-database-always-encrypted-azure-key-vault). 
- O [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que poderiam afetar as identidades da organização, configura respostas automatizadas para detectar ações suspeitas relacionadas a identidades de sua organização, investiga incidentes suspeitos e executa as ações apropriadas para resolvê-los.
- O [RBAC (Controle De Acesso Baseado Em Função) do Azure](/azure/active-directory/role-based-access-control-configure) permite o gerenciamento de acesso detalhado para o Azure. O acesso à assinatura é limitado ao administrador da assinatura, e o acesso ao Azure Key Vault é restrito a todos os usuários.

Para saber mais sobre como usar os recursos de segurança do Banco de Dados SQL do Azure, confira o exemplo [Aplicativo de demonstração da Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample) exemplo.
   
### <a name="web-and-compute-resources"></a>Recursos Web e de computação

#### <a name="app-service-environment"></a>Ambiente do Serviço de Aplicativo

O [Serviço de Aplicativo do Azure](/azure/app-service/) é um serviço gerenciado para a implantação de aplicativos Web. O aplicativo Contoso Webstore é implantado como um [aplicativo Web do Serviço de Aplicativo](/azure/app-service-web/app-service-web-overview).

O [Ambiente do Serviço de Aplicativo do Azure (ASE v2)](/azure/app-service/app-service-environment/intro) é um recurso do Serviço de Aplicativo que fornece um ambiente totalmente isolado e dedicado a executar com segurança os aplicativos do Serviço de Aplicativo em grande escala. é um plano de serviço Premium usado por essa arquitetura fundamental para permitir a conformidade com PCI DSS.

Os ASEs são isolados para executar somente aplicativos de um único cliente e sempre são implantados em uma rede virtual. Os clientes têm um controle refinado sobre o tráfego de entrada e saída da rede de aplicativos, e os aplicativos podem estabelecer conexões seguras de alta velocidade por meio de redes virtuais com recursos corporativos locais.

O uso de ASEs para essa arquitetura é permitido para os seguintes controles/configurações:

- Host dentro de uma rede virtual protegida e regras de segurança de rede
- ASE configurado com o certificado ILB autoassinado para comunicação HTTPS
- [Modo Balanceamento de Carga Interno](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (modo 3)
- [TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings) desabilitado
- [Criptografia de TLS](/azure/app-service-web/app-service-app-service-environment-custom-settings) alterado
- Controlar [portas N/W de tráfego de entrada](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 
- [WAF – Restringir dados](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [Tráfego de banco de dados SQL](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview) permitido


#### <a name="jumpbox-bastion-host"></a>Jumpbox (host bastião)

Como o Ambiente do Serviço de Aplicativo é protegido e bloqueado, é preciso haver um mecanismo para permitir as versões ou alterações feitas por DevOps que possam ser necessárias, como a capacidade de monitorar um aplicativo Web usando Kudu. Uma máquina virtual é protegida por trás do balanceador de carga de NAT, que fornece a capacidade de conectar-se à VM em uma porta diferente da TCP 3389. 

Uma máquina virtual foi criada como jumpbox (host bastião) com as seguintes configurações:

-   [Extensão de antimalware](/azure/security/azure-security-antimalware)
-   [Extensão do OMS](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Extensão de Diagnóstico do Azure](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) usando o Azure Key Vault 
-   Uma [política de desligamento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos de máquina virtual quando eles não estiverem em uso

### <a name="security-and-malware-protection"></a>Proteção de segurança e malware

A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) permite uma exibição centralizada do estado da segurança de todos os seus recursos do Azure. Você pode verificar rapidamente se os controles de segurança apropriados estão em vigor e configurados de maneira correta, bem como identificar com rapidez os recursos que exigem atenção.  

O [Assistente do Azure](/azure/advisor/advisor-overview) é um consultor de nuvem personalizado que ajuda a seguir as melhores práticas para otimizar as implantações do Azure. Ele analisa a telemetria de uso e a configuração do recurso e, depois, recomenda soluções que possam ajudar a melhorar a economia de custo, o desempenho, a alta disponibilidade e a segurança de seus recursos do Azure.

O [Microsoft Antimalware](/azure/security/azure-security-antimalware) para serviços de nuvem do Azure e máquinas virtuais é uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados, com alertas configuráveis quando softwares mal-intencionado ou indesejado conhecidos tentam se instalar ou executar em seus sistemas do Azure.

### <a name="operations-management"></a>Gerenciamento de operações

#### <a name="application-insights"></a>Application Insights

Use o [Application Insights](https://azure.microsoft.com/services/application-insights/) para obter insights práticos através do gerenciamento de desempenho de aplicativo e análises instantâneas.

#### <a name="log-analytics"></a>Log Analytics

O Log Analytics é um serviço do [OMS (Operations Management Suite)](https://azure.microsoft.com/services/log-analytics/) que ajuda a coletar e analisar dados gerados pelos recursos em seus ambientes local e de nuvem.

#### <a name="oms-solutions"></a>Soluções do OMS

Essas soluções OMS adicionais devem ser consideradas e configuradas: 
- [Log Analytics de Atividade](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
- [Usar a Análise de Rede do Azure](/azure/log-analytics/log-analytics-azure-networking-analytics?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Azure SQL Analytics](/azure/log-analytics/log-analytics-azure-sql)
- [Controle de alterações](/azure/log-analytics/log-analytics-change-tracking?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Análise do Cofre de Chaves](/azure/log-analytics/log-analytics-azure-key-vault?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Mapa do Serviço](/azure/operations-management-suite/operations-management-suite-service-map)
- [Segurança e Auditoria](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Antimalware](/azure/log-analytics/log-analytics-malware?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Gerenciamento de atualizações](/azure/operations-management-suite/oms-solution-update-management)

### <a name="security-center-integration"></a>Integração da Central de Segurança

A implantação padrão destina-se a fornecer uma linha de base de recomendações da Central de Segurança, indicando um estado de configuração íntegro e seguro. Você pode habilitar a coleta de dados da Central de Segurança do Azure. Para saber mais, confira [Central de Segurança do Azure – Introdução](/azure/security-center/security-center-get-started).

## <a name="deploy-the-solution"></a>Implantar a solução

Os componentes para implantar esta solução estão disponíveis no [Repositório de código do projeto][code-repo]. A implantação da arquitetura fundamental requer várias etapas executadas por meio do Microsoft PowerShell v5. Para conectar-se ao site, você deve fornecer um nome de domínio personalizado (por exemplo, contoso.com). Isso é especificado usando a opção `-customHostName` na etapa 2. Para saber mais, confira [Comprar um nome de domínio personalizado Aplicativos Web do Azure](/azure/app-service-web/custom-dns-web-site-buydomains-web-app). Um nome de domínio personalizado não é necessário para implantar e executar a solução com êxito, mas não será possível se conectar ao site para fins de demonstração.

Os scripts adicionam usuários de domínio ao locatário do Azure AD que você especifica. A Microsoft recomenda criar um novo locatário do Azure AD para ser usado como um teste.

Se você encontrar problemas durante a implantação, confira [Perguntas frequentes e solução de problemas](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md).

A Microsoft realmente recomenda que uma instalação limpa do PowerShell seja usada para implantar a solução. Como alternativa, verifique se você está usando os módulos necessários mais recentes para a execução adequada dos scripts de instalação. Este exemplo conecta-se ao o jumpbox (host bastião) e executa os comandos a seguir. Observe que isso habilita o comando de domínio personalizado.


1. Instale os módulos necessários e configure as funções de administrador corretamente.
 
    ```powershell
     .\0-Setup-AdministrativeAccountAndPermission.ps1 
        -azureADDomainName contosowebstore.com
        -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -configureGlobalAdmin 
        -installModules
    ```
    Para obter instruções detalhadas de uso, confira [Instruções de Script - Configurar conta de administração e permissões](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/0-Setup-AdministrativeAccountAndPermission.md).
    
2. Instale o gerenciamento de atualizações da solução. 
 
    ```powershell
    .\1-DeployAndConfigureAzureResources.ps1 
        -resourceGroupName contosowebstore
        -globalAdminUserName adminXX@contosowebstore.com 
        -globalAdminPassword **************
        -azureADDomainName contosowebstore.com 
        -subscriptionID XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
        -suffix PCIcontosowebstore
        -customHostName contosowebstore.com
        -sqlTDAlertEmailAddress edna@contosowebstore.com 
        -enableSSL
        -enableADDomainPasswordPolicy 
    ```
    
    Para obter instruções detalhadas de uso, confira [instruções de Script - Implantar e configurar recursos do Azure](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md).
    
3. Registro em log e monitoramento do OMS Depois que a solução for implantada, um espaço de trabalho do [OMS (Microsoft Operations Management Suite)](/azure/operations-management-suite/operations-management-suite-overview) poderá ser aberto e os modelos de exemplo fornecidos no repositório de solução poderão ser usados para ilustrar como um painel de monitoramento pode ser configurado. Para os modelos de exemplo do OMS, consulte a [pasta omsDashboards](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md). Observe que os dados devem ser coletados no OMS para que os modelos sejam implantados corretamente. Isso pode levar até uma hora ou mais dependendo da atividade do site.
 
    Ao configurar o registro em log do OMS, considere a inclusão desses recursos:
 
    - Microsoft.Network/applicationGateways
    - Microsoft.Network/NetworkSecurityGroups
    - Microsoft.Web/serverFarms
    - Microsoft.Sql/servers/databases
    - Microsoft.Compute/virtualMachines
    - Microsoft.Web/sites
    - Microsoft.KeyVault/Vaults
    - Microsoft.Automation/automationAccounts
 

    
## <a name="threat-model"></a>Modelo de ameaça

Um diagrama de fluxo de dados (DFD) e o modelo de risco de exemplo para a Contoso Webstore [Modelo de risco de projeto](https://aka.ms/pciblueprintthreatmodel).

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>Matriz de responsabilidades do cliente

Os clientes são responsáveis por manter uma cópia da [Matriz de Resumo de Responsabilidade](https://aka.ms/fsiblueprintcrm), que descreve os requisitos do PCI DSS que são responsabilidade do cliente e os que são responsabilidade do Microsoft Azure.



## <a name="disclaimer-and-acknowledgments"></a>Isenção de responsabilidade e reconhecimentos

*Setembro de 2017*

- Este documento serve apenas para fins informativos. A MICROSOFT E A AVYAN NÃO FORNECEM NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.  
- Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft ou da Avyan.  
- Os clientes podem copiar e usar este documento para fins de consulta interna.  

  > [!NOTE]
  > Determinadas recomendações neste documento podem resultar no aumento do uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do Azure para o cliente.  

- A solução neste documento serve como uma arquitetura fundamental e não deve ser usada no estado em que se encontra para fins de produção. Para ficar em conformidade é necessário que os clientes consultem um auditor para validar a solução final do cliente.  
- Todos os nomes de cliente, registros de transações e dados relacionados nesta página são fictícios, criados para essa arquitetura fundamental e meramente ilustrativos. Nenhuma associação ou conexão real é intencional ou deve ser inferida.  
- Esta solução foi desenvolvida em conjunto pela Microsoft e a Avyan Consulting e está disponível de acordo com a [Licença MIT](https://opensource.org/licenses/MIT).

### <a name="document-authors"></a>Autores de documentos

* *Frank Simorjay (Microsoft)*  

[code-repo]: https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms "Repositório do código"
