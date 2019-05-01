---
title: Proteger arquitetura de computação do Azure
description: Esta é uma arquitetura de referência para uma arquitetura de rede de Perímetro de nível empresarial, utilizando dispositivos de rede Virtual e outras ferramentas. Essa arquitetura foi projetada para atender Secure Cloud computação arquitetura requisitos do departamento de defesa funcionais. No entanto, ele pode ser utilizado para qualquer organização. Essa referência inclui duas opções automatizadas usando soluções da Citrix ou F5.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: f2e3d72db3f29dbc6d03b3259acb18daf684fb12
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917600"
---
# <a name="secure-azure-computing-architecture"></a>Proteger arquitetura de computação do Azure

Um número de clientes do DoD implantar cargas de trabalho do Azure aumenta rapidamente ficaram perguntando pelas diretrizes ao configurar redes virtuais seguras e as ferramentas de segurança e serviços estipulados pelo prática e padrões do DoD. DISA publicado a [documento de requisitos funcionais de arquitetura de computação de nuvem de seguro (SCCA)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) em 2017. SCCA descreve os objetivos funcionais para a proteção de defesa contra informações de sistema da rede (DISN) e pontos de conexão do provedor de nuvem comercial e como os aplicativos de nuvem segura de proprietários no limite da conexão de missão. É obrigatório que cada entidade do DoD que se conecta à nuvem comercial segue as diretrizes estabelecidas no FRD SCCA.
 
Há quatro componentes do SCCA. O limite de ponto de acesso na nuvem (BCAP), a pilha de segurança do Datacenter Virtual (VDSS), o Datacenter Virtual (VDMS) de serviços gerenciados e confiáveis o Gerenciador de credenciais de nuvem (TCCM). Microsoft desenvolveu uma solução que atenderá aos requisitos SCCA para IL4 e IL5 cargas de trabalho em execução no Azure. Essa solução específica do Azure é chamada de arquitetura de computação de Azure de seguro (SACA). Os clientes que implantam SACA estará em conformidade com o FRD SCCA e permitirá que os clientes do DoD mover cargas de trabalho para o Azure depois de conectado. 

Enquanto as arquiteturas e diretrizes de SCCA são específicas para os clientes do DoD, as revisões mais recentes de SACA também ajudará a clientes civis estão em conformidade com diretrizes de conexão (TIC) de internet confiável, bem como os clientes comerciais que desejam implementar uma DMZ segura para protege seus ambientes do azure.


## <a name="secure-cloud-computing-architecture-components"></a>Componentes de arquitetura de computação em nuvem seguros

**BCAP**

A finalidade de BCAP é proteger o DISN de ataques originados no ambiente de nuvem. BCAP irá realizar a detecção de intrusão e prevenção, bem como filtrar o tráfego não autorizado. Esse componente pode estar localizado junto com outros componentes do SCCA. É altamente recomendável que esse componente é implantado usando o hardware físico. Abaixo você encontrará a lista de requisitos de segurança BCAP.

***Requisitos de segurança BCAP***

![Matriz de requisitos BCAP](media/bcapreqs.png)


**VDSS**

A finalidade de VDSS é proteger os aplicativos do DoD missão proprietário que são hospedados no Azure. VDSS executa a maior parte das operações de segurança a SCCA. Ele realizará a inspeção do tráfego para proteger os aplicativos em execução no Azure. Esse componente pode ser fornecido dentro de seu ambiente do Azure.

***Requisitos de segurança VDSS***

![Matriz de requisitos VDSS](media/vdssreqs.png)

**VDMS**

A finalidade de VDMS é fornecer a segurança do host, bem como serviços do Centro de dados compartilhados. As funções de VDMS podem executar no hub de seu SCCA ou o proprietário de missão pode implantar partes em sua própria assinatura do Azure específica. Esse componente pode ser fornecido dentro de seu ambiente do Azure.

***Requisitos de segurança VDMS***

![Matriz de requisitos VDMS](media/vdmsreqs.png)


**TCCM**

TCCM é uma função de negócios. Essa pessoa será responsável por gerenciar a SCCA. Suas obrigações incluem o estabelecimento de planos e políticas de acesso da conta para o ambiente de nuvem, garantindo a identidade e gerenciamento de acesso está operando corretamente e para manter o plano de gerenciamento de credencial de nuvem. Essa pessoa é indicada pelo autorizando oficial. O BCAP, VDSS e VDMS fornecerá as funcionalidades necessárias para o TCCM realizar suas funções de trabalho.

***Requisitos de segurança TCCM***

