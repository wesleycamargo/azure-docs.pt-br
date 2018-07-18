---
title: Projeto de análise de Integridade do Azure
description: Diretrizes para implantação de um projeto de análise de integridade de HIPAA/HITRUST
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 26566e0a-0a54-49f4-a91d-48e20b7cef71
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2018
ms.author: jomolesk
ms.openlocfilehash: e627019f6219509c21ca6245ee1f7f54f2e1669a
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "33894306"
---
# <a name="azure-security-and-compliance-blueprint---hipaahitrust-health-data-and-ai"></a>Projeto de Segurança e Conformidade do Azure: dados de integridade de HIPAA/HITRUST e IA

## <a name="overview"></a>Visão geral

**O Projeto de Segurança e Conformidade do Azure - Dados de integridade de HIPAA/HITRUST e AI oferece uma implantação imediata de uma solução de PaaS do Azure para demonstrar como ingerir, armazenar, analisar e interagir com dados de integridade de forma segura, ao mesmo tempo em que atende aos requisitos de conformidade do setor. O projeto ajuda a acelerar a utilização e adoção de nuvem para clientes com dados regulados.**

O Projeto de Segurança e Conformidade do Azure - Dados de integridade de HIPAA/HITRUST e AI fornece ferramentas e diretrizes para ajudar a implementar uma lei americana HIPAA (Health Insurance Portability Accountability Act) segura, e ambiente de PaaS (plataforma como serviço) da Information Health Trust (HITRUST) pronto para ingerir, armazenar, analisar e interagir com registros médicos pessoais e não pessoais em um ambiente de nuvem de várias camadas segura, implantado como um solução de ponta a ponta. Ele apresenta uma arquitetura de referência comum e foi projetado para simplificar a adoção do Microsoft Azure. Essa arquitetura fornecida ilustra uma solução para atender às necessidades das organizações que procuram uma abordagem em nuvem para reduzir custo indireto e custo da implantação.

![](images/components.png)

A solução é projetada para consumir um conjunto de dados de exemplo formatado utilizando o FHIR (Fast Healthcare Interoperability Resources), um padrão mundial para troca eletrônica de informações sobre serviços de saúde e armazená-las de forma segura. Dessa forma, os clientes podem usar o Microsoft Azure Machine Learning para aproveitar as análises e ferramentas de business intelligence avançadas para analisar as previsões feitas nos dados de exemplo. Como um exemplo do tipo de experimentação que o Azure Machine Learning pode facilitar, o plano inclui um exemplo de conjunto de dados, scripts e ferramentas para prever o tempo de permanência de um paciente em uma instalação hospitalar. 

Esse projeto destina-se a servir de base modular para que os clientes ajustem aos requisitos específicos, desenvolvendo novas experimentações do Azure Machine Learning para resolver cenários de casos de uso clínico e operacionais. É projeto para ser seguro e compatível quando implantado, no entanto, os clientes são responsáveis por configurar as funções corretamente e implementar quaisquer modificações. Observe o seguinte:

-   Este projeto fornece uma linha de base para ajudar os clientes a usar o Microsoft Azure em um ambiente HITRUST e HIPAA.

-   Embora o projeto tenha sido concebido para ser alinhado com HIPAA e HITRUST (através do CSF - Common Security Framework), ele não deve ser considerado compatível até ser certificado por um auditor externo em conformidade com os requisitos de certificação HIPAA e HITRUST.

-   Os clientes são responsáveis pela realização de avaliações adequadas de segurança e conformidade de qualquer solução compilada usando essa arquitetura fundamental.

## <a name="deploying-the-automation"></a>Implantar a automação

- Para implantar a solução, siga as instruções fornecidas nas diretrizes de implantação. 

