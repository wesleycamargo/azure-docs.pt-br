---
title: Diagrama de processamento de pagamento para ambientes em conformidade com o PCI DSS
description: Requisito de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 2f1e00a8-0dd6-477f-9453-75424d06a1df
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2017
ms.author: frasim
ms.openlocfilehash: 7f85c8b0377e57f08044bac41dbddbbedb7a4f55
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2017
---
# <a name="azure-blueprint-automation-payment-processing-for-pci-dss-compliant-environments"></a>Automação do plano gráfico do Azure: processamento de pagamento para ambientes em conformidade com o PCI DSS

## <a name="overview"></a>Visão geral

O processamento de pagamento para ambientes compatíveis com PCI DSS fornece orientação para a implantação de um ambiente PaaS (Plataforma como um Serviço) compatível com PCI DSS adequado para processar dados confidenciais de cartão de pagamento. Ele apresenta uma arquitetura de referência comum e foi projetado para simplificar a adoção do Microsoft Azure. Esse plano gráfico ilustra uma solução completa para atender às necessidades das organizações que buscam uma abordagem baseada em nuvem para reduzir a carga e o custo de implantação.

Esse plano gráfico foi elaborado para ajudar a atender aos requisitos rigorosos dos padrões de segurança de dados do setor de cartões de pagamento (PCI DSS 3.2) para coleta, armazenamento e recuperação de dados de cartão de pagamento. Ele demonstra o processamento adequado de dados de cartão de crédito (incluindo o número, a validade e o código de verificação do cartão) em um ambiente seguro, compatível e com várias camadas, implantado como uma solução PaaS completa baseada no Azure. Para saber mais sobre os requisitos de PCI DSS 3.2 e a solução, confira [Requisitos de PCI DSS - Visão geral de alto nível](pci-dss-requirements-overview.md).

Esse plano gráfico serve como base para os clientes entenderem melhor os requisitos específicos e não deve ser usado no estado em que se encontra em um ambiente de produção. Implantar um aplicativo nesse ambiente sem modificações não é suficiente para atender a todos os requisitos de uma solução personalizada compatível com PCI DSS. Observe o seguinte:
- Esse plano gráfico fornece uma linha de base para ajudar os clientes a usar o Microsoft Azure em conformidade com PCI DSS.
- A consecução da conformidade com PCI DSS exige a certificação de uma solução de cliente de produção por um QSA (Supervisor de Segurança Qualificado).
- Os clientes são responsáveis por realizar as devidas revisões de segurança e conformidade de qualquer solução criada usando essa arquitetura fundamental, já que os requisitos podem variar com base nas particularidades da implementação e da região geográfica de cada cliente.  