![Matriz de requisitos TCCM](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>Considerações de planejamento e os componentes SACA 

A arquitetura de referência SACA é projetada para implantar os componentes VDSS e VDMS no azure, bem como habilitar o TCCM. Essa arquitetura é modular, o que significa que todas as partes de VDSS e VDMS podem residir em um hub centralizado ou alguns dos controles podem ser atendidos no espaço de proprietário de missão ou até mesmo localmente. A recomendação da nossa equipe da Microsoft é colocalizar os componentes VDSS e VDMS em uma central Virtual Net que todos os proprietários de missão podem se conectar por meio do. O diagrama a seguir ilustra a nossa arquitetura recomendada. 


![Diagrama de arquitetura de referência de SACA](media/sacav2generic.png)

Ao planejar sua estratégia de conformidade SCCA e arquitetura técnica, há muitas coisas a considerar. É importante que os tópicos a seguir são levados em consideração desde o início, pois todos os clientes precisará explicar isso. Os tópicos a seguir foram questões que surgem com clientes do DoD reais e tendem a diminuir o planejamento e a execução. 

- Quais BCAP será usado por sua organização?
    - DISA BCAP
        - DISA tem dois BCAPs operacionais no Pentágono e autoridade de certificação Camp Roberts, com um terceiro disponível online em breve. 
        - BCAPs do DISA todos os tem circuitos de ExpressRoute no Azure, que pode ser aproveitado por clientes do DoD para conectividade. 
        - DISA já tem uma sessão do Microsoft Peering de nível empresarial para os clientes do DoD que deseja inscrever-se às ferramentas de SaaS da Microsoft, como o Office 365. Usando DISA BCAP, você pode habilitar a conectividade e o emparelhamento à sua instância SACA. 
    - Criar seu próprio BCAP
        - Isso exigiria a concessão de espaço em um centro de dados colocalizadas e configurar um circuito do ExpressRoute para o Azure. 
        - Essa opção exigirá a aprovação adicional 
        - Devido a uma aprovação adicional e uma compilação física out, essa opção leva mais tempo. 
    - A Microsoft recomenda utilizar o BCAP DISA. Essa opção estiver disponível prontamente, foi construído na redundância e já tem clientes operando nele atualmente em produção.
- Espaço IP roteável DoD
    - Você precisará usar o espaço IP roteável DoD em sua borda. A opção para NAT aquelas para o espaço IP privado no Azure está disponível.  
    - Entre em contato com o NIC do DoD para obter o espaço de IP, ele será necessário como parte do seu envio SNAP com DISA. 
    - Se você planeja NAT para o espaço de endereço privado no Azure, será necessário um mínimo de um/24 a sub-rede do espaço de endereço atribuído da NIC para cada região em que você planeja implantar SACA. 
- Redundância 
    - A Microsoft sugere que você implante uma instância SACA em pelo menos duas regiões. Nuvem do DoD, isso significaria que implantá-lo em ambas as regiões do DoD disponíveis. 
    - Também é aconselhável que você se conectar a pelo menos dois BCAPs por meio de circuitos de ExpressRoute separados. Ambas as rotas do Express, em seguida, pode ser vinculadas a instância SACA de cada região. 
- Requisitos específicos do componente do DoD
    - Sua organização tem requisitos específicos fora os requisitos de SCCA? (Algumas organizações têm requisitos específicos de IPS)
- SACA é uma arquitetura modular  
    - Use somente os componentes que você precisa para seu ambiente. 
        - Implante as NVAs em uma camada única ou multicamada
        - Integrado IPS ou Traga sua própria IPS
- Nível de impacto de DoD de seus aplicativos e dados
    - Se houver a possibilidade de aplicativos em execução em nossas regiões IL5, é recomendável que você crie sua instância SACA no IL5. A instância pode ser usada na frente de aplicativos de IL4, bem como IL5. Porém, uma instância de SACA IL4 na frente de um aplicativo IL5 provavelmente não receberá capacitação. 
- Qual fornecedor de dispositivo de rede Virtual será usado para VDSS?
    - Como mencionado anteriormente, essa referência SACA pode ser criada usando uma variedade de dispositivos e serviços do Azure. No entanto, temos os modelos de solução automatizada para implantar a arquitetura SACA com F5 e Citrix. Essas soluções serão abordadas em mais detalhes abaixo. 
- Quais serviços do Azure você usará?
    - Há serviços do Azure que podem atender aos requisitos em torno do log analytics, a proteção baseada em host e a funcionalidade de IDS. No entanto, é possível que alguns serviços não estão geralmente disponíveis em nossas regiões IL5. Isso pode levar à necessidade de usar algumas ferramentas de terceiros 3ª se esses serviços do Azure não podem atender às suas necessidades. Você precisará examinar quais ferramentas são confortável e a possibilidade de usar a capacidade das ferramentas nativa do Azure. 
    - Ele é a recomendação da Microsoft que você use quantas ferramentas nativas do Azure conforme eles são criados com base na segurança de nuvem e integram perfeitamente com o restante da plataforma Azure possíveis. Abaixo está uma lista de ferramentas nativas do Azure que podem ser aproveitados para atender a vários requisitos de SCCA. 
        - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
        - [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) 
        - [Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
        - [Cofre da Chave do Azure](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
        - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
        - [Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/overview)
        - [Firewall do Azure](https://docs.microsoft.com/azure/firewall/overview) 
        - [Porta da frente do Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
        - [Grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview)
        - [Proteção contra DDoS do Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
        - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Dimensionamento
    - Um exercício de dimensionamento precisará ser concluída. Você precisará examinar o número de conexões simultâneas, que talvez você tenha por meio de instância SACA, bem como os requisitos de taxa de transferência de rede. 
    - Isso é uma etapa crítica, pois ele irá ajudar a dimensionar as VMs, bem como ajudar a identificar as licenças que serão necessárias a partir de vários fornecedores que você usará em sua instância SACA. 
    - Uma análise de custo BOM não pode ser feita sem esse exercício de dimensionamento, também é importante garantir que tudo o que é dimensionado corretamente para permitir o melhor desempenho. 


## <a name="most-common-deployment-scenario"></a>Cenário de implantação mais comuns

A Microsoft tem vários clientes que já passaram por completo de implantação ou pelo menos os estágios de seus ambientes de SACA de planejamento. Isso nos permitiu obter informações sobre o cenário mais comum de implantação. O diagrama a seguir ilustra a arquitetura mais comuns. 


![Diagrama de arquitetura de referência de SACA](media/sacav2commonscenario.png) 


Como você pode ver no diagrama, os clientes do DoD normalmente inscrever-se a dois do BCAPs DISA, um desses vidas na Costa Oeste e o outro reside na Costa Leste. Um par de privado de ExpressRoute está habilitado para o Azure em cada local DISA BCAP. Esses pares de ExpressRoute, em seguida, são vinculados ao Gateway de rede Virtual no DoD Leste e regiões do DoD Central do Azure. Uma instância SACA é implantada na região DoD Leste e Azure Central do DoD e todas as entrada e saída fluxos de tráfego por meio dele e para a conexão de rota expressa para o BCAP DISA. 

Missão proprietário aplicativos, em seguida, escolha quais regiões do Azure, eles serão implantar seus aplicativos em e use emparelhamento de rede Virtual para conectar seu aplicativo do rede Virtual para a rede Virtual de SACA. Força todo o tráfego por meio de instância VDSS o túnel. 

Essa arquitetura é altamente recomendável pela Microsoft, pois ele atenderá aos requisitos de SCCA, é altamente disponível e escalonável com facilidade e simplifica a implantação e gerenciamento.

## <a name="automated-saca-deployment-options"></a>Opções de implantação automatizadas de SACA

 Anteriormente, mencionamos que a Microsoft estabeleceu uma parceria com fornecedores de dois para criar um modelo de infraestrutura SACA automatizado. Os dois modelos implantará os seguintes componentes do Azure. 

- Rede Virtual de SACA
    - Subrede de gerenciamento
        - Em que o gerenciamento de VMs e serviços são implantados (saltar caixas)
        - VDMS sub-rede
            - Em que as VMs e serviços usados para VDMS são implantados
        - Subredes não confiáveis e confiáveis 
            - Em que os dispositivos virtuais são implantados
        - Sub-rede do Gateway
            - Onde o Gateway de ExpressRoute será implantado
- Máquinas de virtuais de caixa de salto de gerenciamento
    - Usado para gerenciamento fora de banda do ambiente.
- Soluções de Virtualização de Rede
    - Citrix ou F5, dependendo do modelo que você implanta.
- IPs Públicos
    - Usado para o front-end até que o ExpressRoute é colocado online. Esses IPs converterá no back-end, o espaço de endereço privado do Azure
- Tabelas de Rotas 
    - Aplicada durante a automação, essas tabelas de rota force por túnel todo o tráfego por meio do dispositivo virtual
- Balanceadores de carga do Azure - SKU Standard
    - Eles são usados para balancear carga de tráfego entre os dispositivos
- Grupos de segurança de rede
    - Usado para controlar quais tipos de tráfego podem atravessar para determinados pontos de extremidade


**Implantação do Citrix SACA**

Citrix criou um modelo de implantação que implanta duas camadas de dispositivos do Citrix ADC altamente disponíveis. Essa arquitetura atende aos requisitos de VDSS. 

![Diagrama de SACA Citrix](media/citrixsaca.png)


Documentação do Citrix e script de implantação pode ser encontrado [aqui.](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)


 **Implantação de F5 SACA**

F5 criou dois modelos de implantação separados que abrangem duas arquiteturas diferentes. O primeiro deles tem apenas uma camada de dispositivos de F5 em uma configuração de ativo-ativo altamente disponível. Essa arquitetura atende aos requisitos do VDSS. A segunda adiciona uma segunda camada de F5s de ativo-ativo altamente disponíveis. A finalidade dessa camada a segunda é permitir que os clientes adicionem seus próprios IPS separada da F5 entre as camadas de F5. Nem todos os componentes do DoD tenham prescrito para uso IPS específicos. Se esse for o caso, a única camada de dispositivos de F5 funcionará para mais desde que a arquitetura inclui IPS nos dispositivos F5.  

![Diagrama de SACA Citrix](media/f5saca.png)

Documentação de F5 e script de implantação pode ser encontrado [aqui.](https://github.com/f5devcentral/f5-azure-saca) 












