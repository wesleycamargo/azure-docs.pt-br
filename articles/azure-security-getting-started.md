<properties
   pageTitle="Introdução à segurança do Microsoft Azure | Microsoft Azure"
   description="Este artigo fornece uma visão geral de recursos da Segurança do Microsoft Azure e considerações gerais para organizações que estejam migrando seus ativos para um provedor de nuvem."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/28/2015"
   ms.author="yuridio"/>

#Introdução à segurança do Microsoft Azure

Quando você cria ou migra ativos de TI para um provedor de nuvem, depende da capacidade dessa organização de proteger os aplicativos e os dados confiados aos serviços dela e dos controles de segurança que ela oferece para controlar a segurança de seus ativos baseados em nuvem.

A infraestrutura do Azure foi projetada desde a instalação até os aplicativos para a hospedagem simultânea de milhões de clientes e para o fornecimento de uma fundação confiável com a qual as empresas podem atender às suas necessidades de segurança. Além disso, o Azure fornece a você uma ampla variedade de opções de segurança configuráveis e a capacidade de controlar a forma que você pode personalizar a segurança para atender aos requisitos específicos de suas implantações.

Neste artigo de visão geral sobre a segurança do Azure, examinaremos:

-   Os serviços do Azure e os recursos que você pode usar para ajudar a proteger seus serviços e seus dados no Azure

-   Como a Microsoft protege a infraestrutura do Azure para ajudar a proteger seus dados e aplicativos

##Gerenciamento de identidade e de acesso


O controle do acesso à infraestrutura de TI, aos dados e aos aplicativos é essencial. No Microsoft Azure, esses recursos são fornecidos por serviços como o Active Directory do Azure, o Armazenamento do Azure e dão suporte a vários padrões e APIs.

