<properties
   pageTitle="Segurança de rede e Serviços de Nuvem da Microsoft | Microsoft Azure"
   description="Conheça alguns dos principais recursos disponíveis no Azure para criar ambientes de rede seguros"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2015"
   ms.author="jonor;sivae"/>

# Segurança de rede e Serviços de Nuvem da Microsoft

Os Serviços de Nuvem da Microsoft entregam serviços e estrutura em larga escala, recursos de nível empresarial e várias opções de conectividade híbrida. Os clientes podem optar por acessar esses serviços através da Internet ou com a Rota Expressa, que fornece conectividade de rede privada. A plataforma Microsoft Azure permite que os clientes estendam facilmente sua infra-estrutura para a nuvem e criem arquiteturas com várias camadas. Para complementar os serviços da Microsoft, terceiros podem habilitar recursos avançados oferecendo serviços de segurança e dispositivos virtuais. Este white paper fornece uma visão geral sobre segurança e problemas de arquitetura que os clientes devem considerar ao usar os Serviços de Nuvem da Microsoft acessadas através da Rota Expressa e ao criar serviços seguros na Rede Virtual do Microsoft Azure.

## Início rápido
O gráfico lógico direcioná-lo a um exemplo específico das várias técnicas de segurança disponíveis na plataforma Microsoft Azure. Use-o para localizar rápida e precisamente o que é necessário ou leia o documento desde o início para obter uma explicação sobre as várias opções disponíveis. ![Fluxograma de opções de segurança][0]