[![](./images/deploy.png)](https://aka.ms/healthblueprintdeploy)

Para uma visão geral de como essa solução funciona, assista a este [vídeo](https://aka.ms/healthblueprintvideo) explicando e demonstrando sua implantação.

- Perguntas frequentes podem ser encontradas nas diretrizes de [Perguntas Frequentes](https://aka.ms/healthblueprintfaq).

-   **Diagrama de arquitetura.** O diagrama mostra a arquitetura de referência utilizada para o modelo e o exemplo do cenário de caso de uso.

-   **Modelos de implantação** Nessa implantação, os [modelos do Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) são usados para implantar os componentes da arquitetura automaticamente no Microsoft Azure especificando parâmetros de configuração durante a instalação.

-   **[Scripts de implantação automatizada](https://aka.ms/healthblueprintdeploy)**. Esses scripts ajudam a implantar a solução. Os scripts consistem em:


-   Uma instalação do módulo e um script de configuração de [administrador global](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) são usados para instalar e verificar se os módulos do PowerShell obrigatórios e as funções de administrador global foram configuradas corretamente. 
-   Um script do PowerShell de instalação é utilizado para implantar a solução, fornecido por meio de um arquivo .zip que contém funções de demonstração pré-criadas.

## <a name="solution-components"></a>Componentes da solução


A arquitetura fundamental é composta pelos componentes a seguir:

-   **[Modelo de risco](https://aka.ms/healththreatmodel)** Um modelo de risco abrangente é fornecido no formato tm7 para uso com a [Microsoft Threat Modeling Tool](https://www.microsoft.com/en-us/download/details.aspx?id=49168), mostrando os componentes da solução, os fluxos de dados entre eles e os limites de confiança. O modelo pode ajudar os clientes a entender os pontos de risco em potencial na infraestrutura do sistema ao desenvolver componentes de aprendizado de máquina ou outras modificações.

-   **[Matriz de implementação do cliente](https://aka.ms/healthcrmblueprint)** Uma pasta de trabalho do Microsoft Excel lista os requisitos HITRUST relevantes e explica como a Microsoft e o cliente são responsáveis pelo cumprimento de cada um desses requisitos.

-   **[Análise de integridade.](https://aka.ms/healthreviewpaper)** A solução foi revisada pela Coalfire Systems, Inc. A Análise de Conformidade de Integridade (HIPAA e HITRUST) e diretrizes para implantação fornecem uma análise do \'auditor da solução e considerações para transformar o projeto em uma implantação pronta para produção.

# <a name="architectural-diagram"></a>Diagrama de arquitetura


![](images/refarch.png)

## <a name="roles"></a>Funções


O projeto define duas funções para usuários administrativos (operadores) e três funções para usuários em gerenciamento hospitalar e atendimento ao paciente. Uma sexta função é definida para um auditor avaliar a conformidade com HIPAA e outros regulamentos. O RBAC (controle de acesso baseado em função) do Azure permite o gerenciamento de acesso com mais precisão para cada usuário da solução através de funções personalizadas e internas. Consulte [Introdução ao Controle de Acesso Baseado em Função no Portal do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) e [Funções internas para controle de acesso baseado em função do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) para obter informações detalhadas sobre RBAC, funções e permissões.

### <a name="site-administrator"></a>Administrador de site


O administrador de site é responsável pela assinatura do Azure do cliente. Eles controlam a implantação geral, mas não têm acesso aos registros de pacientes.

-   Atribuições de função padrão: [Proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)

-   Atribuições de função personalizadas: N/A

-   Escopo: Assinatura

### <a name="database-analyst"></a>Analista de Banco de Dados

O analista de banco de dados administra o banco de dados e a instância do SQL Server.
Eles não têm acesso aos registros de pacientes.

-   Atribuições de função interna: [Colaborador de banco de dados SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-db-contributor), [Colaborador do SQL Server](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-server-contributor)

-   Atribuições de função personalizadas: N/A

-   Escopo: ResourceGroup

 ### <a name="data-scientist"></a>Cientista de dados


O cientista de dados opera o serviço do Microsoft Azure Machine Learning. Eles podem importar, exportar e gerenciar dados e executar relatórios. O cientista de dados tem acesso aos dados do paciente, mas não tem privilégios administrativos.

-   Atribuições de função internas: [Colaborador da Conta de Armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)

-   Atribuições de função personalizadas: N/A

-   Escopo: ResourceGroup

### <a name="chief-medical-information-officer-cmio"></a>CMIO (Diretor médico executivo de informações)


O CMIO permeiam a divisão entre informática/tecnologia e profissionais de saúde em uma organização de saúde. Suas obrigações geralmente incluem o uso de análises para determinar se os recursos estão sendo alocados adequadamente dentro da organização.

-   Atribuições de função internas: Nenhuma

### <a name="care-line-manager"></a>Gerente de enfermagem


O Gerente de enfermagem está diretamente envolvido com o cuidado dos pacientes.
Essa função exige o monitoramento do status de cada paciente, bem como a certeza de que a equipe está disponível para atender às demandas de cuidados de seus pacientes. O gerente de enfermagem é responsável por adicionar e atualizar registros de pacientes.

-   Atribuições de função internas: Nenhuma

-   Atribuições de função personalizadas: tem privilégio para executar HealthcareDemo.ps1 para fazer tanto a Admissão do paciente como a Alta do paciente.

-   Escopo: ResourceGroup

### <a name="auditor"></a>Auditor


O auditor avalia a conformidade da solução. Eles não têm acesso direto à rede.

-   Atribuições de função internas: [Leitor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)

-   Atribuições de função personalizadas: N/A

-   Escopo: Assinatura

## <a name="example-use-case"></a>Exemplo de caso de uso


O exemplo de caso de uso incluído neste projeto ilustra como o Projeto pode ser utilizado para habilitar o aprendizado de máquina e análises em dados de integridade na nuvem. Contosoclínica é um hospital de pequeno porte localizado nos Estados Unidos. Os administradores de rede hospitalar querem usar o Microsoft Azure Machine Learning para uma melhor previsão do tempo de permanência do paciente no momento da internação, visando aumentar a eficiência da carga de trabalho operacional e melhorar a qualidade dos cuidados que podem ser fornecidos.

### <a name="predicting-length-of-stay"></a>Previsão da duração da permanência


O cenário de caso de uso utiliza o Microsoft Azure Machine Learning para prever o período de permanência de um paciente admitido recentemente, comparando os dados médicos obtidos na entrada do paciente com dados históricos agregados de pacientes anteriores.
O projeto inclui um grande conjunto de registros médicos anônimos para demonstrar o treinamento e as capacidades preditivas da solução. Em uma implantação de produção, os clientes usariam seus próprios registros para treinar a solução para previsões mais precisas, refletindo os detalhes únicos de seu ambiente, instalações e pacientes.

### <a name="users-and-roles"></a>Usuários e funções


**Administrador de site -- Alexandre**

*Email: Alexandre\_SiteAdmin*

O trabalho de Alexandre é avaliar tecnologias que podem reduzir o custo indireto de gerenciamento de uma rede local e reduzir os custos de gerenciamento. Alexandre tem avaliado o Azure há algum tempo, mas tem dificuldades em configurar os serviços necessários para atender aos requisitos de conformidade da HiTrust para armazenar os Dados do Paciente na nuvem. Alexandre selecionou o AI de Integridade do Azure para implementar uma solução de integridade em conformidade, que abordou os requisitos para atender aos requisitos do cliente para HiTrust.

**Cientista de dados -- Débora**

*Email: Debora\_DataScientist*

Débora é responsável por usar e criar modelos que analisam registros médicos para fornecer informações sobre o atendimento ao paciente. Débora usa SQL e a linguagem de programação estatística R para criar seus modelos.

**Analista de Banco de Dados -- Daniel**

*Email: Daniel\_DBAnalyst*

Daniel é o principal contato para assuntos relacionados ao Microsoft SQL Server, que armazena todos os dados de pacientes da Contosoclínica. Daniel é um administrador experiente do administrador do SQL Server que recentemente familiarizou-se com o Banco de Dados SQL do Microsoft Azure.

**Diretor médico executivo de informações -- Carolina**

Carolina está trabalhando com Diogo, gerente de enfermagem, e Débora, cientista de dados, para determinar quais fatores influenciam o tempo de permanência do paciente.
Carolina usa as previsões da solução LOS (duração de permanência) para determinar se os recursos estão sendo alocados adequadamente na rede hospitalar. Por exemplo, usando o painel incluído nesta solução.

**Gerente de enfermagem -- Diogo**

*Email: Diogo\_CareLineManager*

Como o indivíduo diretamente responsável pela gestão de admissão e alta de pacientes na Contosoclínica, Diogo usa as previsões geradas pela solução LOS para garantir que funcionários adequados estejam disponíveis para prestar atendimento aos pacientes enquanto estiverem hospedados nas instalações.

**Auditor -- Henrique**

*Email: Henrique\_Auditor*

Henrique é um auditor certificado que tem experiência de auditoria para ISO, SOC e HiTrust. Henrique foi contratado para avaliar a rede da Contosoclínica. Henrique pode revisar a Matriz de Responsabilidades do Cliente fornecida com a solução para garantir que o modelo e a solução LOS possam ser utilizados para armazenar, processar e exibir dados pessoais confidenciais.


# <a name="design-configuration"></a>Configuração de design


Esta seção detalha as configurações padrão e as medidas de segurança internas no Projeto descritas para:

- **INGERIR** fontes de dados brutos, incluindo fonte de dados FHIR
- **ARMAZENAR**  informações confidenciais
- **ANALISAR** e prever resultados
- **INTERAGIR** com os resultados e previsões
- Gerenciamento de **IDENTIDADES** da solução
- Recursos habilitados para **SEGURANÇA**


## <a name="identity"></a>IDENTIDADE 

### <a name="azure-active-directory-and-role-based-access-control-rbac"></a>Microsoft Azure Active Directory e RBAC (Controle de Acesso Baseado em Função)


**Autenticação:**

-   [O Azure AD (Microsoft Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidades e diretório baseado na nuvem multilocatário\' da Microsoft. Todos os usuários da solução foram criados no Azure Active Directory, incluindo os usuários que acessam o Banco de Dados SQL.



-   A autenticação para o aplicativo é executada usando o Azure AD. Para saber mais, confira [Integrando aplicativos com o Active Directory do Azure](/azure/active-directory/develop/active-directory-integrating-applications).

-   O [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que afetam as identidades da organização, configura as respostas automatizadas para detectar ações suspeitas relacionadas a identidades de sua organização e investiga incidentes suspeitos e toma a medida apropriada para resolvê-los.

-   O [RBAC (Controle De Acesso Baseado Em Função) do Azure](/azure/role-based-access-control/role-assignments-portal) permite o gerenciamento de acesso detalhado para o Azure. O acesso à assinatura é limitado ao administrador da assinatura e o acesso do Azure Key Vault é limitado ao administrador de site. São necessárias senhas fortes (mínimo de 12 caracteres com pelo menos uma letra maiúscula/minúscula, número e caractere especial).

-   A autenticação multifator terá suporte quando a opção -enableMFA estiver habilitada durante a implantação.

-   As senhas expiram após 60 dias quando a opção -enableADDomainPasswordPolicy estiver habilitada durante a implantação.

**Funções:**

-   A solução usa [funções internas](/azure/role-based-access-control/built-in-roles) para gerenciar o acesso aos recursos.

-   Todos os usuários são atribuídos a funções internas específicas por padrão.

### <a name="azure-key-vault"></a>Cofre da Chave do Azure

-   Os dados armazenados no Key Vault incluem:

    -   Chave do Application Insights
    -   Chave de acesso ao armazenamento de dados do paciente
    -   Cadeia de conexão do paciente
    -   Nome da tabela de dados do paciente
    -   Ponto de extremidade de serviço Web do Microsoft Azure Machine Learning
    -   Chave de API do Microsoft Azure Machine Learning

-   Políticas de acesso avançadas são configuradas em uma base de necessidade
-   As políticas de acesso do Key Vault são definidas com o mínimo de permissões necessárias para chaves e segredos
-   Todas as chaves e segredos no Key Vault têm datas de validade
-   Todas as chaves no Key Vault são protegidas por HSM \[Tipo de Chave = Chave protegida HSM - chave RSA de 2048 bits\]
-   Todos os usuários/identidades recebem permissões mínimas requeridas usando o RBAC (Controle de Acesso Baseado em Função)
-   Aplicativos não compartilham um Key Vault, exceto se confiam uns nos outros e precisam ter acesso aos mesmos segredos em tempo de execução
-   Os Logs de diagnóstico para Key Vault são habilitados com um período de retenção de pelo menos 365 dias.
-   Operações criptográficas permitidas para chaves são restritas às necessárias

## <a name="ingest"></a>INGERIR 

### <a name="azure-functions"></a>Funções do Azure
A solução foi projetada para usar o [Azure Functions](/azure/azure-functions/) processar a duração dos dados de permanência de exemplo utilizados na demonstração analítica. Três funcionalidade nas funções foram criadas.

**1. Importação em massa de dados phi dos dados do cliente**

Ao usar o script de demonstração. .\\HealthcareDemo.ps1 com a opção **BulkPatientAdmission**, conforme descrito em**Implantar e executar a demonstração**, executa o pipeline de processamento a seguir:
1. **Armazenamento de Blobs** - Exemplo do arquivo .csv de dados do paciente baixado para armazenamento
2. **Grade de Eventos** - O evento publica dados para o Azure Functions (importação em massa - evento de blob)
3. **Azure Functions** - Executa o processamento e armazena os dados no Armazenamento SQL usando a função segura - evento (tipo; url para blobs)
4. **SQL DB** - O armazenamento de banco de dados para Dados do Paciente usando marcas para classificação e o processo do ML é iniciado para fazer o teste de treinamento.

![](images/dataflow.png)

Além disso, o Azure Functions foi projetado para ler e proteger dados confidenciais designados no conjunto de dados de exemplo, usando as marcas a seguir:
- dataProfile => “ePHI”
- proprietário => \<UPN do Administrador de Site\>
- ambiente => “Pilot”
- departamento => "Ecossistema Global" A marcação foi aplicada ao conjunto de dados de exemplo, onde os nomes dos pacientes foram identificados como texto não criptografado.

**2. Admissão de novos pacientes**

Ao usar o script de demonstração. .\\HealthcareDemo.ps1 com a opção **BulkPatientadmission**, conforme descrito em **Implantar e executar a demonstração**, executa o pipeline de processamento a seguir: ![](images/securetransact.png)
**1. Azure Functions** disparou e as solicitações de função para um [token de portador](/rest/api/) do Azure Active Directory.

**2. Key Vault** solicitado por um segredo associado ao token solicitado.

**3. As funções do Azure validam a solicitação, e autorizam a solicitação de acesso ao Key Vault.

**4. Key Vault** retorna o segredo, nesse caso, a cadeia de conexão SQL DB.

**5. Azure Functions** usa a cadeia de conexão para conectar com segurança ao Banco de Dados SQL e continua o processamento adicional para armazenar dados ePHI.

Para obter o armazenamento dos dados, um esquema API comum foi implementado seguindo o FHIR (Fast Healthcare Interoperability Resources, disparo pronunciado). A função foi fornecida nos elementos de compartilhamento do FHIR a seguir:

-   [Esquema do paciente](https://www.hl7.org/fhir/patient.html) cobre as informações "quem" sobre um paciente.

-   [Esquema de observação](https://www.hl7.org/fhir/observation.html) cobre o elemento central nos serviços de saúde, usado para dar suporte ao diagnóstico, monitorar o progresso, determinar linhas de base e padrões e, inclusive, capturar características demográficas. 

-   [Esquema de consulta](https://www.hl7.org/fhir/encounter.html) cobre os tipos de consultas como ambulatório, emergência, atendimento domiciliar, internamento e consultas virtuais.

-   [Esquema de condição](https://www.hl7.org/fhir/condition.html) cobre informações detalhadas sobre uma condição, problema, diagnóstico ou outro evento, situação, assunto ou conceito clínico que aumentou para um nível de preocupação.  



### <a name="event-grid"></a>Grade de Eventos


A solução dá suporte para a Grade de Eventos do Azure, um único serviço para gerenciar o roteamento de todos os eventos de qualquer origem para qualquer destino, fornecendo:

-   [Segurança e autenticação](/azure/event-grid/security-authentication)

-   [Controle de acesso baseado em função](/azure/event-grid/security-authentication#management-access-control) para várias operações de gerenciamento, como listar assinaturas de evento, criar novas e gerar chaves

-   Auditoria

## <a name="store"></a>ARMAZENAR 

### <a name="sql-database-and-server"></a>Servidor e Banco de Dados SQL 


-   [TDE (Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) fornece criptografia em tempo real e descriptografia de dados armazenados no Banco de Dados SQL do Microsoft Azure, usando uma chave armazenada no Azure Key Vault.

-   [Avaliação de Vulnerabilidade de SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) é uma ferramenta fácil de configurar que pode descobrir, rastrear e corrigir potenciais vulnerabilidades do banco de dados.

-   [Detecção de ameaças do Banco de Dados SQL](/azure/sql-database/sql-database-threat-detection) habilitada.

-   [Auditoria do Banco de Dados SQL](/azure/sql-database/sql-database-auditing) habilitada.

-   [Log de Diagnóstico e métricas do Banco de Dados SQL](/azure/sql-database/sql-database-metrics-diag-logging) habilitado.

-   [Regras de firewall do nível do banco de dados e servidor](/azure/sql-database/sql-database-firewall-configure) foram reforçadas.

-   [Colunas Always Encrypted](/azure/sql-database/sql-database-always-encrypted-azure-key-vault) são utilizadas para proteger o nome e sobrenome dos pacientes.
    Além disso, a criptografia da coluna do banco de dados também utiliza o Azure Active Directory para autenticar o aplicativo no Banco de Dados SQL do Microsoft Azure.

-   O acesso ao Banco de Dados SQL e ao SQL Server está configurado de acordo com o princípio do mínimo privilégio.

-   Apenas os endereços IP necessários são permitidos através do firewall do SQL.

### <a name="storage-accounts"></a>Contas de armazenamento


-   [Os dados em movimento são transferidos usando apenas TLS/SSL](/azure/storage/common/storage-require-secure-transfer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

-   Acesso anônimo não é permitido para contêineres.

-   Regras de alerta são configuradas para acompanhar atividades anônimas.

-   HTTPS é necessário para acessar os recursos da conta de armazenamento.

-   Dados da solicitação de autenticação são registrados e monitorados.

-   Dados no armazenamento de Blobs são criptografados em repouso.

## <a name="analyze"></a>ANALISAR

### <a name="machine-learning"></a>Machine Learning


-   [Registro em log está habilitado](/azure/machine-learning/studio/web-services-logging) para serviços Web do Machine Learning.
- usar [Machine Learning](/azure/machine-learning/desktop-workbench/experimentation-service-configuration) Workbench requer o desenvolvimento de experimentos, o que fornece a capacidade de prever um conjunto de soluções. [Integrar o Workbench](/azure/machine-learning/desktop-workbench/using-git-ml-project) pode ajudar a agilizar o gerenciamento de experimentos.

## <a name="security"></a>SEGURANÇA

### <a name="azure-security-center"></a>Central de Segurança do Azure
- A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) permite uma exibição centralizada do estado da segurança de todos os seus recursos do Azure. Você pode verificar rapidamente se os controles de segurança apropriados estão em vigor e configurados de maneira correta, bem como identificar com rapidez os recursos que exigem atenção. 

- O [Assistente do Azure](/azure/advisor/advisor-overview) é um consultor de nuvem personalizado que ajuda a seguir as melhores práticas para otimizar as implantações do Azure. Ele analisa a telemetria de uso e configuração do recurso e, depois, recomenda soluções que podem ajudar você a melhorar a economia, o desempenho, a alta disponibilidade e a segurança de seus recursos do Azure.

### <a name="application-insights"></a>Application Insights
- O [Application Insights](/azure/application-insights/app-insights-overview) é um serviço APM (Gerenciamento de Desempenho de Aplicativos) extensível para desenvolvedores da Web em várias plataformas. Use-o para monitorar seu aplicativo Web online. Ele detecta anomalias de desempenho. Ele inclui ferramentas de análise avançadas para ajudar você a diagnosticar problemas e entender o que os usuários realmente fazem com seu aplicativo. Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo.

### <a name="azure-alerts"></a>Alertas do Azure
- [Os alertas oferecem um método de monitoramento de serviços do Azure e permitem configurar as condições sobre dados. Os alertas também fornecem notificações quando uma condição de alerta corresponder aos dados de monitoramento.

### <a name="operations-management-suite-oms"></a>OMS (Microsoft Operations Management Suite)
O [Microsoft Operations Management Suite (também conhecido como OMS)](/azure/operations-management-suite/operations-management-suite-overview) é uma coleção de serviços de gerenciamento.

-   Espaço de trabalho está habilitado para a Central de Segurança

-   Espaço de trabalho está habilitado para Monitoramento de Carga de Trabalho

-   Monitoramento de Carga de Trabalho está habilitado para:

    -   Identidade e Acesso

    -   Segurança e Auditoria

    -   Azure SQL DB Analytics

    -   Solução de [Análise do WebApp do Azure](/azure/log-analytics/log-analytics-azure-web-apps-analytics)

    -   Análise do Cofre de Chaves

    -   Controle de Alterações

-   [Conector do Application Insights (versão prévia)](/azure/log-analytics/log-analytics-app-insights-connector) está habilitado

-   [Análise do Log de Atividades](/azure/log-analytics/log-analytics-activity) está habilitada