O [Active Directory do Azure](active-directory-whatis.md) (AD do Azure) é um repositório de identidades e mecanismo que fornece autenticação, autorização e controle de acesso para usuários, grupos e objetos de uma organização. O AD do Azure oferece aos desenvolvedores uma maneira eficaz de integrar o gerenciamento de identidade aos aplicativos. Os protocolos padrão do setor, como [SAML 2.0](https://en.wikipedia.org/wiki/SAML_2.0), [WS-Federation](https://msdn.microsoft.com/library/bb498017.aspx) e [OpenID Connect](http://openid.net/connect/) possibilitam a entrada em uma variedade de plataformas, como .NET, Java, Node.js e PHP.

A Graph API baseada em REST permite que os desenvolvedores leiam e gravem no diretório a partir de qualquer plataforma. Por meio do suporte do [OAuth 2.0](http://oauth.net/2/), os desenvolvedores podem compilar aplicativos Web e móveis que se integram às APIs da Web de terceiros e da Microsoft e compilar suas próprias APIs da Web seguras. As bibliotecas de cliente de software livre estão disponíveis para .Net, Windows Store, iOS e Android com bibliotecas adicionais em desenvolvimento.

### Como o Azure habilita o gerenciamento de identidade e de acesso

O AD do Azure pode ser usado como um diretório de nuvem autônomo para sua organização ou como uma solução integrada ao Active Directory local existente. Alguns recursos de integração incluem a sincronização de diretórios e o logon único (SSO). Eles estendem o alcance de suas identidades existentes no local para a nuvem e melhoram a experiência do administrador e do usuário final.

Alguns outros recursos para gerenciamento de identidade e acesso incluem:

-   O AD do Azure habilita o [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) para aplicativos SaaS, independentemente de onde estejam hospedados. Alguns aplicativos são federados com o AD do Azure e outros usam SSO com senha. Os aplicativos federados também podem dar suporte ao provisionamento do usuário e o armazenamento de senha no cofre.

-   O acesso aos dados no [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) é controlado por meio de autenticação. Cada Conta de Armazenamento tem uma chave primária ([Chave de Conta de Armazenamento](https://msdn.microsoft.com/library/azure/ee460785.aspx), ou SAK) e uma chave secreta secundária (a [Assinatura de Acesso Compartilhado](storage-dotnet-shared-access-signature-part-1.md), ou SAS).

-   O AD do Azure fornece identidade como um serviço de Federação (usando [Serviços de Federação do Active Directory](fundamentals-identity.md), sincronização e replicação com diretórios locais.

-   O [Azure Multi-Factor Authentication (MFA)](multi-factor-authentication.md) é o serviço de autenticação multifator que exige que os usuários também verifiquem as entradas usando um aplicativo móvel, uma chamada telefônica ou uma mensagem de texto. Ela está disponível para uso com o AD do Azure, para proteger os recursos locais com o Servidor do Azure MFA e com aplicativos personalizados e diretórios que usam o SDK.

-   [Os Serviços de Domínio do AD do Azure](https://azure.microsoft.com/services/active-directory-ds/) permitem adicionar máquinas virtuais do Azure a um domínio sem a necessidade de implantar controladores de domínio. Os usuários podem entrar nessas máquinas virtuais usando suas credenciais corporativas do Active Directory e administrar as máquinas virtuais associadas ao domínio usando uma Política de Grupo para impor linhas de base de segurança em todas as máquinas virtuais do Azure.

-   O [Active Directory B2C do Azure](https://azure.microsoft.com/services/active-directory-b2c/) fornece um serviço de gerenciamento de identidade global, altamente disponível para aplicativos voltados para o consumidor que pode ser dimensionado para centenas de milhões de identidades. Ele pode ser integrado a plataformas móveis e da Web. Seus clientes podem fazer logon em todos os seus aplicativos por meio de experiências personalizáveis usando suas contas sociais existentes ou criando novas credenciais.

##Controle de acesso de dados e criptografia

A Microsoft emprega os princípios de Separação de Tarefas e [Privilégios Mínimos](https://en.wikipedia.org/wiki/Principle_of_least_privilege) durante as operações do Azure. O acesso a dados pela equipe de suporte do Azure requer sua permissão explícita e é concedido de forma "just-in-time" que é registrada e auditada e então revogada após a conclusão do projeto.

Além disso, o Azure oferece vários recursos para proteger dados em trânsito e em repouso, incluindo a criptografia de dados, arquivos, aplicativos, serviços, comunicações e unidades. Você tem a opção de criptografar informações antes de colocá-las no Azure, bem como armazenar chaves em seus datacenters locais.

![Microsoft Antimalware no Azure](./media/azure-security-getting-started\sec-azgsfig1.PNG)

### Tecnologias de criptografia do Azure

Você pode coletar detalhes sobre o acesso administrativo ao seu ambiente de assinatura usando [Relatórios do AD do Azure](active-directory-reporting-audit-events.md). Você tem a opção de configurar a [Criptografia de Unidade do BitLocker](https://technet.microsoft.com/library/cc732774.aspx) em VHDs com informações confidenciais no Azure.

Outros recursos do Azure que ajudarão você a manter seus dados seguros incluem:

-   Os desenvolvedores de aplicativos podem compilar a criptografia nos aplicativos implantados no Azure usando a Windows [CryptoAPI](https://msdn.microsoft.com/library/ms867086.aspx) e o .NET Framework.

- A criptografia do lado cliente para o armazenamento de blob da Microsoft permite o controle completo das chaves. O serviço de armazenamento nunca vê as chaves e é incapaz de descriptografar os dados.

-   O [Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) (com o [RMS SDK](https://msdn.microsoft.com/library/dn758244.aspx) fornece criptografia ao nível de dados e de arquivos e prevenção de perda de dados por meio do gerenciamento de acesso baseado na política.

-   O Azure dá suporte à [criptografia no nível de tabela e de coluna (TDE/CLE)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database.aspx) em Máquinas Virtuais do SQL Server e dá suporte a servidores de gerenciamento de chaves de terceiros locais nos datacenters de clientes.

-   As Chaves de Conta de Armazenamento, as Assinaturas de Acesso Compartilhado, os certificados de gerenciamento e outras chaves são exclusivos de cada locatário do Azure.

-   O armazenamento híbrido do Azure [StorSimple](http://www.microsoft.com/server-cloud/products/storsimple/overview.aspx) criptografa dados por meio de um par de chaves pública/privada de 128 bits antes de carregá-los no Armazenamento do Azure.

-   O Azure oferece suporte e usa vários mecanismos de criptografia, incluindo SSL/TLS, IPsec e AES, dependendo dos tipos de dados e dos contêineres e transportes.

##Virtualização

A plataforma do Azure usa um ambiente virtualizado. As instâncias do usuário funcionam como máquinas virtuais autônomas que não têm acesso a um servidor de host físico e esse isolamento é imposto usando [níveis de privilégio do processador (anel-0/anel-3)](https://en.wikipedia.org/wiki/Protection_ring) físico.

O Anel 0 é o mais privilegiado e o 3 é o menos. O sistema operacional convidado é executado em um Anel 1 com menos privilégios e os aplicativos no Anel 3, com o último privilégio. Essa virtualização dos recursos físicos leva a uma clara separação entre o sistema operacional convidado e o hiervisor, resultando na separação de segurança adicional entre os dois.

O Hipervisor do Azure funciona como um microkernel e passa todas as solicitações de acesso de hardware de VMs convidadas para o host para processamento usando uma interface de memória compartilhada chamada VMBus. Isso impede que os usuários obtenham acesso bruto de leitura/gravação/execução no sistema e minimiza o risco de compartilhamento de recursos do sistema.

![Microsoft Antimalware no Azure](./media/azure-security-getting-started\sec-azgsfig2.PNG)

### Como o Azure implementa a virtualização

O Azure usa um firewall de hipervisor (filtro de pacote), que é implementado no hipervisor e configurado por um agente de controlador de malha. Isso ajuda a proteger locatários contra o acesso não autorizado. Por padrão, quando uma VM é criada, todo o tráfego é bloqueado e o agente do controlador de malha configura o filtro de pacote para adicionar *regras e exceções* para permitir o tráfego autorizado.

Há duas categorias de regras que são programadas aqui:

-   **Configuração da Máquina ou Regras de Infraestrutura**: por padrão, toda a comunicação é bloqueada. Há exceções para permitir que uma VM envie e receba tráfego DHCP e DNS. As VMs também podem enviar tráfego para a Internet "pública" e enviar tráfego para outras VMs no cluster e no servidor de Ativação do Sistema Operacional. A lista de permissões da VM para destinos não inclui sub-redes de roteador do Azure, o back-end de gerenciamento do Azure e outras propriedades da Microsoft.

-   **Arquivo de Configuração de Função**: define as ACLs de entrada com base no modelo de serviço dos locatários. Por exemplo, se um locatário tiver um front-end da Web na porta 80 em uma determinada VM, o Azure abrirá a porta TCP 80 para todos os IPs se você estiver configurando um ponto de extremidade no modelo [Gerenciamento de Serviços do Azure](resource-manager-deployment-model.md). Se a VM tiver uma função de back-end ou de trabalho em execução, abriremos a função de trabalho somente para a VM que esteja no mesmo locatário.

##Isolamento

Manter a separação para evitar a transferência não autorizada e não intencional de informações entre as implantações em uma arquitetura multilocatária compartilhada é outro requisito de segurança importante da nuvem.

O Azure implementa o [controle de acesso à rede](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/) e a segregação por meio do isolamento de VLAN, ACLs, filtros IP e balanceadores de carga. O tráfego de entrada externo para suas máquinas virtuais está restrito a portas e a protocolos definidos por você. A filtragem de rede é implementada para evitar o tráfego falsificado e restringe o tráfego de entrada e saída para componentes de plataforma confiáveis. As políticas de fluxo de tráfego são implementadas em dispositivos de proteção de limite que negam o tráfego por padrão.

![Microsoft Antimalware no Azure](./media/azure-security-getting-started\sec-azgsfig3.PNG)

A Conversão de Endereços de Rede (NAT) é usada para separar o tráfego de rede interno do tráfego externo. O tráfego interno não é roteável externamente. Os [endereços IP virtuais](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) que são externamente roteáveis são convertidos em endereços [IP dinâmico interno](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) que só são roteáveis dentro do Azure.

O tráfego externo para máquinas virtuais do Azure passa pelo firewall via Lista de Controle de Acesso (ACLs) em roteadores, balanceadores de carga e switches de Camada 3. Somente os protocolos conhecidos específicos são permitidos. As ACLs estão em vigor para limitar o tráfego originado de VMs convidadas para outras VLANs usadas para gerenciamento. Além disso, o tráfego filtrado por meio de filtros IP no sistema operacional do host limita ainda mais o tráfego nas camadas de vínculo de dados e de rede.

### Como o Azure implementa o isolamento

O Azure Fabric Controller é responsável por alocar recursos de infraestrutura para cargas de trabalho de locatário e gerencia a comunicação unidirecional do host para VMs. O hipervisor do Azure impõe a memória e a separação de processos entre VMs e roteia com segurança o tráfego de rede para locatários do sistema operacional convidado. O Azure também implementa o isolamento para locatários, armazenamento e redes virtuais:

-   Cada locatário do AD do Azure é logicamente isolado usando limites de segurança.

-   As Contas de Armazenamento do Azure são exclusivas de cada assinatura e o acesso deve ser autenticado usando uma Chave de Conta de Armazenamento.

-   As Redes Virtuais são logicamente isoladas por meio de uma combinação de endereços IP privados, firewalls e ACLs de IP exclusivos. Os balanceadores de carga roteiam tráfego para os locatários apropriados com base em definições de ponto de extremidade.

##Rede Virtual e firewall

As [redes virtuais e distribuídas](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx) no Azure ajudam a garantir que seu tráfego de rede privada seja logicamente isolado do tráfego de outras Redes Virtuais do Azure.

![Microsoft Antimalware no Azure](./media/azure-security-getting-started\sec-azgsfig4.PNG)

Sua assinatura pode conter várias redes privadas isoladas (e inclui firewall, balanceamento de carga e conversão de endereços de rede).

O Azure fornece três níveis principais de separação de rede em cada cluster do Azure para segregar logicamente o tráfego. As [Redes Locais Virtuais](https://azure.microsoft.com/services/virtual-network/) (VLANs) são usadas para separar o tráfego de cliente do restante da rede do Azure. O acesso à rede do Azure de fora do cluster é restrito por meio de balanceadores de carga.

O tráfego de rede em VMs deve passar pelo switch virtual do hipervisor. O componente de filtro IP no Sistema Operacional Raiz isola a VM raiz das VMs convidadas e das VMs convidadas umas das outras. Ele executa a filtragem de tráfego para restringir a comunicação entre nós do locatário e a Internet pública (com base na configuração do serviço do cliente), separando-os de outros locatários.

O filtro IP ajuda a evitar que as VMs convidadas:
 
- Gerem tráfego falsificado
 
- Recebam tráfego não endereçado a elas

- Direcionem o tráfego para pontos de extremidade de infraestrutura protegidos

- Enviem ou recebam tráfego de difusão inadequado

Você pode colocar suas máquinas virtuais em [Redes Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-network/). Essas redes virtuais são semelhantes às redes configuradas em ambientes locais, onde estão geralmente associadas a um switch virtual. As máquinas virtuais conectadas à mesma Rede Virtual do Azure podem se comunicar umas com as outras sem configuração adicional. Você também tem a opção de configurar várias sub-redes diferentes na sua Rede Virtual do Azure.

Você pode usar as seguintes tecnologias de Rede Virtual do Azure para ajudar a proteger as comunicações na Rede Virtual do Azure:

-   [**Grupos de Segurança de Rede (NSG)**](virtual-networks-nsg.md). Você pode usar um NSG para controlar o tráfego em uma ou mais instâncias de máquina virtual (VM) em sua rede virtual. Um NSG contém regras de controle de acesso que permitem ou negam o tráfego com base na direção do tráfego, no protocolo, no endereço e porta de origem e no endereço e porta de destino.

-   [**Roteamento definido pelo usuário (UDR)**](virtual-networks-udr-overview.md). Você pode controlar o roteamento de pacotes por meio de um dispositivo virtual criando rotas definidas pelo usuário que especificam o próximo salto para os pacotes que fluem para uma sub-rede específica indo, então, para o dispositivo de segurança de rede virtual.

-   [**Encaminhamento IP**](virtual-networks-udr-overview.md). Um dispositivo de segurança de rede virtual deve ser capaz de receber o tráfego de entrada não endereçado a si mesma. Para permitir que uma VM receba o tráfego endereçado a outros destinos, você habilita o Encaminhamento IP para a VM.

-   [**Túnel forçado**](vpn-gateway-about-forced-tunneling.md). O túnel forçado permite redirecionar ou "forçar" todo o tráfego direcionado para a Internet gerado por suas máquinas virtuais em uma Rede Virtual do Azure de volta para seu local por meio de um túnel VPN de site a site para inspeção e auditoria

-   ACLs de [**ponto de extremidade**](virtual-machines-set-up-endpoints.md). Você pode controlar quais computadores têm conexões de entrada permitidas da Internet para uma máquina virtual em sua Rede Virtual do Azure definindo ACLs de ponto de extremidade.

-   [**Soluções de segurança de rede de parceiros**](https://azure.microsoft.com/marketplace/). Há uma série de soluções de segurança de rede do parceiro que você pode acessar do Azure Marketplace.

### Como o Azure implementa redes virtuais e firewall

O Azure implementa firewalls de filtragem de pacotes em todos os hosts e VMs convidadas por padrão. As imagens do sistema operacional do Windows da Galeria do Azure também têm o Firewall do Windows habilitado por padrão. Os balanceadores de carga no perímetro das comunicações de controle de redes voltadas ao público do Azure com base em ACLs IP gerenciadas por administradores de clientes.

Se o Azure mover dados de um cliente como parte das operações normais ou durante um desastre, ele fará isso em canais de comunicações privados criptografados. Outros recursos aproveitados pelo Azure a serem usados em redes virtuais e no firewall são:

-   **Firewall de Host Nativo**: a malha e o armazenamento do Azure são executados em um sistema operacional nativo sem hipervisor e, portanto, o firewall do Windows está configurado com os dois conjuntos de regras acima. O Armazenamento tem execução nativa para otimizar o desempenho.

-   **Firewall do Host**: o firewall do host existe para proteger o sistema operacional do host que executa o hipervisor. As regras são programadas para permitir que o controlador de malha e as jump boxes conversem com o sistema operacional do host em uma porta específica. As outras exceções existem para permitir a resposta DHCP e Respostas DNS. O Azure usa um arquivo de configuração de computador que tem o modelo de regras de firewall para o sistema operacional do host. O próprio host é protegido de ataques externos por um firewall do Windows configurado para permitir apenas a comunicação de fontes conhecidas e autenticadas.

-   **Firewall Convidado**: replica as regras no filtro de pacotes do Switch da VM mas programadas em software diferente (isto é, a parte do Firewall do Windows do sistema operacional convidado). O firewall da VM convidada pode ser configurado para restringir as comunicações de ou para a VM convidada, mesmo que a comunicação seja permitida pelas configurações no Filtro IP do host. Por exemplo, você poderá usar o firewall da VM convidada para restringir a comunicação entre duas de suas Redes Virtuais configuradas para se conectarem entre si.

-   **Firewall de Armazenamento (FW)**: o firewall no front-end de armazenamento filtra tráfego para que ele se restrinja às portas 80/443 e a outras portas de utilitário necessárias. O firewall no back-end de armazenamento restringe as comunicações ao que vier dos servidores de front-end de armazenamento.

-   **Gateway de Rede Virtual**: os [Gateways de Rede Virtual do Azure](virtual-networks-configure-vnet-to-vnet-connection.md) servem como os gateways entre locais que conectam suas cargas de trabalho na Rede Virtual do Azure para seus sites locais. É necessário conectar-se a sites locais por meio de [túneis de VPN site a site IPsec](vpn-gateway-create-site-to-site-rm-powershell.md) ou por meio de circuitos de [Rota Expressa](expressroute-introduction.md). Para túneis de VPN IPsec/IKE, os gateways executam handshakes de IKE e estabelecem túneis de VPN IPsec S2S entre as Redes Virtuais e sites locais. Os Gateways de Rede Virtual também encerram [VPNs ponto a site](vpn-gateway-point-to-site-create.md).

##Acesso Remoto Seguro

Os dados armazenados na nuvem devem ter garantias suficientes habilitadas para evitar explorações e manter a confidencialidade e a integridade em trânsito. Isso inclui controles de rede que se integram à identidade auditável baseada em política de uma organização e a mecanismos de gerenciamento de acesso.

A tecnologia de criptografia interna permite que você criptografe comunicações dentro e entre implantações, entre regiões do Azure e do Azure para datacenters locais. O acesso de administrador às máquinas virtuais por meio de [sessões da área de trabalho remota](virtual-machines-log-on-windows-server.md), do [Windows PowerShell remoto](http://blogs.technet.com/b/heyscriptingguy/archive/2013/09/07/weekend-scripter-remoting-the-cloud-with-windows-azure-and-powershell.aspx) e do [Portal de Gerenciamento do Azure](https://azure.microsoft.com/overview/preview-portal/) é sempre criptografado.

Para estender com segurança seu datacenter local para a nuvem, o Azure fornece a [VPN site a site](vpn-gateway-create-site-to-site-rm-powershell.md) e a [VPN ponto a site](vpn-gateway-point-to-site-create.md), bem como links dedicados com [Rota Expressa](expressroute-introduction.md) (as conexões para Redes Virtuais do Azure sobre VPN são criptografadas).

### Como o Azure implementa o acesso remoto seguro

As conexões com o Portal do Azure sempre devem ser autenticadas e exigem SSL/TLS. Você pode configurar certificados de gerenciamento para habilitar o gerenciamento seguro. Os protocolos seguros padrão do setor, como [SSTP](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) e [IPsec](https://en.wikipedia.org/wiki/IPsec) têm suporte completo.

A [Rota Expressa do Azure](expressroute-introduction.md) permite criar conexões privadas entre os datacenters do Azure e a infraestrutura no seu local ou em um ambiente de colocalização. As conexões da Rota Expressa não passam pela Internet pública. Elas oferecem mais confiabilidade, velocidades mais rápidas, latências menores e maior segurança que os links típicos baseados na Internet. Em alguns casos, o uso de conexões da Rota Expressa para transferir dados entre o local e o Azure pode proporcionar um custo/benefício significativo.

##Log e monitoramento

O Azure fornece logs autenticados de eventos de segurança relevantes que geram uma trilha de auditoria e foram criados para serem resistentes à falsificação. Isso inclui informações do sistema, como logs de eventos de segurança em VMs de infraestrutura do Azure e o AD do Azure. O monitoramento de eventos de segurança inclui a coleta de eventos, como alterações em endereços IP do DHCP ou do DNS, tentativas de acesso a portas, protocolos ou endereços IP que são bloqueados por design, alterações em configurações de firewall ou política de segurança, criação de conta ou grupo, processos inesperados ou instalação de driver.

![Microsoft Antimalware no Azure](./media/azure-security-getting-started\sec-azgsfig5.PNG)

O acesso de usuário privilegiado de registro de logs de auditoria e atividades, as tentativas de acesso autorizado e não autorizado, as exceções do sistema e os eventos de segurança de informação são mantidos por um período estabelecido. A retenção dos logs fica a seu critério porque você configura a coleta de logs e a retenção de acordo com seus requisitos.

### Como o Azure implementa os logs e o monitoramento

O Azure implanta Agentes de Gerenciamento (MA) e agentes do Monitor de Segurança do Azure (ASM) para cada computação, armazenamento ou nó de malha no gerenciamento, sejam nativos ou virtuais. Cada Agente de Gerenciamento é configurado para autenticar uma conta de armazenamento da equipe de serviço com um certificado obtido do repositório de certificados do Azure e encaminha dados de diagnóstico e de evento pré-configurados para a conta de armazenamento. Esses agentes não são implantados em máquinas virtuais de clientes.

Os administradores do Azure acessam logs por meio de um portal da Web para acesso autenticado e controlado aos logs. Um administrador pode analisar, filtrar, correlacionar e analisar logs. As contas de armazenamento da equipe de serviço do Azure para logs são protegidas contra o acesso de administrador direto, ajudando a evitar a violação de log.

A Microsoft coleta logs de dispositivos de rede usando o protocolo Syslog e de servidores de host usando os Serviços de Coleta de Auditoria (ACS) da Microsoft. Esses logs são colocados em um banco de dados de log do qual os alertas de eventos suspeitos serão gerados diretamente para um administrador do Microsoft. O administrador pode acessar e analisar esses logs.

O [Diagnóstico do Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) é um recurso do Azure que permite coletar dados de diagnóstico de um aplicativo em execução no Azure. São dados de diagnóstico para tarefas de depuração e solução de problemas, medição de desempenho, monitoramento de uso de recursos, análise de tráfego e planejamento da capacidade e auditoria. Depois que os dados de diagnóstico forem coletados, eles poderão ser transferidos para uma conta de armazenamento do Azure para persistência. As transferências podem ser agendadas ou sob demanda. O artigo [Gerenciamento de logs de auditoria e de segurança do Microsoft Azure](azure-security-audit-log-management.md) fornece detalhes sobre como coletar essas informações e analisá-las.

##Atenuação de ameaças

Além de isolamento, criptografia e filtragem, o Azure emprega alguns mecanismos e processos de atenuação de ameaças para proteger a infraestrutura e os serviços. Isso inclui controles e tecnologias internos usados para detectar e remediar ameaças avançadas, como DDoS, escalonamento de privilégios e [OWASP Top-10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

Os controles de segurança e os processos de gerenciamento de risco que a Microsoft tem em vigor para proteger sua infraestrutura de nuvem reduzem o risco de incidentes de segurança. Mas, no caso de um incidente, a equipe de Gerenciamento de Incidentes de Segurança (SIM) dentro da equipe do Microsoft Online Security Services & Compliance (OSSC) estará pronta para responder 24 horas por dia, sete dias por semana.

### Como o Azure implementa a atenuação de ameaças

O Azure tem controles de segurança para implementar a atenuação de ameaças e também para auxiliar os clientes a atenuarem potenciais ameaças em seus ambientes. A lista a seguir resume os recursos de atenuação de ameaças oferecidos pelo Azure:

-   O [AntiMalware do Azure](azure-security-antimalware.md) é habilitado por padrão em todos os servidores de infraestrutura. Você pode ativá-lo opcionalmente nas suas próprias VMs.

-   A Microsoft mantém o monitoramento contínuo de servidores, redes e aplicativos para detectar ameaças e impedir explorações. Os alertas automatizados notificam os administradores sobre comportamentos anormais, permitindo que eles tomem ações corretivas contra ameaças internas e externas.

-   Você tem a opção de implantar soluções de segurança de terceiros em suas assinaturas, como firewalls de aplicativo Web da [Barracuda](https://techlib.barracuda.com/ng54/deployonazure).

-   A abordagem da Microsoft para testes de penetração inclui "[Agrupamentos Vermelhos](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)", que envolve profissionais de segurança da Microsoft atacando sistemas de produção (não-cliente) no Azure para testar as defesas contra ameaças persistentes e avançadas do mundo real.

-   Os sistemas de implantação integrados gerenciam a distribuição e a instalação de patches de segurança na plataforma do Azure.

##Próximas etapas

[Central de confiabilidade do Azure](https://azure.microsoft.com/support/trust-center/)

[Blog da equipe de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/)

[Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)

[Blog do Active Directory](http://blogs.technet.com/b/ad/)

<!---HONumber=AcomDC_1203_2015-->