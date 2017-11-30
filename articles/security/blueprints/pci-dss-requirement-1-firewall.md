---
title: "Plano gráfico de processamento de pagamento do Azure - Requisitos de Firewall"
description: Requisito 1 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: b1935d88-acae-42f9-bc25-bb0766f876ab
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 995ecd5ef876695145fc6313aba2a46d2cc085cc
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="firewall-requirements-for-pci-dss-compliant-environments"></a>Requisitos de Firewall para ambientes em conformidade com PCI DSS 
## <a name="pci-dss-requirement-1"></a>Requisito 1 de PCI DSS

**Instalar e manter uma configuração de firewall para proteger os dados de titular do cartão**

> [!NOTE]
> Esses requisitos são definidos pelo [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) como parte do [Padrão de Segurança de Dados PCI (PCI DSS), Versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre procedimentos de teste e diretrizes para cada requisito.

Os firewalls são dispositivos que controlam o tráfego de computador permitido entre as redes de uma entidade (internas) e as redes não confiáveis (externas), bem como o tráfego para dentro e para fora de áreas mais restritas dentro das redes confiáveis internas da entidade. O ambiente de dados do titular do cartão é um exemplo de uma área mais restrita dentro da rede confiável de uma entidade.
Um firewall examina todo o tráfego de rede e bloqueia as transmissões que não atendem aos critérios de segurança especificados.
Todos os sistemas devem ser protegidos contra acesso não autorizado de redes não confiáveis, seja entrando-se no sistema pela Internet como comércio eletrônico, funcionário acessando a Internet por meio de navegadores de desktop, funcionário acessando email, conexões dedicadas como conexões entre empresas, por meio de redes sem fio ou seja via outras fontes. Muitas vezes, caminhos aparentemente insignificantes para e de redes não confiáveis podem fornecer sendas desprotegidas a sistemas principais. Os firewalls são um mecanismo de proteção principal para qualquer rede de computadores.
Outros componentes do sistema podem fornecer funcionalidade de firewall, desde que cumpram os requisitos mínimos para firewalls conforme definido no Requisito 1. Quando outros componentes do sistema são usados no ambiente de dados do titular do cartão para fornecer a funcionalidade de firewall, esses dispositivos devem ser incluídos no escopo e na avaliação do Requisito 1.

## <a name="pci-dss-requirement-11"></a>Requisito 1.1 de PCI DSS

**1.1** Estabeleça e implemente o firewall e outros padrões de configuração de roteador que incluam o seguinte (consulte os requisitos 1.1.1 a 1.1.7).


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore fornece firewall do CDE usando o isolamento de PaaS, e uma implementação do Ambiente do serviço de aplicativo garante que a entrada e a saída de dados do CDE seja protegida.<br /><br />Um [Ambiente do serviço de aplicativo (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) é um plano de serviço Premium usado por motivos de conformidade. Para obter mais informações sobre controles do ASE, consulte [Diretriz de PCI - Ambiente do Serviço de Aplicativo](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-111"></a>Requisito 1.1.1 de PCI DSS

**1.1.1** Um processo formal para a aprovação e o teste de todas as conexões de rede e alterações nas configurações de firewall e de roteador


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Uma instância da Contoso Webstore estabelece um modelo de DevOps CI/CD para garantir que todas as alterações sejam gerenciadas corretamente. A [Operations Management Suite (OMS)](/azure/operations-management-suite/) fornece um amplo registro em log das alterações. A precisão das alterações pode ser examinada e verificada. Para obter instruções mais específicas, consulte [Diretriz de PCI - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).<br /><br />A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) permite uma exibição centralizada do estado da segurança de todos os seus recursos do Azure. Você pode verificar rapidamente se os controles de segurança apropriados estão em vigor e configurados de maneira correta, bem como identificar com rapidez os recursos que exigem atenção.|



### <a name="pci-dss-requirement-112"></a>Requisito 1.1.2 de PCI DSS

**1.1.2** O diagrama de rede atual que identifica todas as conexões entre o ambiente de dados do titular do cartão e outras redes, incluindo redes sem fio

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Consulte a documentação de design e arquitetura de referência da Contoso Webstore como parte do padrão de instalação da solução.|



### <a name="pci-dss-requirement-113"></a>Requisito 1.1.3 de PCI DSS

**1.1.3** O diagrama atual que mostra todos os fluxos de dados de cartão de crédito entre sistemas e redes

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Consulte o DFD da Contoso Webstore e o Modelo de risco.|



### <a name="pci-dss-requirement-114"></a>Requisito 1.1.4 de PCI DSS

**1.1.4** Os requisitos de um firewall em cada conexão de Internet e entre qualquer zona desmilitarizada (DMZ) e a zona da rede interna

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure usa dispositivos de proteção de limite como gateways, ACLs de rede e firewalls do aplicativo, para controlar as comunicações em limites externos e internos no nível da plataforma. O cliente, em seguida, os configura de acordo com suas especificações e requisitos. O Microsoft Azure filtra a comunicação ao entrar na plataforma. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore fornece um DMZ usando um isolamento de PaaS, e uma implementação do Ambiente do serviço de aplicativo garante que a entrada e a saída de dados do CDE seja protegida.<br /><br />Um [Ambiente do serviço de aplicativo (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) é um plano de serviço Premium usado por motivos de conformidade. Para obter mais informações sobre controles do ASE, consulte [Diretriz de PCI - Ambiente do Serviço de Aplicativo](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-115"></a>Requisito 1.1.5 de PCI DSS

**1.1.5** Descrição de grupos, funções e responsabilidades para o gerenciamento de componentes de rede

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa o [Controle de Acesso Baseado em Função do Azure (RBAC)](/azure/active-directory/role-based-access-control-configure) para isolar as funções de usuário. O RBAC permite o gerenciamento de acesso detalhado para o Azure. Existem configurações específicas para o acesso de assinatura e o acesso do Azure Key Vault.|



### <a name="pci-dss-requirement-116"></a>Requisito 1.1.6 de PCI DSS

**1.1.6** A documentação da aprovação e justificativa de negócios para uso de todos os serviços, protocolos e portas permitidos, incluindo a documentação dos recursos de segurança implementados para esses protocolos considerados inseguros.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore abre apenas as portas e protocolos necessários durante o projeto de RA. Detalhes sobre o fluxo de dados podem ser vistos no DFD e no Modelo de Risco.|



### <a name="pci-dss-requirement-117"></a>Requisito 1.1.7 de PCI DSS

**1.1.7** Requisito para revisar os conjuntos de regras de roteador e firewall pelo menos a cada seis meses

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Na Contoso Webstore, os conjuntos de regras de firewall são analisados para garantir que nenhuma regra desnecessária ou não usada seja incluída. Por design, a demonstração é implantada com privilégios mínimos, a menor ocupação de espaço do caminho.|



## <a name="pci-dss-requirement-12"></a>Requisito 1.2 de PCI DSS

**1.2** Crie configurações de firewall e de roteador que restrinjam as conexões entre redes não confiáveis e quaisquer componentes de sistema no ambiente de dados do titular do cartão. 

> [!NOTE]
> Uma "rede não confiável" é qualquer rede externa às redes que pertencem à entidade sob revisão, e/ou que esteja fora da capacidade de a entidade de controlar ou gerenciar.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | O CDE da Contoso Webstore é definido na documentação de implantação e da RA. As redes não confiáveis são negadas por design.|



### <a name="pci-dss-requirement-121"></a>Requisito 1.2.1 de PCI DSS

**1.2.1** Restrinja o tráfego de entrada e saída àquele que é necessário para o ambiente de dados do titular do cartão e especifique a negação de todos os outros tráfegos.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | O CDE da Contoso Webstore é definido na documentação de implantação e da RA. As redes não confiáveis são negadas por design. A demonstração da Contoso Webstore configura o firewall do aplicativo do Microsoft Azure para permitir que apenas intervalos de endereços IP especificados acessem os serviços do Microsoft Azure. A Contoso Webstore fornece um firewall de negação total em todos os limites do CDE. Todas as configurações são executadas durante a configuração inicial da implantação.

> [!NOTE]
> O Ambiente do Serviço de Aplicativo (ASE) é usado nesta solução para isolar o CDE. No entanto, é essencial que seu Assessor de Segurança Qualificado (QSA) avalie essa solução, pois o ASE implementa um isolamento de DMZ que permite que as conexões de saída sejam feitas pelo ASE. O PCI-DSS requer que todas as conexões de entrada e de saída não necessárias sejam bloqueadas. Para o ASE operar corretamente, o ASE estabelecerá conexões de saída como necessário conforme definido em ["Considerações de rede para um Ambiente do Serviço de Aplicativo"](/azure/app-service/app-service-environment/network-info). Os clientes devem avaliar as conexões de saída com o teu QSA antes de implantar a solução em um ambiente de produção para assegurar que atenderá aos requisitos. |



### <a name="pci-dss-requirement-122"></a>Requisito 1.2.2 de PCI DSS

**1.2.2** Projeta e sincronize os arquivos de configuração do roteador.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore fornece configurações sincronizadas para controles de rede nativa do Microsoft Azure.|



### <a name="pci-dss-requirement-123"></a>Requisito 1.2.3 de PCI DSS

**1.2.3** Instale os firewalls de perímetro entre todas as redes sem fio e o ambiente de dados do titular do cartão e configure esses firewalls para negar ou, se o tráfego for necessário para fins comerciais, permitir apenas o tráfego autorizado entre o ambiente sem fio e o ambiente de dados do titular do cartão.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore não tem nenhuma solução sem fio ou recurso habilitado.|



## <a name="pci-dss-requirement-13"></a>Requisito 1.3 de PCI DSS

**1.3** Proíba o acesso público direto entre a Internet e qualquer componente do sistema no ambiente de dados do titular do cartão.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure utiliza dispositivos de proteção de limite com base em host e em rede, como firewalls, balanceadores de carga e ACLs. Esses dispositivos usam mecanismos, como isolamento de VLAN, NAT e filtragem de pacote para separar o tráfego do cliente do tráfego de gerenciamento e da Internet. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore fornece, no momento da implantação, as configurações do firewall de aplicativo do Azure para permitir que apenas os intervalos de endereços IP especificados para acessar o site incluam as VMs do Azure do bastião no respectivo CDE.|



### <a name="pci-dss-requirement-131"></a>Requisito 1.3.1 de PCI DSS

**1.3.1** Implemente uma DMZ para limitar o tráfego de entrada a somente os componentes de sistema que fornecem serviços, protocolos e portas de acesso público autorizados.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A DMZ implementada pela Contoso Webstore garante que apenas os serviços autorizados possam se conectar ao CDE.|



### <a name="pci-dss-requirement-132"></a>Requisito 1.3.2 de PCI DSS

**1.3.2** Limite o tráfego de Internet de entrada a endereços IP na DMZ.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A DMZ implementada pela Contoso Webstore garante que apenas os serviços autorizados possam se conectar ao CDE.|



### <a name="pci-dss-requirement-133"></a>Requisito 1.3.3 de PCI DSS

**1.3.3** Implemente as medidas antifalsificação para detectar e impedir que endereços IP de origem forjados entrem na rede. (Por exemplo, bloqueie o tráfego proveniente da Internet com um endereço de origem interna.)

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure implementa a filtragem de rede para evitar tráfego falsificado e restringir o tráfego de entrada e saída a componentes de plataforma confiáveis. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável.|



### <a name="pci-dss-requirement-134"></a>Requisito 1.3.4 de PCI DSS

**1.3.4** Não permita o tráfego de saída não autorizado do ambiente de dados do titular do cartão para a Internet.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A arquitetura do Contoso Webstore impede que o tráfego de saída não autorizado desde o ambiente no escopo para a Internet. Isso é feito configurando-se ACLs do tráfego de saída para protocolos e portas aprovadas no Microsoft Azure. Esses controles incluem acesso ao CDE no banco de dados do SQL Server. <br /><br />Uma instância de banco de dados SQL de PaaS é usada para exibir as medidas de segurança de banco de dados. Para saber mais, confira [Diretriz de PCI – Banco de Dados SQL do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-135"></a>Requisito 1.3.5 de PCI DSS

**1.3.5** Permita que apenas as conexões "estabelecidas" entrem na rede.


**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure implementa a filtragem de rede para evitar tráfego falsificado e restringir o tráfego de entrada e saída a componentes de plataforma confiáveis. A rede do Microsoft Azure é segregada para separar o tráfego de cliente do tráfego de gerenciamento. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Não aplicável.|



### <a name="pci-dss-requirement-136"></a>Requisito 1.3.6 de PCI DSS

**1.3.6** Coloque componentes do sistema que armazenam os dados do titular do cartão (como um banco de dados) em uma zona de rede interna, separada da DMZ e de outras redes não confiáveis.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure usa a segregação de rede e a NAT para separar o tráfego de cliente do tráfego de gerenciamento. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A arquitetura do Contoso Webstore impede que o tráfego de saída não autorizado desde o ambiente no escopo para a Internet. Isso é feito configurando-se ACLs do tráfego de saída para protocolos e portas aprovadas no Microsoft Azure. Esses controles incluem acesso ao CDE no banco de dados do SQL Server. <br /><br />Uma instância de banco de dados SQL de PaaS é usada para exibir as medidas de segurança de banco de dados. Para saber mais, confira [Diretriz de PCI – Banco de Dados SQL do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-137"></a>Requisito 1.3.7 de PCI DSS

**1.3.7** Não revele endereços IP privados e informações de roteamento a partes não autorizadas. 

> [!NOTE]
> Os métodos para ocultar o endereçamento IP podem incluir, mas não estão limitados a:
> - Conversão de endereços de rede (NAT).
> - A colocação de servidores que contêm os dados de titular do cartão por trás de firewalls/servidores proxy.
> - Remoção ou filtragem de anúncios de rota para redes privadas que empregam o endereçamento registrado.
> - Uso interno do espaço de endereço do RFC1918 em vez de endereços registrados.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure usa a Conversão de endereços de rede (NAT) e a segregação de rede para separar o tráfego de cliente do tráfego de gerenciamento. Os dispositivos do Azure são identificados exclusivamente por seus UUIDs e são autenticados usando-se o Kerberos. Os dispositivos de rede gerenciados pelo Azure são identificados pelo endereço IP de RFC 1918. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore coloca todos os dados de titulares de cartão atrás de firewalls/servidores proxy e usa o espaço de endereço do RFC1918 internamente.|



## <a name="pci-dss-requirement-14"></a>Requisito 1.4 de PCI DSS

**1.4** Instale o software de firewall pessoal ou funcionalidade equivalente todos os dispositivos de computação portáteis (inclusive os de propriedade da empresa e/ou dos funcionários) que se conectam à Internet quando estãor fora da rede (por exemplo, laptops usados pelos funcionários), e que também são usados para acessar o CDE. As configurações de firewall (ou equivalentes) incluem: -Definições de configuração específicas são definidas.
- O firewall pessoal (ou funcionalidade equivalente) está em execução ativamente.
- O firewall pessoal (ou funcionalidade equivalente) não pode ser alterado pelos usuários dos dispositivos de computação portáteis.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore não fornece a proteção dos dispositivos de usuário final. O [Microsoft Intune](https://www.microsoft.com/cloud-platform/microsoft-intune) pode ser usado para gerenciar os dispositivos móveis que sua força de trabalho usa para acessar os dados da empresa.|



## <a name="pci-dss-requirement-15"></a>Requisito 1.5 de PCI DSS

**1.5** Garanta que políticas de segurança e procedimentos operacionais para o gerenciamento de firewalls estejam documentados, em uso e sejam do conhecimento de todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore fornece, no momento da implantação, as configurações do firewall de aplicativo do Azure para permitir que apenas os intervalos de endereços IP especificados para acessar o site incluam as VMs do Azure do bastião no respectivo CDE.|




