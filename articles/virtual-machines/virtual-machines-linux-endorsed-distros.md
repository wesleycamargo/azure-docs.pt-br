<properties
    pageTitle="Distribuições endossadas do Linux | Microsoft Azure"
    description="Saiba mais sobre o Linux nas distribuições endossadas do Azure, incluindo diretrizes para Ubuntu, OpenLogic, Oracle e SUSE."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management,azure-resource-manager"
    />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>




#<a name="linux-on-azure-endorsed-distributions"></a>Linux em distribuições endossadas pelo Azure

> [AZURE.NOTE] Se tiver alguns minutos, ajude-nos a melhorar a documentação das VMs do Linux do Azure respondendo a essa [pesquisa rápida](https://aka.ms/linuxdocsurvey) sobre suas experiências. Cada resposta nos ajuda a realizar seu trabalho.

As imagens Linux na Galeria do Azure ou no Marketplace são fornecidas por alguns parceiros e estamos trabalhando com várias comunidades Linux para adicionar ainda mais opções à lista de Distribuições Endossadas. Enquanto isso, para distribuições não disponíveis na Galeria, você sempre pode Trazer o Próprio Linux seguindo as diretrizes [nesta página](virtual-machines-linux-classic-create-upload-vhd.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="supported-distributions-&-versions"></a>Distribuições e versões com suporte ##

A tabela a seguir lista as distribuições e versões do Linux com suporte no Azure. Confira também o [suporte para imagens do Linux no Microsoft Azure](https://support.microsoft.com/en-us/kb/2941892) para obter mais informações.

Os drivers LIS (Serviços de Integração do Linux) para Hyper-V e Azure são módulos de kernel que a Microsoft contribui diretamente para o kernel upstream do Linux.  Os drivers LIS também são internos ao kernel da distribuição por padrão ou, para distribuições com base em RHEL/CentOS mais antigas estão disponíveis como um download separado [aqui](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409).  Confira [este artigo](virtual-machines-linux-create-upload-generic.md#linux-kernel-requirements) para saber mais sobre os drivers LIS.

O Agente Linux do Azure já vem pré-instalado nas imagens da Galeria do Azure e normalmente está disponível no repositório de pacotes de distribuição.  O código-fonte pode ser encontrado no [GitHub](https://github.com/azure/walinuxagent).

Distribuição|Versão|Drivers|Agente
---|---|---|---
CentOS by OpenLogic | CentOS 6.3+, 7.0+ | CentOS 6.3: [download do LIS](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 +: no Kernel | Pacote: no [repositório do OpenLogic](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) em "WALinuxAgent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent)
[CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) | 494.4.0+ | No kernel | Código-fonte: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent)
Debian | Debian 7.9+, 8.2+ | No kernel | Pacote: no repositório, em "waagent"  <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent)
Oracle Linux | 6.4+, 7.0+ | No kernel | Pacote: no repositório, em "WALinuxAgent"  <br/>Código-fonte: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
Red Hat Enterprise Linux | RHEL 6.7+, 7.1+ | No kernel|Pacote: no repositório, em "WALinuxAgent"  <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent)
SUSE Linux Enterprise | SLES 11 SP4, SLES 12+ e  <p>  SLES para SAP 11.3+ | No kernel | Pacote: no repositório [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) em "python-azure-agent" <br/>Código-fonte: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
openSUSE | openSUSE 13.2+ | No kernel | Pacote: no repositório [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) em "python-azure-agent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent)
Ubuntu|Ubuntu 12.04, 14.04 e 16.04 | No kernel | Pacote: no repositório em "python-azure-agent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent)


## <a name="partners"></a>Parceiros

### <a name="openlogic"></a>OpenLogic
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

O OpenLogic é líder no fornecimento de soluções empresariais de software livre para a nuvem e o data center. Ele ajuda centenas de empresas líderes de inúmeros setores a adquirir, dar suporte e controlar com segurança o software livre. O OpenLogic oferece suporte técnico de nível comercial e indenização para 600 pacotes de software livre com suporte da OpenLogic Expert Community, inclusive suporte de nível empresarial para CentOS, bem como o parceiro de lançamento para fornecer imagens baseadas no CentOS no Azure.

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

No site CoreOS:

*O CoreOS foi projetado para segurança, consistência e confiabilidade. Em vez de instalar os pacotes por meio de yum ou apt, o CoreOS usa contêineres do Linux para gerenciar seus serviços em um nível mais alto de abstração. Um único código de serviço e todas as dependências são empacotados em um contêiner que pode ser executado em uma ou várias máquinas CoreOS.*


### <a name="credativ"></a>Credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

A Credativ é uma empresa de serviços e consultoria independente, especializada no desenvolvimento e na implementação de soluções profissionais com o uso de software gratuito. A Credative, por ser especialista líder em software livre, é reconhecida internacionalmente e vários departamentos de TI usam o suporte que a empresa fornece. Em conjunto com a Microsoft, a Credativ está preparando imagens correspondentes do Debian para Debian 8 (Jessie) e Debian antes do 7 (Wheezy), projetadas especialmente para execução no Azure e facilmente gerenciadas por meio da plataforma. A empresa também dará suporte a atualização e manutenção de longo prazo das imagens Debian para Azure por meio de seus Centros de Suporte de Software Livre.

### <a name="oracle"></a>Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

A estratégia da Oracle consiste em oferecer um amplo portfólio de soluções para nuvens públicas e privadas, ao mesmo tempo em que dá aos clientes opção e flexibilidade na maneira como eles implantam um software Oracle em nuvens Oracle, bem como em outras nuvens.  A parceria da Oracle com a Microsoft permite aos clientes implantar um software Oracle em nuvens públicas e privadas da Microsoft com a certeza da certificação e do suporte da Oracle.  O compromisso e o investimento da Oracle em soluções de nuvem pública e privada Oracle permanecem inalterados.

### <a name="red-hat"></a>Red Hat
[http://www.redhat.com/en/partners/strategic-alliance/microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

A Red Hat, a principal provedora de soluções de software livre do mundo, ajuda mais de 90% das empresas da Fortune 500 a resolver desafios comerciais, a alinhar suas estratégias comerciais e de TI e a preparar-se para o futuro da tecnologia. A empresa faz isso fornecendo soluções seguras por meio de um modelo de negócios aberto e de um modelo de assinatura acessível e previsível.

### <a name="suse"></a>SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

O SUSE Linux Enterprise Server no Azure é uma plataforma testada que fornece confiabilidade e segurança superiores para a computação em nuvem. A plataforma Linux versátil do SUSE se integra perfeitamente aos serviços de nuvem do Azure para oferecer um ambiente de nuvem facilmente gerenciável. E, com mais de 9.200 aplicativos certificados de mais de 1.800 fornecedores de software independentes para SUSE Linux Enterprise Server, o SUSE garante que as cargas de trabalho em execução compatíveis no datacenter podem ser implantadas com segurança no Azure.

### <a name="canonical"></a>Canônico
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

O controle aberto da comunidade e a engenharia da Canonical impulsionam o sucesso do Ubuntu no cliente, no servidor e na computação em nuvem, incluindo serviços de nuvem pessoais para os consumidores. A visão da Canonical de uma plataforma livre unificada no Ubuntu, do telefone à nuvem, com uma família de interfaces coerentes para telefone, tablet, TV e área de trabalho, torna o Ubuntu a primeira opção para diversas instituições que vão desde provedores de nuvem pública até fabricantes de aparelhos eletrônicos e um favorito entre tecnólogos individuais.

Com desenvolvedores e centros de engenharia no mundo inteiro, a Canonical está posicionada exclusivamente para fazer parceria com fabricantes de hardware, provedores de conteúdo e desenvolvedores de software para oferecer soluções de Ubuntu no mercado, de PCs a servidores e dispositivos portáteis.




<!--HONumber=Oct16_HO2-->