Para uma visão geral de como essa solução funciona, assista a este [vídeo](https://aka.ms/pciblueprintvideo) explicando e demonstrando sua implantação.

## <a name="solution-components"></a>Componentes da solução

A arquitetura fundamental consiste nos seguintes componentes:

- **Diagrama de arquitetura**. O diagrama mostra a arquitetura de referência usada para a solução Contoso Webstore.
- **Modelos de implantação** Nessa implantação, os [modelos do Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) são usados para implantar os componentes da arquitetura automaticamente no Microsoft Azure especificando parâmetros de configuração durante a instalação.
- **Scripts de implantação automatizada**. Esses scripts ajudam a implantar a solução de ponta a ponta. Os scripts consistem em:
    - Uma instalação do módulo e um script de configuração de [administrador global](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) são usados para instalar e verificar se os módulos do PowerShell obrigatórios e as funções de administrador global foram configuradas corretamente.
    - Um script de instalação do PowerShell é usada para implantar a solução completa, fornecida por meio de um arquivo .zip e um arquivo .bacpac que contêm um aplicativo Web de demonstração criado previamente com o [conteúdo de exemplo de banco de dados SQL](https://github.com/Microsoft/azure-sql-security-sample). conteúdo. O código-fonte desta solução está disponível para análise no [repositório de código do plano gráfico de processamento de pagamento][code-repo]. 

## <a name="architectural-diagram"></a>Diagrama de arquitetura

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>Cenário do usuário

O plano gráfico se refere ao caso de uso abaixo.

> Esse cenário ilustra como um loja online fictícia migrou seu processamento de cartões de pagamento para uma solução PaaS baseada no Azure. A solução lida com a coleta de informações de usuário básicas, incluindo dados de pagamento. A solução não processa pagamentos com esses dados do titular do cartão; depois que os dados são coletados, os clientes são responsáveis por iniciar e concluir as transações por um processador de pagamentos. Para obter mais informações, consulte ["Análise e diretrizes de implementação"](https://aka.ms/pciblueprintprocessingoverview).

### <a name="use-case"></a>Caso de uso
Uma pequena loja online chamada *Contoso Webstore* está pronta para mover seus sistemas de pagamento para a nuvem. Ela selecionou o Microsoft Azure para hospedar o processo de compra e permitir que um assistente colete pagamentos de cartão de crédito de seus clientes.

O administrador está procurando uma solução que pode ser implantada rapidamente para atingir suas metas em uma solução de nuvem. Ele usará essa POC (prova de conceito) para discutir com os stakeholders como o Azure pode ser usado para coletar, armazenar e recuperar dados de cartão de crédito e débito em conformidade com as exigências de PCI DSS (Payment Card Industry Data Security Standard).

> Você será responsável por realizar as devidas revisões de segurança e conformidade de qualquer solução criada com a arquitetura usada por essa POC, já que os requisitos podem variar com base nas particularidades da sua implementação e região geográfica. O PCI DSS requer que você trabalhe diretamente com um Supervisor de Segurança Qualificado reconhecido para certificar sua solução pronta para produção.

### <a name="elements-of-the-foundational-architecture"></a>Elementos da arquitetura fundamental

A arquitetura fundamental é criada com os seguintes elementos fictícios:

Site de domínio`contosowebstore.com`

Funções de usuário usadas para explicar o caso de uso e fornecer informações sobre a interface do usuário.

#### <a name="role-site-and-subscription-admin"></a>Função: administração de site e assinatura

|Item      |Exemplo|
|----------|------|
|Nome de Usuário: |`adminXX@contosowebstore.com`|
| Nome: |`Global Admin Azure PCI Samples`|
|Tipo de usuário:| `Subscription Administrator and Azure Active Directory Global Administrator`|

- A conta de administrador não pode ler informações de cartão de crédito sem máscaras. Todas as ações são registradas.
- A conta de administrador não pode gerenciar ou fazer logon no Banco de Dados SQL.
- A conta de administrador pode gerenciar o Active Directory e a assinatura.

#### <a name="role-sql-administrator"></a>Função: administrador SQL

|Item      |Exemplo|
|----------|------|
|Nome de Usuário: |`sqlAdmin@contosowebstore.com`|
| Nome: |`SQLADAdministrator PCI Samples`|
| Nome: |`SQL AD Administrator`|
|Sobrenome: |`PCI Samples`|
|Tipo de usuário:| `Administrator`|

- A conta sqladmin não pode exibir informações de cartão de crédito não filtradas. Todas as ações são registradas.
- A conta sqladmin pode gerenciar o banco de dados SQL.

#### <a name="role-clerk"></a>Função: atendente

|Item      |Exemplo|
|----------|------|
|Nome de Usuário:| `receptionist_EdnaB@contosowebstore.com`|
| Nome: |`Edna Benson`|
| Nome:| `Edna`|
|Sobrenome:| `Benson`|
| Tipo de usuário: |`Member`|

Melissa Mello é a recepcionista e gerente de negócios. Ela é responsável por fazer com que as informações do cliente estejam corretas e a cobrança seja concluída. Melissa é a usuária conectada para todas as interações com o site de demonstração da Contoso Webstore. Ela tem os seguintes direitos: 

- Melissa pode criar e ler informações do cliente
- Melissa pode modificar as informações do cliente.
- Melissa pode substituir as informações de número de cartão de crédito, validade e CVV.

> Na Contoso Webstore, o usuário fica automaticamente como a usuária **Melissa** para testar os recursos do ambiente implantado.

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


A arquitetura fundamental reduz o risco de vulnerabilidades de segurança usando o Gateway de Aplicativo com WAF (firewall do aplicativo Web) e o conjunto de regras OWASP habilitado. Dentre outros recursos estão:

- [SSL de ponta a ponta](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Habilitar [Descarregamento SSL](/azure/application-gateway/application-gateway-ssl-portal)
- Desabilitar [TLS versões 1.0 e 1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall do aplicativo Web](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) (modo WAF)
- [Modo de prevenção](/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3.0
- [Habilitar o registro em log de diagnóstico](/azure/application-gateway/application-gateway-diagnostics)
- [Investigações de integridade personalizadas](/azure/application-gateway/application-gateway-create-gateway-portal)
- A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center) e o [Assistente do Azure](/azure/advisor/advisor-security-recommendations) fornecer proteção e notificações adicionais. A Central de Segurança do Azure também fornece um sistema de reputação.

#### <a name="virtual-network"></a>Rede virtual

A arquitetura fundamental define uma rede virtual privada com um espaço de endereço de 10.0.0.0/16.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

Cada uma das camadas de rede tem um NSG (grupo de segurança de rede dedicado):
- Um grupo de segurança de rede DMZ para firewall e WAF de Gateway de Aplicativo
- Um NSG para jumpbox de gerenciamento (host bastião)
- Um NSG para o ambiente do serviço de aplicativo

Cada NSG tem portas e protocolos específicos abertos para o funcionamento seguro e correto da solução. Para saber mais, confira [Diretriz de PCI – Grupos de Segurança de Rede](#network-security-groups).

Cada NSG tem portas e protocolos específicos abertos para o funcionamento seguro e correto da solução. Além disso, as seguintes configurações estão habilitadas para cada NSG:
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

O [OMS (Operations Management Suite)](/azure/operations-management-suite/) pode fornecer à Contoso Webstore um registro em log extensivo de todas as atividades de sistema e usuário, inclusive registro em log do titular dos dados. A precisão das alterações pode ser examinada e verificada. 

- **Logs de atividades:** os [logs de atividades](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas em recursos em sua assinatura.
- **Logs de Diagnóstico: os** [Logs de diagnóstico](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) são todos os logs emitidos por cada recurso. Esses logs são logs de eventos do sistema Windows, Armazenamento de Blobs do Azure, tabelas e logs de fila.
- **Logs de firewall**: o Gateway de Aplicativo fornece logs de diagnóstico e acesso completos. Logs do firewall estão disponíveis para recursos do Gateway de Aplicativo que têm o WAF habilitado.
- **Arquivamento de logs:** todos os logs de diagnóstico estão configurados para gravar em uma conta de armazenamento do Azure centralizada e criptografada com arquivamento após um período de retenção definido (2 dias). Os logs são conectados ao Azure Log Analytics para processar, armazenar e usar painéis. O [Log Analytics](https://azure.microsoft.com/services/log-analytics) é um serviço do OMS que ajuda a coletar e analisar dados gerados pelos recursos em seus ambientes local e de nuvem.

### <a name="encryption-and-secrets-management"></a>Gerenciamento de criptografia e segredos

A Contoso Webstore criptografa todos os dados de cartão de crédito e usa o Azure Key Vault para gerenciar chaves, impedindo a recuperação de CHD.

- O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ajuda a proteger segredos e chaves criptográficas usadas por aplicativos e serviços em nuvem. 
- [SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) é usada para criptografar todos os dados do cliente, data de validade e CVV.
- Os dados são armazenados no disco usando [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) e BitLocker.

### <a name="identity-management"></a>Gerenciamento de identidades

As tecnologias a seguir oferecem recursos de gerenciamento de identidade no ambiente do Azure.
- O [Azure AD (Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários da solução foram criados no Azure Active Directory, incluindo os usuários que acessam o Banco de Dados SQL.
- A autenticação para o aplicativo é executada usando o Azure AD. Para saber mais, confira [Integrando aplicativos com o Active Directory do Azure](/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a criptografia de coluna do banco de dados também usa o Azure AD para autenticar o aplicativo no Banco de Dados SQL do Azure. Para obter mais informações, consulte [Always Encrypted: proteger dados confidenciais no Banco de Dados SQL](/azure/sql-database/sql-database-always-encrypted-azure-key-vault). 
- O [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que afetam as identidades da organização, configura as respostas automatizadas para detectar ações suspeitas relacionadas a identidades de sua organização e investiga incidentes suspeitos e toma a medida apropriada para resolvê-los.
- O [RBAC (Controle De Acesso Baseado Em Função) do Azure](/azure/active-directory/role-based-access-control-configure) permite o gerenciamento de acesso detalhado para o Azure. O acesso à assinatura é limitado ao administrador da assinatura, e o acesso ao Azure Key Vault é restrito a todos os usuários.

Para saber mais sobre como usar os recursos de segurança do Banco de Dados SQL do Azure, confira o exemplo [Aplicativo de demonstração da Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample) exemplo.
   
### <a name="web-and-compute-resources"></a>Recursos Web e de computação

#### <a name="app-service-environment"></a>Ambiente do Serviço de Aplicativo

O [Serviço de Aplicativo do Azure](/azure/app-service/) é um serviço gerenciado para a implantação de aplicativos Web. O aplicativo Contoso Webstore é implantado como um [aplicativo Web do Serviço de Aplicativo](/azure/app-service-web/app-service-web-overview).

O [Ambiente do Serviço de Aplicativo do Azure (ASE v2)](/azure/app-service/app-service-environment/intro) é um recurso do Serviço de Aplicativo que fornece um ambiente totalmente isolado e dedicado a executar com segurança os aplicativos do Serviço de Aplicativo em grande escala. é um plano de serviço Premium usado por essa arquitetura fundamental para permitir a conformidade com PCI DSS.

Os ASEs são isolados para executar somente aplicativos de um único cliente e sempre são implantados em uma rede virtual. Os clientes têm um controle refinado sobre o tráfego de entrada e saída da rede de aplicativos, e os aplicativos podem estabelecer conexões seguras de alta velocidade por meio de redes virtuais com recursos corporativos locais.

O uso de ASEs para essa arquitetura é permitido para os seguintes controles/configurações:

- Host em uma Rede Virtual protegida e regras de segurança de rede
- ASE configurado com um certificado de ILB autoassinado para comunicação HTTPS
- [Modo Balanceamento de Carga Interno](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (modo 3)
- Desabilitar [TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings), um protocolo TLS que foi preterido do ponto de vista de PCI DSS
- Alterar [Codificação TLS](/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Controlar [portas N/W de tráfego de entrada](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 
- [WAF – Restringir dados](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Permitir [tráfego de Banco de Dados SQL](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)


#### <a name="jumpbox-bastion-host"></a>Jumpbox (host bastião)

Como o Ambiente do Serviço de Aplicativo é protegido e bloqueado, é preciso haver um mecanismo para permitir as versões ou alterações feitas por DevOps que possam ser necessárias, como a capacidade de monitorar um aplicativo Web usando Kudu. A máquina virtual está protegida por trás do balanceador de carga NAT que permite a conexão da VM em uma porta diferente de TCP 3389. 

Uma máquina virtual foi criada como jumpbox (host bastião) com as seguintes configurações:

-   [Extensão de antimalware](/azure/security/azure-security-antimalware)
-   [Extensão do OMS](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Extensão de Diagnóstico do Azure](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) usando o Azure Key Vault (obedece a requisitos de Azure Governamental, PCI DSS, HIPAA e outros).
-   Um [política de desligamento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos de máquina virtual quando não está em uso.

### <a name="security-and-malware-protection"></a>Proteção de segurança e malware

A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) permite uma exibição centralizada do estado da segurança de todos os seus recursos do Azure. Você pode verificar rapidamente se os controles de segurança apropriados estão em vigor e configurados de maneira correta, bem como identificar com rapidez os recursos que exigem atenção.  

O [Assistente do Azure](/azure/advisor/advisor-overview) é um consultor de nuvem personalizado que ajuda a seguir as melhores práticas para otimizar as implantações do Azure. Ele analisa a telemetria de uso e configuração do recurso e, depois, recomenda soluções que podem ajudar você a melhorar a economia, o desempenho, a alta disponibilidade e a segurança de seus recursos do Azure.

O [Microsoft Antimalware](/azure/security/azure-security-antimalware) para Serviços de Nuvem do Azure e Máquinas Virtuais é uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados, com alertas configuráveis quando se sabe que software mal-intencionado ou indesejado tenta se instalar ou executar no seus sistemas do Azure.

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

A implantação padrão destina-se a fornecer uma linha de base de recomendações da central de segurança, indicando um estado de integridade e segurança da configuração. Você pode habilitar a coleta de dados da Central de Segurança do Azure. Para saber mais, confira [Central de Segurança do Azure – Introdução](/azure/security-center/security-center-get-started).

## <a name="deploy-the-solution"></a>Implantar a solução

Os componentes para implantar essa solução estão disponíveis no [repositório de código Diagrama de PCI][code-repo]. A implantação da arquitetura fundamental requer várias etapas executadas por meio do Microsoft PowerShell v5. Para conectar-se ao site, você deve fornecer um nome de domínio personalizado (por exemplo, contoso.com). Isso é especificado usando a opção `-customHostName` na etapa 2. Para saber mais, confira [Comprar um nome de domínio personalizado Aplicativos Web do Azure](/azure/app-service-web/custom-dns-web-site-buydomains-web-app). Um nome de domínio personalizado não é necessário para implantar e executar a solução com êxito, mas não será possível se conectar ao site para fins de demonstração.

Os scripts adicionam usuários de domínio ao locatário do Azure AD que você especifica. Recomendamos criar um novo locatário do Azure AD para usar como teste.

Se você encontrar problemas durante a implantação, confira [Perguntas frequentes e solução de problemas](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md).

É altamente recomendável que uma instalação limpa do PowerShell seja usada para implantar a solução. Como alternativa, verifique se você está usando os módulos necessários mais recentes para a execução adequada dos scripts de instalação. Neste exemplo, faça logon na jumpbox (host bastião) e execute os comandos a seguir. Observe que isso habilita o comando de domínio personalizado.


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
    
2. Instalar solution-update-management 
 
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
    
3. Registro em log e monitoramento do OMS Depois que a solução for implantada, um espaço de trabalho do [OMS (Microsoft Operations Management Suite)](/azure/operations-management-suite/operations-management-suite-overview) pode ser aberto e os modelos de exemplo fornecidos no repositório de solução podem ser usados para ilustrar como um painel de monitoramento pode ser configurado. Para os modelos de exemplo do OMS, consulte a [pasta omsDashboards](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md). Observe que os dados devem ser coletados no OMS para que os modelos sejam implantados corretamente. Isso pode levar até uma hora ou mais dependendo da atividade do site.
 
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

Um diagrama de fluxo de dados (DFD) e o modelo de ameaça de exemplo para a Contoso Webstore [Modelo de ameaça de plano gráfico de processamento de pagamento](https://aka.ms/pciblueprintthreatmodel).

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>Matriz de responsabilidades do cliente

Os clientes são responsáveis por manter uma cópia da [Matriz de Resumo de Responsabilidade](https://aka.ms/pciblueprintcrm32), que descreve os requisitos de PCI DSS de responsabilidade do cliente e os de responsabilidade do Microsoft Azure.

## <a name="pci-compliance-review"></a>Revisão de conformidade com PCI 

A solução foi revisada pela Coalfire systems, Inc. (Supervisor de Segurança Qualificado por PCI-DSS). O [Exame de conformidade com PCI e diretriz de implementação](https://aka.ms/pciblueprintprocessingoverview) fornece uma análise de um auditor da solução e considerações para transformar o diagrama em uma implantação de produção.

## <a name="disclaimer-and-acknowledgements"></a>Isenção de responsabilidade e confirmações

*Setembro de 2017*

- Este documento serve apenas para fins informativos. A MICROSOFT E A AVYAN NÃO FORNECEM NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.  
- Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft ou da Avyan.  
- Os clientes podem copiar e usar este documento para fins de consulta interna.  

  > [!NOTE]
  > Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.  

- A solução neste documento serve como uma arquitetura fundamental e não deve ser usada no estado em que se encontra para fins de produção. A consecução da conformidade com PCI DSS exige que os clientes consultem um Supervisor de Segurança Qualificado.  
- Todos os nomes de cliente, registros de transações e dados relacionados nesta página são fictícios, criados para essa arquitetura fundamental e meramente ilustrativos. Nenhuma associação ou conexão real é intencional ou deve ser inferida.  
- Esta solução foi desenvolvida em conjunto pela Microsoft e a Avyan Consulting e está disponível de acordo com a [Licença MIT](https://opensource.org/licenses/MIT).
- Esta solução foi examinada pela Coalfire, auditora de PCI-DSS da Microsoft. O [Exame de conformidade com PCI](https://aka.ms/pciblueprintcrm32) fornece uma análise independente e de terceiros da solução, e componentes que precisam ser tratados. 

### <a name="document-authors"></a>Autores de documentos

- *Frank Simorjay (Microsoft)*  
- *Gururaj Pandurangi (Avyan Consulting)*


[code-repo]: https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms "Repositório do código"