[Exemplo 1: criar uma DMZ para proteger aplicativos com NSGs](#example-1-build-a-simple-dmz-with-nsgs)</br> [Exemplo 2: criar uma DMZ para proteger aplicativos com firewall e NSGs](#example-2-build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs)</br> [Exemplo 3: criar uma DMZ para proteger redes com firewall, UDR e NSG](#example-3-build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg)</br> [Exemplo 4: adicionando uma conexão híbrida com uma VPN de dispositivo virtual site a site](#example-4-adding-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br> [Exemplo 5: adicionando uma conexão híbrida com uma VPN Azure Gateway site a site](#example-5-adding-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn)</br> [Exemplo 6: adicionando uma conexão híbrida com a Rota Expressa](#example-6-adding-a-hybrid-connection-with-expressroute)</br> Exemplos para adicionar VNet a conexões VNet, de alta disponibilidade e de encadeamento de serviço serão adicionados a este documento nos próximos meses.

## Proteção de infraestrutura e conformidade da Microsoft
A Microsoft tomou uma posição de liderança dando suporte às iniciativas de conformidade exigidas pelos clientes corporativos. Seguem algumas das certificações de conformidade do Azure: ![Selos de conformidade do Azure][1]

Mais detalhes podem ser encontrados em: [http://azure.microsoft.com/support/trust-center/compliance/](http://azure.microsoft.com/support/trust-center/compliance/)

A Microsoft tem uma abordagem abrangente para proteger a infraestrutura de nuvem necessária para executar os serviços globais em larga escala. A infraestrutura de nuvem da Microsoft inclui hardware, software, redes, administrativas e equipe de operações, bem como data centers físicos.

![Recursos de segurança do Azure][2]

A abordagem acima fornece uma base segura para os clientes implantarem seus serviços na Microsoft Cloud. A próxima etapa é o design e a criação de uma arquitetura de segurança pelos clientes para proteger esses serviços.

## Arquiteturas tradicionais de segurança e DMZs (zonas desmilitarizadas)
Embora a Microsoft faça investimentos consideráveis para proteger a infraestrutura de nuvem, os clientes também devem proteger os serviços de nuvem e os grupos de recursos. Uma abordagem de segurança em várias camadas fornece a melhor defesa. Uma DMZ (zona de delimitação de segurança de rede) protege recursos da rede interna de uma rede não confiável. DMZ é um conceito conhecido na arquitetura de segurança de rede de TI empresarial, referindo-se às bordas ou partes da rede estabelecidas entre a Internet e a infraestrutura de TI da empresa protegida.

Em redes empresariais típicas, a infraestrutura básica é reforçada intensamente no perímetro, com várias camadas de dispositivos de segurança. O limite de cada camada consiste em dispositivos e pontos de imposição de políticas. Os dispositivos podem incluir: firewalls, prevenção de DDoS (ataque de negação de serviço distribuído), IDS/IPS (sistemas de detecção de intrusão ou sistemas de proteção), dispositivos VPN (redes virtuais privadas), etc. A imposição de políticas pode assumir a forma de políticas de firewall, ACLs ou roteamento específico. A primeira linha de defesa da rede, que aceita diretamente o tráfego de entrada da Internet, será uma combinação desses mecanismos para bloquear ataques e tráfego perigoso enquanto as solicitações legítimas adentram a rede. Esse tráfego será roteado diretamente aos recursos na DMZ. Esse recurso pode "falar" com recursos mais dentro da rede, passando pelo próximo limite de validação antes de ser roteado mais profundamente na rede. A camada mais externa é chamada DMZ porque essa parte da rede está exposta à Internet, geralmente com alguma forma de proteção em ambos os lados da DMZ. A figura a seguir mostra um exemplo de uma sub-rede DMZ individual em uma rede corporativa com dois limites de segurança (DMZ-Internet e DMZ VLAN Back-end).

![Uma DMZ em uma rede corporativa][3]

Há muitas arquiteturas usadas para implementar uma DMZ, de um simples do balanceador de carga na frente de um Web farm até uma DMZ com várias sub-redes e diferentes mecanismos em cada limite para bloquear o tráfego e proteger as camadas mais profundas da rede corporativa. Como a DMZ é criada depende das necessidades específicas da organização e da tolerância a riscos associada.

Conforme os clientes vão movendo suas cargas de trabalho para nuvens públicas, é essencial dar suporte a recursos semelhantes de arquitetura de DMZ no Azure para atender aos requisitos de conformidade e de segurança. Este documento fornece diretrizes sobre como os clientes podem criar um ambiente de rede seguro no Azure, concentrando-se na DMZ, mas envolvendo uma discussão abrangente sobre vários aspectos da segurança de rede, começando com as seguintes questões, dentre outras:

1.	Como pode ser criada uma DMZ no Azure?
2.	Quais são alguns dos recursos do Azure disponíveis para criar a DMZ?
3.	Como cargas de trabalho de back-end podem ser protegidas?
4.	Como a comunicação com a Internet é controlada para as cargas de trabalho no Azure?
5.	Como as redes locais podem ser protegidas contra implantações no Azure?
6.	Quando recursos de segurança nativos do Azure devem ser usados em vez de dispositivos ou serviços de terceiros?

O diagrama abaixo mostra várias camadas de segurança que o Azure fornece aos clientes, tanto nativos na plataforma Azure quanto através de recursos definidos pelo cliente:

![Arquitetura de segurança do Azure][4]

Vinda da Internet há a proteção DDoS do Azure vigiando ataques em grande escala contra o Azure. A derrubada dela levaria aos pontos de extremidade públicos definidos pelo cliente que são usados para determinar qual tráfego pode passar pelo serviço de nuvem para a rede virtual. O isolamento de rede virtual do Azure nativo garante o isolamento completo de todas as outras redes e garante que o tráfego flua somente através de métodos e caminhos configurados pelo usuário. Esses caminhos e métodos são a próxima camada onde NSG (grupos de segurança de rede), UDR (roteamento definido pelo usuário) e dispositivos de rede virtual podem ser usados para criar limites de segurança, incluindo DMZs, para proteger as implantações de aplicativo na rede protegida.

A próxima seção fornece uma visão geral das Redes Virtuais do Azure. As Redes Virtuais do Azure são criadas por clientes e as cargas de trabalho implantadas estão conectadas a elas. As Redes Virtuais são a base de todos os recursos de segurança de rede necessários para estabelecer uma DMZ e proteger as implantações dos clientes no Azure.

## Visão geral das máquinas virtuais do Azure
Antes que o tráfego da Internet possa acessar as Redes Virtuais do Azure, há duas camadas de segurança inerentes à plataforma Azure:

1.	**Proteção DDoS**: ataque de negação de serviço distribuído (DDoS) é uma camada da rede física do Azure que protege a própria plataforma Azure contra ataques em larga escala baseados na Internet, em que invasores usam vários nós "bot" em uma tentativa de sobrecarregar um serviço de Internet. O Azure tem uma malha de proteção sólida DDoS em todas as conexões de entrada de Internet. Essa camada de proteção DDoS não tem atributos configuráveis do usuário e não é acessível ao cliente. Isso protege o Azure, como plataforma, de ataques em grande escala, mas não protege diretamente o aplicativo cliente individual. Camadas adicionais de resiliência podem ser configuradas pelo cliente contra um ataque localizado. Por exemplo, se um cliente foi atacado com DDoS em grande escala em um ponto de extremidade público, o Azure bloqueará conexões a esse serviço. Um cliente pode realizar o failover a outra rede virtual ou ponto de extremidade de serviço não envolvidos com o ataque para restaurar o serviço. Observe que enquanto um cliente A pode ser afetado naquele ponto de extremidade, nenhum outro serviço fora do ponto de extremidade é afetado. Além disso, outros clientes e serviços não veem nenhum impacto vindo desse ataque.
2.	**Pontos de extremidade de serviço**: os pontos de extremidade permitem que os serviços de nuvem ou grupos de recursos tenham endereços IP e portas públicas (na Internet) expostos, o ponto de extremidade fará a NAT do tráfego para o endereço e porta internos na Rede Virtual do Azure. Esse é o caminho principal por onde o tráfego externo passa para a Rede Virtual do Azure. Os pontos de extremidade de serviço são configuráveis pelo usuário para determinar qual tráfego pode passar e como/para onde ele deve ser convertido na rede virtual. 

Depois que o tráfego alcança a rede virtual, há muitos recursos que entram em ação, já que as redes virtuais do Azure são a base onde os clientes anexam as cargas de trabalho e onde se aplica o nível de segurança de rede básico. É uma rede privada (uma sobreposição de rede virtual) no Azure para clientes com os seguintes recursos e características:
 
1.	**Isolamento de tráfego**: uma rede virtual é o limite de isolamento de tráfego na plataforma Azure. As VMs em uma rede virtual não pode se comunicar diretamente com VMs em uma rede virtual diferente, mesmo que ambas as redes virtuais sejam criadas pelo mesmo cliente. Essa é uma propriedade vital que garante que as VMs e as comunicações do cliente permaneçam privadas em uma rede virtual. 
2.	**Topologia de multicamadas**: as redes virtuais permitem que os clientes definam a topologia de multicamadas alocando sub-redes e designando os espaços de endereço separados para elementos ou "camadas" diferentes de suas cargas de trabalho. Esses agrupamentos e topologias lógicos permitem que os clientes definam políticas de acesso diferentes baseadas nos tipos de carga de trabalho e também controlam fluxos de tráfego entre as camadas. 
3.	**Conectividade entre locais**: os clientes podem estabelecer a conectividade entre locais entre uma rede virtual e vários sites locais ou outras redes virtuais no Azure por meio de Gateways de VPN do Azure ou dispositivos de rede virtual de terceiros. O Azure dá suporte a VPNs site a site (S2S) usando protocolos padrão de IPsec/IKE e conectividade privada de Rota Expressa. 
4.	**Grupo de segurança de rede** (NSG) permite que os clientes criem regras (ACLs) para o nível desejado de granularidade: sub-redes virtuais, VMs individuais ou interfaces de rede. Os clientes podem controlar o acesso permitindo ou negando a comunicação entre as cargas de trabalho em uma rede virtual, de sistemas nas redes do cliente por meio de conectividade entre locais ou comunicação direta via Internet. 
5.	**Rotas definidas pelo usuário** (UDR) e **Encaminhamento IP** permitem que os clientes definam os caminhos de comunicação entre camadas diferentes em uma rede virtual. Os clientes podem implantar firewall, IDS/IPS e outros dispositivos virtuais, além de rotear o tráfego de rede por meio desses dispositivos de segurança para a aplicação, a auditoria e a inspeção das políticas de limites de segurança.
6.	**Dispositivos de rede virtual** no Azure Marketplace: dispositivos de segurança, como firewalls, balanceadores de carga e IDS/IPS (serviços de prevenção e de detecção de invasões) estão disponíveis no Azure Marketplace e na Galeria de imagens de VM. Os clientes podem implantar esses dispositivos em suas redes virtuais e, especificamente, em seus limites de segurança (incluindo as sub-redes DMZ) para concluir um ambiente de rede seguro com várias camadas.

Com esses recursos e funções, um exemplo de como uma arquitetura de DMZ poderia ser criada no Azure é o seguinte:

![Uma DMZ em uma Rede Virtual do Azure][5]

## Requisitos e características de DMZ
Esta seção descreve características e requisitos de uma DMZ no Azure. Conforme descrito anteriormente, a DMZ é projetada para ser o front-end da rede, fazendo a interface de comunicação diretamente da Internet. Os pacotes de entrada devem fluir por dispositivos de segurança, firewall, IDS, IPS e afins, na DMZ antes de alcançar os servidores back-end. Pacotes indo das cargas de trabalho para a Internet também podem fluir pelos dispositivos de segurança na DMZ para fins de auditoria, inspeção e imposição da política antes de sair da rede. Além disso, a DMZ também pode ser usada para hospedar os gateways de VPN entre locais entre redes virtuais de cliente e redes locais.

### Características da DMZ
Em relação à figura acima, Uma DMZ em uma Rede Virtual do Azure, algumas das características de uma boa DMZ são as seguintes:

- DMZ voltada para a Internet (Front-end):
    - A sub-rede DMZ em si é voltada para a Internet, comunicando-se diretamente com a Internet
    - IPs públicas, VIPs e/ou pontos de extremidade de serviço passam tráfego da internet para a rede e dispositivos de front-end
    - O tráfego de entrada da internet passa por dispositivos de segurança antes de outros recursos da rede de front-end
    - Se segurança de saída estiver habilitada, o tráfego passa por dispositivos de segurança, como a etapa final, antes de passar para a internet
- "Rede protegida" (Back-end): em direção à infraestrutura básica
    - Não há um caminho direto da Internet para a infraestrutura básica
    - Os canais para a infraestrutura básica devem percorrer dispositivos de segurança, como NSGs, firewalls ou dispositivos VPN na DMZ
    - Outros dispositivos na DMZ NÃO DEVEM ligar a Internet à infraestrutura básica
    - Dispositivos de segurança tanto no lado da Internet quanto no lado da rede protegida da DMZ (por exemplo, os dois ícones de firewall exibidos na figura acima) podem ser um único dispositivo virtual com regras ou interfaces diferenciadas para o limite de internet e o de back-end (por exemplo, um dispositivo, logicamente separado, lidando com a carga em ambos os limites da DMZ)
- Outras práticas recomendadas e restrições comuns
    - As cargas de trabalho na DMZ não devem armazenam informações essenciais aos negócios
    - O acesso e as atualizações para configurações e implantações da DMZ são limitadas somente aos administradores autorizados

### Requisitos da DMZ
Para habilitar essas características, a lista a seguir fornece orientação sobre requisitos de rede virtual para implementar uma DMZ com êxito:

- Arquitetura de sub-rede: especifique a rede virtual, de modo que uma sub-rede inteira fique dedicada como DMZ, separada de outras sub-redes na mesma rede virtual. Isso garantirá o tráfego entre a DMZ e outras camadas de sub-rede internas ou privadas flua por meio de um firewall ou dispositivo virtual IDS/IPS nos limites da sub-rede com rotas definidas pelo usuário.
- O grupo de segurança de rede (NSG) para o DMZ: a própria sub-rede DMZ deve estar aberta para permitir a comunicação entre a DMZ e a Internet, mas isso não significa que os clientes devem ignorar os NSGs. Siga as práticas de segurança comuns para minimizar as superfícies de rede expostas à Internet, bloqueando os intervalos de endereço remoto permitidos para acessar as implantações e/ou os protocolos de aplicativo específicos e as portas que estão abertas. Observe que nem sempre será possível. Por exemplo, se os clientes têm um site externo no Azure, a DMZ deve permitir solicitações de entrada da Web de endereços IP públicos, mas só deve abrir as portas do aplicativo Web: TCP:80 e TCP:443.
- Tabela de roteamento para DMZ: a própria sub-rede DMZ deve ser capaz de se comunicar diretamente com a Internet, mas não deve permitir a comunicação direta de e para o back-end ou em redes locais sem passar por um dispositivo de firewall ou de segurança.
- Configuração de dispositivo de segurança DMZ: para rotear e inspecionar os pacotes entre a DMZ e o restante das redes protegidas, os dispositivos de segurança, como dispositivos IPS, IDS e firewall, podem ter vários locais de origem com NICs diferentes para a DMZ e para as sub-redes de back-end. As NICs na DMZ irão se comunicar diretamente de e para a Internet, com os NSGs correspondentes e a tabela de roteamento da DMZ. As NICs conectadas às sub-redes de back-end terão NSGs e tabelas de roteamento mais restritos das sub-redes correspondentes do back-end.
- Funcionalidade de dispositivo de segurança da DMZ: os dispositivos de segurança implantados na DMZ normalmente executam a seguinte funcionalidade:
    - Firewall: imposição de regras de firewall ou políticas de controle de acesso para solicitações de entrada
    - Detecção e prevenção de ameaças: detecta ameaças e atenua ataques da Internet
    - Auditoria e registro em log: mantém logs detalhados de auditoria e análise
    - Proxy invertido: redireciona solicitações de entrada correspondentes aos servidores de back-end mapeando e convertendo os endereços de destino nos dispositivos de front-end da DMZ, normalmente os firewalls, para os endereços de servidor de back-end reais
    - Encaminhar proxy: fornece NAT e também realiza auditoria para a comunicação iniciada de dentro da rede virtual para a Internet
    - Roteador: atua como o roteador para encaminhar tráfego de entrada de sub-rede e entre sub-redes dentro da rede virtual
    - Dispositivo VPN: atua como o local entre gateways de VPN para conectividade VPN entre locais entre redes locais do cliente e redes virtuais do Azure
    - Servidor VPN: atua como servidores VPN para aceitar clientes VPN que se conectam a redes virtuais do Azure

>[AZURE.TIP]Mantém as pessoas autorizadas a acessar as peças de segurança da DMZ completamente separadas das pessoas autorizadas como administradores de operações/implantação/desenvolvimento de aplicativos. Manter esses grupos separados permite uma diferenciação de deveres e impede que uma única pessoa ignore controles de segurança de aplicativos e de rede.

### Perguntas a serem feitas durante a criação de limites de rede
Ao se tratar de "Redes do Azure" nesta seção, a menos que especificamente mencionadas, todas as redes (redes, redes virtuais ou sub-redes) fazem referência a redes virtuais privadas do Azure criadas por um administrador de assinatura e não envolvem as redes físicas subjacentes no Azure.

Além disso, as redes virtuais do Azure geralmente são usadas para estender redes locais tradicionais. É possível incorporar soluções de rede híbridas site a site ou Rota Expressa com arquiteturas de DMZ. Essa é uma consideração importante ao criar limites de segurança de rede e é discutida nas perguntas abaixo.

Portanto, ao criar uma rede com uma DMZ e vários limites de segurança essenciais, três perguntas devem ser respondidas.

#### 1) Quantos limites são necessários?
O primeiro ponto de decisão é decidir quantos limites de segurança são necessários em um determinado cenário:

- Um único limite: um no front-end da DMZ e outro entre a rede virtual e a Internet.
- Dois limites: um do lado da Internet da rede DMZ, outro entre a sub-rede DMZ e as sub-redes de back-end nas redes virtuais do Azure
- Três limites: do lado da internet da DMZ, entre a DMZ e as sub-redes de back-end e entre as sub-redes de back-end e a rede local
- Limites N: dependendo dos requisitos de segurança, não há limites para o número de limites de segurança que podem ser aplicados a uma determinada rede.

O número e tipo dos limites necessários irão variar dependendo da tolerância a riscos da empresa e o cenário específico que está sendo implementado. Geralmente, essa é uma decisão conjunta feita por vários grupos dentro de uma organização, geralmente uma equipe de risco e conformidade, uma equipe de rede/plataforma e uma equipe de desenvolvimento de aplicativos. As pessoas com conhecimento de segurança, os dados envolvidos e as tecnologias usadas devem pesar na decisão para garantir a postura de segurança apropriada em cada implementação.

>[AZURE.TIP]Use a menor quantidade de limites que satisfaçam os requisitos de segurança em uma determinada situação. Com mais limites, mais difíceis ficam as operações e a solução de problemas, bem como a sobrecarga do gerenciamento envolvido com a gestão de várias políticas de limites ao longo do tempo. No entanto, os limites insuficientes aumentam o risco. Encontrar o equilíbrio é fundamental.

![Rede híbrida com três limites de segurança][6]

A figura acima mostra uma visão de alto nível de rede de com três limites de segurança, com um limite entre a DMZ e a Internet, entre as sub-redes privadas de front-end e back-end do Azure e entre a sub-rende de back-end e a rede corporativa local.

#### 2) Onde estão localizados os limites?
Depois que o número de limites é decidido, onde implementá-los é o próximo ponto a se decidir. Em geral, há três opções; 1) usar um serviço intermediário baseado na Internet (por exemplo, WAF baseado em nuvem, que não é discutido neste documento), 2) usar recursos nativos e/ou dispositivos virtuais de rede no Azure, 3) usar dispositivos físicos na rede local.

