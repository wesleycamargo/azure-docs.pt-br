<properties linkid="manage-linux-other-resources-endorsed-distributions" urlDisplayName="Endorsed distributions" pageTitle="Endorsed distributions of Linux in Azure" metaKeywords="" description="Learn about Linux on Azure-endorsed distributions, including guidelines for Ubuntu, OpenLogic, and SUSE." metaCanonical="" services="virtual-machines" documentationCenter="" title="Linux on Azure-Endorsed Distributions" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Linux em distribuições endossadas pelo Azure

As imagens de distribuição na Galeria do Azure são fornecidas pelos parceiros a seguir, e estamos trabalhando junto a diversas comunidades Linux para trazer ainda mais distribuições endossadas. Enquanto isso, para distribuidores não disponíveis na Galeria, você sempre pode Trazer o Próprio Linux seguindo as diretrizes [desta página][desta página].

## Canônico

[][]<http://www.ubuntu.com/cloud/azure></a>

O controle aberto da comunidade e a engenharia da Canonical impulsionam o sucesso do Ubuntu no cliente, no servidor e na computação em nuvem, incluindo serviços de nuvem pessoais para os consumidores. A visão da Canonical de uma plataforma livre unificada no Ubuntu, do telefone à nuvem, com uma família de interfaces coerentes para telefone, tablet, TV e área de trabalho, torna o Ubuntu a primeira opção para diversas instituições que vão desde provedores de nuvem pública até fabricantes de aparelhos eletrônicos e um favorito entre tecnólogos individuais.

Com desenvolvedores e centros de engenharia no mundo inteiro, a Canonical está posicionada exclusivamente para fazer parceria com fabricantes de hardware, provedores de conteúdo e desenvolvedores de software para oferecer soluções de Ubuntu no mercado, de PCs a servidores e dispositivos portáteis.

## OpenLogic

[][1]<http://www.openlogic.com/azure></a>

O OpenLogic é líder no fornecimento de soluções empresariais de software livre para a nuvem e o data center. Ele ajuda centenas de empresas líderes de inúmeros setores a adquirir, dar suporte e controlar com segurança o software livre. O OpenLogic oferece suporte técnico de nível comercial e indenização para 600 pacotes de software livre com suporte da OpenLogic Expert Community, inclusive suporte de nível empresarial para CentOS, bem como o parceiro de lançamento para fornecer imagens baseadas no CentOS no Azure.

## Oracle

[][2]<http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html></a>

A estratégia da Oracle consiste em oferecer um amplo portfólio de soluções para nuvens públicas e privadas, ao mesmo tempo em que dá aos clientes opção e flexibilidade na maneira como eles implementam um software Oracle em nuvens Oracle, bem como em outras nuvens. A parceria da Oracle com a Microsoft permite aos clientes implantar um software Oracle em nuvens públicas e privadas da Microsoft com a certeza da certificação e do suporte da Oracle. O compromisso e o investimento da Oracle em soluções de nuvem pública e privada Oracle permanecem inalterados.

## SUSE

[][3]<http://www.suse.com/suse-linux-enterprise-server-on-azure></a>

O SUSE Linux Enterprise Server no Azure é uma plataforma testada que fornece confiabilidade e segurança superiores para a computação em nuvem. A plataforma Linux versátil do SUSE se integra perfeitamente aos serviços de nuvem do Azure para oferecer um ambiente de nuvem facilmente gerenciável. E, com mais de 9.200 aplicativos certificados de mais de 1.800 fornecedores de software independentes para SUSE Linux Enterprise Server, o SUSE garante que as cargas de trabalho em execução compatíveis no datacenter podem ser implantadas com segurança no Azure.

## Versões com suporte

A tabela a seguir mostra as versões de distribuição diferentes, os drivers LIS (Serviços de Integração do Linux) e as versões do Agente Linux do Azure cujo funcionamento foi testado no Azure. Os drivers LIS são inseridos no kernel da distribuição por padrão ou permanecem disponíveis [aqui][aqui]. As versões do Agente Linux estão disponíveis no repositório do pacote de distribuição ou em [Github][Github].

A tabela também inclui um link para o [patch de compatibilidade do kernel Linux][patch de compatibilidade do kernel Linux] exigido por algumas versões de distribuição/kernel para funcionar da maneira ideal no Azure.

<table border="1" width="600">
<tr bgcolor="#E9E7E7">
<th>
Distribuição

</th>
<th>
Versão

</th>
<th>
Drivers

</th>
<th>
Patch de compatibilidade do kernel

</th>
<th>
Agente

</th>
</tr>
<tr>
<th>
Canonical Ubuntu

</th>
<td>
Ubuntu 12.04.1+, 13.10 e 14.04

</td>
<td>
No kernel

</td>
<td>
<a href="http://go.microsoft.com/fwlink/?LinkID=275152&amp;clcid=0x409">Necessário apenas para 12.04 ou 12.04.01</a>

</td>
<td>
Pacote: No repositório de pacotes em walinuxagent
 Origem: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a>

</td>
</tr>
<tr>
<th>
CentOS by OpenLogic

</th>
<td>
CentOS 6.3+

</td>
<td>
CentOS 6.3: <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">Drivers LIS</a>; CentOS 6.4+ drivers: no Kernel

</td>
<td>
<a href="http://go.microsoft.com/fwlink/?LinkID=275153&amp;clcid=0x409">Necessário apenas para 6.3</a>

</td>
<td>
Pacote: No <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">repositório de pacotes do Open,</a> em walinuxagent
 Origem: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a>
</td>
</tr>
<tr>
<th>
Oracle Linux

</th>
<td>
6.4+

</td>
<td>
No kernel

</td>
<td>
N/D

</td>
<td>
Pacote: No repositório, nomeie: WALinuxAgent
 Origem: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a>

</td>
</tr>
<tr>
<th>
SUSE Linux Enterprise

</th>
<td>
SLES 11 SP3+

</td>
<td>
No kernel

</td>
<td>
N/D

</td>
<td>
Pacote: No repositório <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Nuvem:Ferramentas</a>, nomeie: WALinuxAgent
 Código-fonte: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a>

</td>
</tr>
<tr>
<th>
openSUSE

</th>
<td>
openSUSE 13.1+

</td>
<td>
No kernel

</td>
<td>
N/D

</td>
<td>
Pacote: No repositório <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Nuvem:Ferramentas</a>, nomeie: WALinuxAgent
 Código-fonte: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a>

</td>
</tr>
</table>

  [desta página]: ../virtual-machines-linux-create-upload-vhd/
  []: http://www.ubuntu.com/cloud/azure
  [1]: http://www.openlogic.com/azure
  [2]: http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html
  [3]: http://www.suse.com/suse-linux-enterprise-server-on-azure
  [aqui]: http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409
  [Github]: https://github.com/azure/walinuxagent
  [patch de compatibilidade do kernel Linux]: http://go.microsoft.com/fwlink/?LinkID=403027&clcid=0x409