Em redes puramente do Azure, as opções são recursos nativos do Azure (por exemplo, balanceadores de carga do Azure) ou dispositivos de rede virtual do ecossistema variado de parceiros do Azure (por exemplo, firewalls de ponto de verificação).

Se for necessário um limite entre o Azure e uma rede local, os dispositivos de segurança podem residir em um dos lados da conexão (ou em ambos). Assim, uma decisão deve ser tomada sobre o local para colocar o mecanismo de segurança.

Na figura acima, os limites Internet para DMZ e Frontend para Backend estão totalmente contidos no Azure e devem ser recursos nativos do Azure ou dispositivos de rede virtual. Os dispositivos de segurança no limite entre o Azure (sub-rede de back-end) e a rede corporativa podem estar no lado do Azure ou no lado local ou até uma combinação de dispositivos em ambos os lados. Pode haver vantagens e desvantagens relevantes em ambas as opções; portanto, elas devem ser levadas em consideração.

Por exemplo, o uso do mecanismo de segurança físico existente no lado da rede local. A favor: nenhum novo mecanismo é necessário, somente nova configuração. Contra: todo o tráfego deve voltar do Azure à rede local para ser visto pelo mecanismo de segurança. Assim, o tráfego do Azure para o Azure poderia incorrer em latência significativa e afetar a experiência do usuário e o desempenho do aplicativo se ele fosse forçado novamente à rede local para a imposição de política de segurança.

#### 3) Como os limites são implementados?
Cada limite de segurança provavelmente terá requisitos de recurso diferentes (por exemplo, IDS e regras de Firewall no lado da Internet da DMZ, mas somente ACLs entre a DMZ e a sub-rede de back-end). Decidir quais dispositivos usar dependerá de requisitos de cenário e de segurança. Os exemplos de DMZ 1, 2 e 3 abaixo abordam algumas opções que podem ser usadas. A análise dos recursos de rede nativos do Azure e dos dispositivos disponíveis no Azure do ecossistema de parceiros irá expor as inúmeras opções disponíveis para resolver praticamente qualquer cenário.

Outro ponto-chave de decisão de implementação é como se conectar à rede local com o Azure. Por meio do Gateway Virtual do Azure ou de um dispositivo de rede virtual. Essas opções estão descritas e são discutidas mais detalhadamente nos exemplos 4, 5 e 6 abaixo.

Além disso, talvez seja necessário tráfego VNet para VNet dentro do Azure; esses cenários são abordados detalhadamente nos exemplos 7 e 8 abaixo.

Depois de saber as respostas para as perguntas acima, a seção [Início Rápido](#fast-start) seção acima pode ajudar a identificar quais exemplos são mais apropriados para um determinado cenário.

## Criando limites de segurança com redes virtuais do Azure
### Exemplo 1: criar uma DMZ simples com NSGs
[Voltar ao Início Rápido](#fast-start) | [Instruções detalhadas de compilação para este exemplo][Example1]

![DMZ de entrada com NSG][7]

#### Descrição do ambiente
Neste exemplo, há uma assinatura que contém o seguinte:

- Dois serviços de nuvem: "FrontEnd001" e "BackEnd001"
- Uma rede virtual, “CorpNetwork”, com duas sub-redes, “FrontEnd” e “BackEnd”
- Um grupo de segurança de rede que é aplicado a ambas as sub-redes
- Um Windows Server que representa um servidor Web de aplicativos ("IIS01")
- Dois servidores Windows que representam servidores de back-end de aplicativos ("AppVM01", "AppVM02")
- Um servidor Windows que representa um servidor DNS ("DNS01")

Há mais detalhes para criar este exemplo (ele fornece scripts e um modelo ARM) localizados na página [instruções detalhadas de compilação][Example1].

#### Descrição do grupo de segurança de rede (NSG)
Neste exemplo, um grupo NSG é criado e carregado com seis regras.

>[AZURE.TIP]Em geral, você deve criar suas regras específicas "Permitir" primeiro e então as regras “Negar” mais genéricas por último. A prioridade dada determina quais regras são avaliadas primeiro. Quando o tráfego se aplicar a uma regra específica, nenhuma regra adicional será avaliada. As regras NSG podem se aplicar na direção de entrada ou de saída (na perspectiva da sub-rede).

Declarativamente, as regras a seguir estão sendo criadas para tráfego de entrada:

1.	O tráfego interno de DNS (porta 53) é permitido
2.	O tráfego de RDP (porta 3389) da Internet para qualquer VM é permitido
3.	O tráfego HTTP (porta 80) da Internet ao servidor Web (IIS01) é permitido
4.	Todo tráfego (todas as portas) de IIS01 para AppVM1 é permitido
5.	Todo o tráfego (todas as portas) da Internet para a Rede Virtual inteira (todas as sub-redes) é Negado
6.	Todo tráfego (todas as portas) da sub-rede Frontend para a sub-rede Backend é negado

Com essas regras associadas a cada sub-rede, se uma solicitação HTTP tiver entrado da Internet para o servidor Web, ambas as regras 3 (permitir) e 5 (negar) se aplicariam, mas já que a regra 3 tem uma prioridade maior, somente ela se aplicaria e a regra 5 não seria utilizada. Portanto, a solicitação HTTP seria permitida para o servidor Web. Se esse mesmo tráfego estivesse tentando acessar o servidor DNS01, a regra 5 (Negar) seria a primeira a ser aplicada e o tráfego não teria permissão para passar para o servidor. A regra 6 (Negar) impede que a sub-rede Frontend converse com a sub-rede Backend (exceto pelo tráfego permitido nas regras 1 e 4); isso protege a rede Backend caso um invasor comprometa o aplicativo Web na rede Frontend, pois ele teria acesso limitado à rede Backend “protegida” (somente para recursos expostos no servidor AppVM01).

Há uma regra de saída padrão que permite o tráfego de saída para a Internet. Neste exemplo, permitiremos o tráfego de saída e não modificaremos quaisquer regras de saída. Para bloquear o tráfego em ambas as direções, é necessário o roteamento definido pelo usuário; isso é explorado "Exemplo 3" abaixo.

#### Conclusão
Essa é uma maneira relativamente simples e direta de isolar a sub-rede de back-end do tráfego de entrada. Mais informações sobre este exemplo como as seguintes:

- Como criar essa DMZ com scripts do PowerShell
- Como criar essa DMZ com um modelo de ARM
- Descrições detalhadas de cada comando NSG
- Cenários de fluxo de tráfego detalhados mostrando como o tráfego é permitido ou negado em cada camada

podem ser encontrados na página [Instruções detalhadas de compilação][Example1].

### Exemplo 2: criar uma DMZ para proteger aplicativos com firewall e NSGs
[Voltar ao Início Rápido](#fast-start) | [Instruções detalhadas de compilação para este exemplo][Example2]

![DMZ de entrada com NVA e NSG][8]

#### Descrição do ambiente
Neste exemplo, há uma assinatura que contém o seguinte:

- Dois serviços de nuvem: "FrontEnd001" e "BackEnd001"
- Uma rede virtual, “CorpNetwork”, com duas sub-redes, “FrontEnd” e “BackEnd”
- Um único grupo de segurança de rede que é aplicado a ambas as sub-redes
- Um dispositivo virtual de rede, neste exemplo, um firewall, conectado à sub-rede Frontend
- Um Windows Server que representa um servidor Web de aplicativos ("IIS01")
- Dois servidores Windows que representam servidores de back-end de aplicativos ("AppVM01", "AppVM02")
- Um servidor Windows que representa um servidor DNS ("DNS01")

Há mais detalhes para criar este exemplo (ele fornece scripts e um modelo ARM) localizados na página [instruções detalhadas de compilação][Example2].

#### Descrição do grupo de segurança de rede (NSG)
Neste exemplo, um grupo NSG é criado e carregado com seis regras.

>[AZURE.TIP]Em geral, você deve criar suas regras específicas "Permitir" primeiro e então as regras “Negar” mais genéricas por último. A prioridade dada determina quais regras são avaliadas primeiro. Quando o tráfego se aplicar a uma regra específica, nenhuma regra adicional será avaliada. As regras NSG podem se aplicar na direção de entrada ou de saída (na perspectiva da sub-rede).

Declarativamente, as seguintes regras estão sendo criadas para o tráfego de entrada:

1.	O tráfego interno de DNS (porta 53) é permitido
2.	O tráfego de RDP (porta 3389) da Internet para qualquer VM é permitido
3.	Todo tráfego da internet (todas as portas) ao NVA (firewall) é permitido
4.	Todo tráfego (todas as portas) de IIS01 para AppVM1 é permitido
5.	Todo o tráfego (todas as portas) da Internet para a Rede Virtual inteira (todas as sub-redes) é Negado
6.	Todo tráfego (todas as portas) da sub-rede Frontend para a sub-rede Backend é negado

Com essas regras associadas a cada sub-rede, se uma solicitação HTTP entra da Internet para o firewall, as regras 3 (Permitir) e 5 (Negar) se aplicam, mas como a regra 3 tem uma prioridade mais alta, só ela se aplica e regra 5 não entra em cena. Assim, a solicitação HTTP seria permitida ao firewall. Se esse mesmo tráfego estava tentando acessar o servidor IIS01, mesmo que ele esteja na sub-rede Frontend, a regra 5 (Deny) seria aplicada e o tráfego não teria permissão para passar para o servidor. A regra 6 (Negar) impede que a sub-rede Frontend converse com a sub-rede Backend (exceto pelo tráfego permitido nas regras 1 e 4); isso protege a rede Backend caso um invasor comprometa o aplicativo Web na rede Frontend, pois ele teria acesso limitado à rede Backend “protegida” (somente para recursos expostos no servidor AppVM01).

Há uma regra de saída padrão que permite o tráfego de saída para a Internet. Neste exemplo, permitiremos o tráfego de saída e não modificaremos quaisquer regras de saída. Para bloquear o tráfego em ambas as direções, é necessário o roteamento definido pelo usuário; isso é explorado "Exemplo 3" abaixo.

#### Descrição da regra de firewall
No firewall, será necessário criar regras de encaminhamento. Como este exemplo só roteia o tráfego de Internet de entrada para o firewall e, em seguida, para o servidor Web, somente uma regra de NAT de encaminhamento será necessária.

A regra de encaminhamento aceitará qualquer endereço de origem de entrada que acesse o Firewall tentando alcançar o HTTP (porta 80 ou 443 para HTTPS) e será enviada para a interface local do firewall e redirecionada para o servidor Web com o endereço IP 10.0.1.5.

#### Conclusão
Essa é uma maneira relativamente simples de proteger seu aplicativo com um firewall e isolar a sub-rede de back-end do tráfego de entrada. Mais informações sobre este exemplo como as seguintes:

- Como criar esse DMZ de exemplo com scripts do PowerShell
- Como criar esse exemplo com um modelo de ARM
- Descrições detalhadas de cada comando NSG e regra de Firewall
- Cenários de fluxo de tráfego detalhados mostrando como o tráfego é permitido ou negado em cada camada

podem ser encontrados na página [Instruções detalhadas de compilação][Example2].

### Exemplo 3 - Criar uma DMZ para proteger as redes com um Firewall, um UDR e um NSG
[Voltar ao Início Rápido](#fast-start) | [Instruções detalhadas de compilação para este exemplo][Example3]

![DMZ bidirecional com NVA, NSG e UDR][9]

#### Configuração do ambiente
Neste exemplo, há uma assinatura que contém o seguinte:

- Três serviços de nuvem: “SecSvc001”, “FrontEnd001” e “BackEnd001”
- Uma Rede Virtual, “CorpNetwork”, com três sub-redes; “SecNet”, “FrontEnd” e “BackEnd”
- Um dispositivo virtual de rede, neste exemplo um firewall, conectado à sub-rede SecNet
- Um Windows Server que representa um servidor Web de aplicativos ("IIS01")
- Dois servidores Windows que representam servidores de back-end de aplicativos ("AppVM01", "AppVM02")
- Um servidor Windows que representa um servidor DNS ("DNS01")

Há mais detalhes para criar este exemplo (ele fornece scripts e um modelo ARM) localizados na página [instruções detalhadas de compilação][Example3].

#### Descrição do UDR (roteamento definido pelo usuário)
Por padrão, as rotas do sistema a seguir são definidas como:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

A VNETLocal sempre será o(s) prefixo(s) do endereço definido para aquela rede específica (isto é, ela será alterada de Rede Virtual para Rede Virtual, dependendo de como cada Rede Virtual específica seja definida). As rotas do sistema restantes são estáticas e padrão como acima.

Neste exemplo, duas tabelas de roteamento são criadas, uma para a sub-rede Frontend e a outra para a sub-rede Backend. Cada tabela é carregada com as rotas estáticas apropriadas para determinada sub-rede. Nesse exemplo, cada tabela tem três rotas que direcionam todo o tráfego (0.0.0.0/0) através do firewall (próximo salto = endereço IP do dispositivo virtual):

1. O tráfego de sub-rede local sem Próximo Salto definido para permitir que o tráfego de sub-rede local ignore o firewall
2. Tráfego de Rede Virtual com um Próximo Salto definido como firewall, isso substitui a regra padrão que permite que o tráfego de Rede Virtual local seja roteado diretamente
3. Todo o tráfego restante (0/0) com um Próximo Salto definido como o firewall

Depois que as tabelas de roteamento forem criadas, serão associadas às sub-redes delas. Para a tabela de roteamento da sub-rede Frontend, uma vez criada e associada à sub-rede, deverá ter esta aparência:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
		 {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

>[AZURE.NOTE]Há limitações atuais com redes UDR e híbridas. Isso está sendo resolvido para uma futura versão; exemplos de como habilitar a DMZ com a Rota Expressa ou rede site a site são discutidos abaixo nos exemplos 3 e 4.

#### Descrição de encaminhamento IP
Um recurso complementar para UDR é o Encaminhamento IP. Essa é uma configuração em um Dispositivo Virtual que permite receber o tráfego endereçado não especificamente para o dispositivo e, em seguida, encaminhar esse tráfego para seu destino final.

Por exemplo, se o tráfego de AppVM01 fizer uma solicitação para o servidor DNS01, o UDR rotearia isso para o firewall. Com o Encaminhamento de IP habilitado, o tráfego para o destino DNS01 (10.0.2.4) será aceito pelo dispositivo (10.0.0.4) e encaminhado para seu destino final (10.0.2.4). Sem o Encaminhamento de IP habilitado no Firewall, tráfego não seria aceito pelo dispositivo, embora a tabela de rota tenha o firewall como o próximo nó. Para usar um dispositivo virtual, é essencial lembrar-se de habilitar o Encaminhamento IP em conjunto com o Roteamento Definido pelo Usuário.

#### Descrição do grupo de segurança de rede (NSG)
Neste exemplo, um grupo NSG é criado e então carregado com uma única regra. Esse grupo é então associado somente às sub-redes Frontend e Backend (e não a SecNet). Declarativamente, a seguinte regra está sendo criada:

1.	Todo tráfego (todas as portas) da Internet a toda a VNet (todas as sub-redes) é negado

Embora NSGs sejam usados neste exemplo, seu principal objetivo será ser como uma segunda camada de defesa contra erros de configuração manual. Queremos bloquear todo tráfego de entrada da Internet para as sub-redes Frontend ou Backend; o tráfego só fluirá por meio da sub-rede SecNet até o firewall (e então, se apropriado, nas sub-redes Frontend ou Backend). Além disso, com as regras UDR em vigor, qualquer tráfego que tenha conseguido passar para as sub-redes Frontend ou Backend seria direcionado para o firewall (graças ao UDR). O firewall veria isso como um fluxo assimétrico e descartaria o tráfego de saída. Assim, há três camadas de segurança protegendo as sub-redes Frontend e Backend; 1) nenhum ponto de extremidade aberto nos serviços de nuvem FrontEnd001 e BackEnd001, 2) NSGs negando o tráfego da Internet, 3) o firewall descartando o tráfego assimétrico.

Um ponto interessante sobre o Grupo de Segurança de Rede neste exemplo é que ele contém apenas uma regra, que é negar o tráfego da Internet para toda a rede virtual, o que incluiria a sub-rede Security. No entanto, como o NSG está associado apenas às sub-redes Frontend e Backend, a regra não será processada em tráfego de entrada para a sub-rede Security. Como resultado, mesmo que a regra NSG diga não ao tráfego da Internet para qualquer endereço na Rede Virtual, como o NSG nunca foi associado à sub-rede Security, o tráfego fluirá para a sub-rede Security.

#### Regras de firewall
No firewall, será necessário criar regras de encaminhamento. Uma vez que o firewall está bloqueando ou encaminhando todos os tráfegos de entrada, de saída e entre Redes Virtuais, serão necessárias várias regras de firewall. Além disso, todo o tráfego de entrada atingirá o endereço IP público do Serviço de Segurança (em portas diferentes), para ser processado pelo firewall. Uma prática recomendada é criar um diagrama de fluxos de lógica antes de configurar as sub-redes e as regras de firewall para evitar a reformulação posteriormente. A figura a seguir é uma exibição lógica das regras de firewall para este exemplo:
 
![Exibição lógica das regras de firewall][10]

>[AZURE.NOTE]Com base no Dispositivo Virtual de Rede usado, as portas de gerenciamento variam. Neste exemplo, um Firewall NG Barracuda é mencionado e usa as portas 22, 801 e 807. Consulte a documentação do fornecedor do dispositivo para localizar as portas exatas usadas para o gerenciamento do dispositivo usado.

#### Descrição das regras de firewall
No diagrama lógico acima, a sub-rede de segurança não é mostrada, já que o firewall é o único recurso nessa sub-rede, e esse diagrama mostra as regras de firewall e como elas permitem ou negam logicamente fluxos de tráfego e não o caminho roteado real. Além disso, as portas externas selecionadas para o tráfego RDP são portas com um intervalo maior (8014 – 8026) e foram selecionadas para alinhar um pouco com os dois últimos octetos do endereço IP local para facilitar a leitura (por exemplo, o endereço do servidor local 10.0.1.4 está associado à porta externa 8014); no entanto, todas as portas não conflitantes acima disso poderiam ser usadas.

Neste exemplo, precisamos de sete tipos de regras, descritos abaixo:

- Regras externas (para o tráfego de entrada):
  1.	Regra de Gerenciamento de Firewall: essa regra de Redirecionamento de Aplicativo permite que o tráfego passe para as portas de gerenciamento do dispositivo virtual de rede.
  2.	Regras de RDP (para cada servidor Windows): essas quatro regras (uma para cada servidor) permitirão o gerenciamento dos servidores individuais via RDP. Isso também poderia ser empacotado em uma regra, dependendo dos recursos do Dispositivo Virtual de Rede usado.
  3.	Regras de Tráfego do Aplicativo: há duas regras de tráfego de aplicativo, a primeira para o tráfego da Web de front-end e a segunda para o tráfego de back-end (por exemplo, o servidor Web para a camada de dados). A configuração dessas regras dependerá de arquitetura de rede (onde os servidores são colocados) e dos fluxos de tráfego (a direção na qual o tráfego flui e quais portas são usadas).
      - A primeira regra permitirá que o tráfego de aplicativo real chegue ao servidor de aplicativos. Embora as outras regras permitam a segurança, o gerenciamento etc, as Regras de Aplicativo são as que permitem que usuários ou serviços externos acessem os aplicativos. Neste exemplo, há um único servidor Web na porta 80 e, portanto, uma única regra de aplicativo de firewall redirecionará o tráfego de entrada para o IP externo, para o endereço IP interno dos servidores Web. A sessão de tráfego redirecionado seria enviada por meio de NAT para o servidor interno.
      - A segunda Regra de Tráfego de Aplicativo é a regra de back-end que permite que o Servidor Web converse com o servidor AppVM01 (mas não com AppVM02) por meio de qualquer porta.
- Regras internas (para o tráfego interno da Rede Virtual)
  4.	Regra de Saída para Internet: essa regra permitirá que o tráfego de qualquer rede passe para as redes selecionadas. Normalmente, essa regra é uma regra padrão que já existe no firewall, mas em um estado desabilitado. Essa regra deve ser habilitada para esse exemplo.
  5.	Regra DNS: essa regra permite que somente o tráfego DNS (porta 53) passe para o servidor DNS. Para este ambiente, a maioria do tráfego do Frontend para o Backend será bloqueado, e essa regra permite especificamente o DNS de qualquer sub-rede local.
  6.	Regra Sub-rede para Sub-rede: essa regra permite que qualquer servidor na rede de back-end se conecte a qualquer servidor na rede de front-end (mas não o contrário).
- Regra à prova de falhas (para o tráfego que não atenda a qualquer um dos itens acima):
  7.	Regra Negar Todo o Tráfego: essa sempre deverá ser a última regra (em termos de prioridade) e, como tal, se os fluxos de tráfego falharem na correspondência a todas as regras anteriores, serão descartados por essa regra. Essa é uma regra padrão e normalmente é ativada; geralmente, não é necessário fazer qualquer modificação.

>[AZURE.TIP]Na segunda regra de tráfego de aplicativo, qualquer porta é permitida para facilitar este exemplo, em um cenário real, a porta mais específica e os intervalos de endereços devem ser usados para reduzir a superfície de ataque dessa regra.

Depois que todas as regras acima forem criadas, será importante examinar a prioridade de cada regra para garantir que o tráfego seja permitido ou negado como desejado. Neste exemplo, as regras estão em ordem de prioridade.

#### Conclusão
Essa é uma maneira mais complexa, mas mais completa, de proteger e isolar a rede (o exemplo 2 estava protegendo apenas o aplicativo e o exemplo 1 estava apenas isolando as sub-redes). Esse design permite monitorar o tráfego em ambas as direções e protege não apenas o servidor de aplicativos de entrada, mas impõe políticas de segurança de rede para todos os servidores na rede. Além disso, dependendo do dispositivo usado, pode-se conseguir auditoria e reconhecimento total de tráfego. Mais informações sobre este exemplo como as seguintes:

- Como criar esse DMZ de exemplo com scripts do PowerShell
- Como criar esse exemplo com um modelo de ARM
- As descrições detalhadas de cada comando NSG, UDR e regra de Firewall
- Cenários de fluxo de tráfego detalhados mostrando como o tráfego é permitido ou negado em cada camada

podem ser encontrados na página [Instruções detalhadas de compilação][Example3].

### Exemplo 4: adicionando uma conexão híbrida com um VPN site a site de dispositivo virtual
[Voltar ao Início Rápido](#fast-start) | Instruções detalhadas de compilação estarão disponíveis em breve

![DMZ com rede híbrida conectada com NVA][11]

#### Configuração do ambiente
Uma rede híbrida usando um NVA (dispositivo virtual de rede) pode ser adicionada a qualquer um dos tipos de DMZ descritas no exemplo 1, 2 ou 3.

Conforme mostrado na figura acima, uma conexão VPN pela Internet (site a site) é usada para conectar uma rede local a uma rede virtual do Azure através de um dispositivo de rede virtual.

>[AZURE.NOTE]Se você usar a Rota Expressa com a opção de emparelhamento público do Azure habilitada, uma rota estática precisará ser criada para a rota até o endereço IP de VPN NVA para fora da borda da Internet corporativa, e não por meio da borda WAN da Rota Expressa. Isso ocorre porque a NAT necessária na opção de emparelhamento público da Rota Expressa do Azure que provavelmente interromperá a sessão VPN (IPSec geralmente não funciona bem com NATs).

Depois que a VPN estiver no local, o NVA torna-se "hub" central para todas as redes e sub-redes. As regras de encaminhamento de firewall determinam quais fluxos de tráfego são permitidos, convertidos (NAT), redirecionados ou descartados (mesmo para fluxos de tráfego entre a rede local e o Azure se os fluxos forem projetados dessa forma).

Os fluxos de tráfego devem ser considerados com cuidado, já que podem ser otimizados ou degradados por esse padrão de design dependendo do caso de uso específico.

O uso do ambiente criado no exemplo 3, "Criar uma DMZ para proteger redes com firewall, UDR e NSG" e a adição de uma conexão de rede híbrida VPN site a site gerariam o design a seguir:

![DMZ com NVA conectado usando uma VPN site a site][12]

No design acima, o roteador local ou outro dispositivo de rede que seja compatível com seu dispositivo de rede virtual para VPN seria o cliente VPN. Este dispositivo físico seria responsável por iniciar e manter a conexão VPN com o dispositivo virtual da rede.

Logicamente para o NVA, a rede é igual a quatro "zonas de segurança" separadas com as regras sobre o NVA sendo as principais orientadoras do tráfego entre essas zonas. Isso se pareceria logicamente com:

![Rede lógica da perspectiva de NVA][13]

#### Conclusão
A adição de uma conexão de rede híbrida VPN site a site para uma rede virtual do Azure pode estender a rede local no Azure de maneira segura. Ao usar uma conexão VPN, o tráfego é criptografado e roteado pela Internet. O uso de NVA, como foi feito neste exemplo, fornece um local central para aplicar e gerenciar a política de segurança. Mais informações sobre este exemplo como as seguintes:

- Como criar esse DMZ de exemplo com scripts do PowerShell
- Como criar esse exemplo com um modelo de ARM
- Cenários de fluxo de tráfego detalhados mostrando como o tráfego flui através desse design

estarão disponíveis em breve e vinculados a essa página.

### Exemplo 5: adicionando uma conexão híbrida com um Gateway VPN site a site do Azure
[Voltar ao Início Rápido](#fast-start) | Instruções detalhadas de compilação estarão disponíveis em breve

![DMZ com a rede híbrida do Gateway conectada][14]

#### Configuração do ambiente
Uma rede híbrida usando um Gateway de VPN do Azure pode ser adicionada a qualquer tipo de DMZ descrito nos exemplos 1 e 2.

Conforme mostrado na figura acima, uma conexão VPN pela Internet (site a site) é usada para conectar uma rede local a uma rede virtual do Azure através de um Gateway de VPN do Azure.

>[AZURE.NOTE]Se você usar a Rota Expressa com a opção de emparelhamento público do Azure habilitada, uma rota estática precisará ser criada para a rota até o endereço IP de do Gateway de VPN do Azure para fora da borda da Internet corporativa, e não por meio da borda WAN da Rota Expressa. Isso ocorre porque a NAT necessária na opção de emparelhamento público da Rota Expressa do Azure que provavelmente interromperá a sessão VPN (IPSec geralmente não funciona bem com NATs).

Conforme mostrado abaixo, com essa opção, o ambiente agora tem duas bordas de rede. Na primeira borda, o NVA e os NSGs controlam fluxos de tráfego para redes internas do Azure e entre o Azure e a Internet, enquanto a segunda borda é o Gateway de VPN do Azure, que é uma borda de rede completamente separada e isolada entre locais e o Azure.

Os fluxos de tráfego devem ser considerados com cuidado, já que podem ser otimizados ou degradados por esse padrão de design dependendo do caso de uso específico.

O uso do ambiente criado no exemplo 1, "Criar uma DMZ para proteger aplicativos com NSGs" e a adição de uma conexão de rede híbrida VPN site a site gerariam o design a seguir:

![DMZ com Gateway conectado usando uma conexão da Rota Expressa][15]

#### Conclusão
A adição de uma conexão de rede híbrida VPN site a site para uma rede virtual do Azure pode estender a rede local no Azure de maneira segura. Usando o Gateway de VPN do Azure nativo, o tráfego é criptografado com IPSec e roteado via Internet. Além disso, o uso de Gateway de VPN do Azure fornece uma opção de menor custo (sem custos com licença adicional ou com dispositivos virtuais de rede de terceiros). Isso é mais econômico no exemplo 1, em que nenhum dispositivo virtual de rede é usado. Mais informações sobre este exemplo como as seguintes:

- Como criar esse DMZ de exemplo com scripts do PowerShell
- Como criar esse exemplo com um modelo de ARM
- Cenários de fluxo de tráfego detalhados mostrando como o tráfego flui através desse design

estarão disponíveis em breve e vinculados a essa página.

### Exemplo 6: adicionando uma conexão híbrida com a Rota Expressa
[Voltar ao Início Rápido](#fast-start) | Instruções detalhadas de compilação estarão disponíveis em breve

![DMZ com a rede híbrida do Gateway conectada][16]

#### Configuração do ambiente
Uma rede híbrida usando uma conexão de emparelhamento privado de Rota Expressa pode ser adicionada a qualquer tipo de DMZ descrita no exemplo 1 ou 2.

Conforme mostrado na figura acima, o emparelhamento privado de Rota Expressa fornece uma conexão direta entre sua rede local e de rede virtual do Azure. O tráfego transmite apenas a rede do provedor de serviços e a rede do Azure/Microsoft, nunca em contato com a Internet.

>[AZURE.NOTE]Há uma limitação usando o Roteamento Definido pelo Usuário (UDR) e a Rota Expresso devido à complexidade do roteamento dinâmico usado no Gateway Virtual do Azure. As sub-redes que estejam se comunicando com o Gateway do Azure e fornecendo a conexão da Rota Expressa não devem ter o UDR aplicado. Além disso, o Gateway do Azure não pode ser o dispositivo NextHop para outras sub-redes associadas ao UDR. A capacidade de integrar totalmente o UDR e a Rota Expressa será habilitada em uma versão futura do Azure.

</br>
>[AZURE.TIP]O uso da Rota Expressa mantém o tráfego de rede corporativa fora da Internet para melhorar a segurança, aumentando significativamente o desempenho e permitindo SLAs do seu provedor da Rota Expressa. Como ele está relacionado ao desempenho da Rota Expressa, o Gateway do Azure pode passar até 2 Gbps com a Rota Expressa, enquanto com VPNs site a site, a taxa de transferência máxima do Gateway do Azure é de 200 Mbps.

Como visto no diagrama abaixo, com essa opção, o ambiente agora tem duas bordas de rede. O NVA e o NSG controlam fluxos de tráfego para redes internas do Azure e entre o Azure e a internet, enquanto o gateway é uma borda de rede completamente separada e isolada entre o local e o Azure.

Os fluxos de tráfego devem ser considerados com cuidado, já que podem ser otimizados ou degradados por esse padrão de design dependendo do caso de uso específico.

O uso do ambiente criado no exemplo 1, "Criar uma DMZ simples para proteger aplicativos com NSGs" e a adição de uma conexão de rede híbrida VPN site a site gerariam o design a seguir:

![DMZ com Gateway conectado usando uma conexão da Rota Expressa][17]

#### Conclusão
A adição de uma conexão de rede de emparelhamento privado de Rota Expressa pode estender a rede local para o Azure de forma segura, com baixa latência e alto desempenho. Além disso, o uso de Gateway do Azure, como feito neste exemplo, fornece uma opção de menor custo (sem licença adicional ou dispositivos virtuais de rede de terceiros). Mais informações sobre este exemplo como as seguintes:

- Como criar esse DMZ de exemplo com scripts do PowerShell
- Como criar esse exemplo com um modelo de ARM
- Cenários de fluxo de tráfego detalhados mostrando como o tráfego flui através desse design

estarão disponíveis em breve e vinculados a essa página.

## Referências
### Sites úteis e documentação
- Acessar o Azure com ARM: 
- Acessando o Azure com o PowerShell: [https://azure.microsoft.com/documentation/articles/powershell-install-configure/](./powershell-install-configure.md)
- Documentação de rede virtual: [https://azure.microsoft.com/documentation/services/virtual-network/](https://azure.microsoft.com/documentation/services/virtual-network/)
- Documentação do grupo de segurança de rede: [https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/](./virtual-network/virtual-networks-nsg.md)
- Documentação do roteamento definido pelo usuário: [https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/](./virtual-network/virtual-networks-udr-overview.md)
- Gateways Virtuais do Azure: [https://azure.microsoft.com/documentation/services/vpn-gateway/](https://azure.microsoft.com/documentation/services/vpn-gateway/)
- VPNs site a site: [https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/](./virtual-network/vpn-gateway-site-to-site-create.md)
- Documentação da Rota Expressa (verifique as seções "Introdução" e "Tutoriais"): [https://azure.microsoft.com/documentation/services/expressroute/](https://azure.microsoft.com/documentation/services/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Fluxograma de opções de segurança"
[1]: ./media/best-practices-network-security/compliancebadges.png "Selos de conformidade do Azure"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Recursos de segurança do Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Uma DMZ em uma rede corporativa"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Arquitetura de segurança do Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Uma DMZ em uma Rede Virtual do Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Rede híbrida com três limites de segurança"
[7]: ./media/best-practices-network-security/example1design.png "DMZ de entrada com NSG"
[8]: ./media/best-practices-network-security/example2design.png "DMZ de entrada com NVA e NSG"
[9]: ./media/best-practices-network-security/example3design.png "DMZ bidirecional com NVA, NSG e UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Exibição lógica das regras de firewall"
[11]: ./media/best-practices-network-security/example4designoptions.png "DMZ com rede híbrida conectada com NVA"
[12]: ./media/best-practices-network-security/example4designs2s.png "DMZ com NVA conectado usando uma VPN site a site"
[13]: ./media/best-practices-network-security/example4networklogical.png "Rede lógica da perspectiva de NVA"
[14]: ./media/best-practices-network-security/example5designoptions.png "DMZ com rede híbrida site a site conectada ao Gateway do Azure"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ com Gateway do Azure usando VPN site a site"
[16]: ./media/best-practices-network-security/example6designoptions.png "DMZ com rede híbrida da Rota Expressa conectada ao Gateway do Azure"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ com Gateway do Azure usando uma conexão de Rota Expressa"

<!--Link References-->
[Example1]: ./virtual-network/virtual-networks-dmz-nsg-asm.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md

<!---HONumber=Sept15_HO4-->