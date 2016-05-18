<properties 
   pageTitle="Opções de resolução de nomes DNS para VMs Linux no Azure"
   description="Cenários de resolução de nomes para VMs Linux no Azure IaaS, incluindo serviços DNS fornecidos, o servidor traga seu próprio DNS e DNS externo híbrido."
   services="virtual-machines"
   documentationCenter="na"
   authors="RicksterCDN"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/11/2016"
   ms.author="rclaus" />

# Opções de resolução de nomes DNS para VMs Linux no Azure

O Azure fornece resolução de nomes DNS por padrão para todas as VMs contidas em uma única Rede Virtual. Você será capaz de implementar sua própria solução de resolução de nomes DNS configurando seus próprios serviços DNS em suas VMs hospedadas no Azure. Os cenários a seguir devem ajudá-lo a escolher qual delas funciona melhor para sua situação específica.

- [Resolução de nomes fornecida pelo Azure](#azure-provided-name-resolution)

- [Resolução de nome usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server)

O tipo de resolução de nomes usado depende de como as VMs e as instâncias de função precisam se comunicar entre si.

**A tabela a seguir ilustra os cenários e as soluções de resolução de nome correspondentes:**

| **Cenário** | **Solução** | **Suffix** |
|--------------|--------------|----------|
| Resolução de nomes entre as instâncias de função ou as máquinas virtuais na mesma rede virtual | [Resolução de nomes fornecida pelo Azure](#azure-provided-name-resolution)| nome de host ou FQDN |
| Resolução de nomes entre instâncias de função ou máquinas virtuais localizadas em redes virtuais diferentes | Servidores DNS gerenciados pelo cliente que encaminham consultas entre redes virtuais para resolução pelo Azure (proxy DNS). Confira [Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server)| Somente FQDN |
| Resolução de nomes de serviço e de computador locais em instâncias de função ou VMs no Azure | Servidores DNS gerenciados pelo cliente (por exemplo, controlador de domínio local, controlador de domínio somente leitura local ou DNS secundário sincronizados usando transferências de zona). Confira [Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server)|Somente FQDN |
| Resolução de nomes de host do Azure em computadores locais | Encaminhe consultas para um servidor de proxy DNS gerenciado pelo cliente na rede virtual correspondente. O servidor proxy encaminha consultas para resolução pelo Azure. Confira [Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server)| Somente FQDN |
| DNS inverso para IPs internos | [Resolução de nome usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) | n/d |

## Resolução de nomes fornecida pelo Azure

Junto com a resolução de nomes DNS públicos, o Azure fornece uma resolução de nomes interna para VMs e instâncias de função que residem na mesma rede virtual. Em redes virtuais baseadas em ARM, o sufixo DNS é consistente em toda a rede virtual (portanto, o FQDN não é necessário) e nomes DNS podem ser atribuídos a NICs e VMs. Embora a resolução de nomes fornecida pelo Azure não requeira qualquer configuração, ela não é a escolha apropriada para todos os cenários de implantação, como mostrado na tabela acima.

### Recursos e considerações

**Recursos:**

- Facilidade de uso: não é necessária nenhuma configuração para usar a resolução de nomes fornecida pelo Azure.

- O serviço de resolução de nomes fornecido pelo Azure é altamente disponível, economizando a necessidade de criar e gerenciar clusters de seus próprios servidores DNS.

- Pode ser usado em conjunto com seus próprios servidores DNS para resolver nomes de host locais e do Azure.

- A resolução de nomes é fornecida entre VMs em redes virtuais sem a necessidade de FQDN.

- Você pode usar nomes de host que descrevem melhor as suas implantações, em vez de trabalhar com nomes gerados automaticamente.

**Considerações:**

- O sufixo DNS criado pelo Azure não pode ser modificado.

- Não é possível registrar manualmente seus próprios registros.

- Não há suporte para o WINS e NetBIOS.

- Os nomes de host devem ser compatíveis com DNS (eles devem usar somente 0-9, a-z e '-' e não podem começar ou terminar com um '-'. Consulte o RFC 3696, seção 2).

- O tráfego da consulta DNS é restrita a cada VM. Isso não deve afetar a maioria dos aplicativos. Se a limitação da solicitação for observada, certifique-se de que o armazenamento em cache do cliente está habilitado. Para obter mais detalhes, consulte [Obtendo o máximo de resolução de nomes fornecida pelo Azure](#Getting-the-most-from-Azure-provided-name-resolution).


### Obtendo o máximo de resolução de nomes fornecida pelo Azure
**Armazenamento em cache no lado cliente:**

Nem todas as consultas DNS precisam ser enviada pela rede. O armazenamento em cache do lado do cliente ajuda a reduzir a latência e melhorar a resistência a pontos de luz de rede resolvendo consultas de DNS recorrentes a partir de um cache local. Os registros DNS contêm um Time-To-Live (TTL) que permite que o cache armazene o registro o maior tempo possível sem afetar a atualização de registro para que o armazenamento em cache do lado do cliente seja adequado para a maioria das situações.

Algumas distribuições do Linux não incluem o armazenamento em cache por padrão. É recomendável que um seja adicionado a cada VM do Linux (após verificação de que ainda não há um cache local).

Há uma série de pacotes de armazenamento em cache de DNS diferentes disponíveis, por exemplo, dnsmasq, aqui estão as etapas para instalar o dnsmasq nas distribuições mais comuns:

- **Ubuntu (usa resolvconf)**:
	- apenas instale o pacote de dnsmasq ("sudo apt-get install dnsmasq").
- **SUSE (usa netconf)**:
	- instale o pacote de dnsmasq ("sudo zypper install dnsmasq"). 
	- habilite o serviço dnsmasq ("systemctl enable dnsmasq.service") 
	- inicie o serviço dnsmasq ("systemctl enable dnsmasq.service") 
	- edite "/etc/sysconfig/network/config" e altere NETCONFIG\_DNS\_FORWARDER = "" para "dnsmasq"
	- atualize resolv. conf ("netconfig update") para definir o cache do resolvedor DNS local
- **OpenLogic (usa NetworkManager)**:
	- instale o pacote de dnsmasq ("sudo yum install dnsmasq").
	- habilite o serviço dnsmasq ("systemctl enable dnsmasq.service")
	- inicie o serviço dnsmasq ("systemctl enable dnsmasq.service")
	- adicione "prepend domain-name-servers 127.0.0.1;" a "/etc/dhclient-eth0.conf"
	- reinicie o serviço de rede ("service network restart") para definir o cache como o resolvedor DNS local

> [AZURE.NOTE]\: O pacote 'dnsmasq' é apenas um dos vários caches DNS disponíveis para o Linux. Antes de usá-lo, verifique sua adequação para suas necessidades específicas e se nenhum outro cache está instalado.

**Tentativa no lado do cliente:**

O DNS é principalmente um protocolo UDP. Como o protocolo UDP não garante a entrega de mensagens, a lógica de repetição é manipulada no próprio protocolo DNS. Cada cliente DNS (sistema operacional) pode apresentar uma lógica de repetição diferente dependendo da preferência dos criadores:

 - Sistemas operacionais Windows tentam novamente depois de 1 segundo e, em seguida, novamente após outros 2, 4 e outros 4 segundos. 
 - As tentativas de instalação do Linux ocorrem, por padrão, depois de 5 segundos. É recomendável alterar para repetir 5 vezes em intervalos de 1 segundo.  

Para verificar as configurações atuais em uma VM do Linux, 'cat /etc/resolv.conf' e examine a linha 'options', por exemplo:

	options timeout:1 attempts:5

O arquivo resolv.conf é normalmente gerado automaticamente e não deve ser editado. As etapas específicas para adicionar a linha 'options' variam de acordo com a distribuição:

- **Ubuntu** (usa resolvconf):
	- adicione a linha de opções para '/etc/resolveconf/resolv.conf.d/head' 
	- execute 'resolvconf -u' para atualizar
- **SUSE** (usa netconf):
	- adicione 'timeout:1 tentativas: 5' ao parâmetro NETCONFIG\_DNS\_RESOLVER\_OPTIONS = "" em '/ etc/sysconfig/rede/config' 
	- execute 'netconfig update' para atualizar
- **OpenLogic** (usa NetworkManager):
	- adicione 'echo "options timeout:1 attempts:5"' em '/etc/NetworkManager/dispatcher.d/11-dhclient' 
	- execute 'service network restart' para atualizar

## Resolução de nome usando o seu próprio servidor DNS
Há várias situações em que suas necessidades de resolução de nomes podem ir além dos recursos fornecidos pelo Azure, por exemplo, ao precisar de resolução DNS entre redes virtuais. Para abordar esse cenário, o Azure oferece a você a capacidade de usar seus próprios servidores DNS.

Os servidores DNS em uma rede virtual podem encaminhar consultas DNS para os resolvedores recursivos do Azure a fim de resolver nomes de host dentro da rede virtual. Por exemplo, um servidor DNS em execução no Azure pode responder a consultas DNS dos seus próprios arquivos de zona DNS e encaminhar todas as outras consultas ao Azure. Isso permite que as VMs vejam tanto suas entradas em seus arquivos de zona quanto nomes de host fornecidos pelo Azure (pelo encaminhador). O acesso a resolvedores recursivos do Azure é fornecido por meio da IP virtual 168.63.129.16.

O encaminhamento de DNS também permite a resolução DNS entre redes virtuais e permite suas máquinas locais resolvam nomes de host fornecidos pelo Azure. Para resolver o nome de host da VM, a VM do servidor DNS deve residir na mesma rede virtual e ser configurado para encaminhar consultas de nome de host ao Azure. Como o sufixo DNS é diferente em cada rede virtual, você pode usar regras de encaminhamento condicionais para enviar consultas DNS a fim de serem resolvidas pela rede virtual correta. A imagem abaixo mostra duas redes virtuais e uma rede local fazendo a resolução de DNS entre redes virtuais usando este método:

![DNS entre redes virtuais](./media/virtual-machines-linux-azure-dns/inter-vnet-dns.png)

Ao usar a resolução de nomes fornecida pelo Azure, o sufixo DNS interno é fornecido para cada máquina virtual que usar o DHCP. Ao usar sua própria solução de resolução de nome, esse sufixo não será fornecido para as VMs porque interfere com outras arquiteturas de DNS. Para fazer referência a máquinas pelo FQDN, ou para configurar o sufixo em suas VMs, ele pode ser determinado usando o PowerShell ou a API:

-  Para redes virtuais gerenciadas do Azure Resource Management, o sufixo está disponível por meio do recurso da [placa de interface de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx), ou você pode executar o comando `azure network public-ip show <resource group> <pip name>` para exibir os detalhes de seu IP público, incluindo o FQDN da NIC.    


Se o encaminhamento de consultas para o Azure não atender às suas necessidades, você precisará fornecer sua própria solução DNS. A solução DNS precisará:

-  Fornecer resolução de nome do host apropriada, por exemplo, via [DDNS](../virtual-network/virtual-networks-name-resolution-ddns.md). Observe que se você usar DDNS, talvez precise desabilitar a limpeza de registro DNS, já que as concessões de DHCP do Azure são muito longas e a limpeza pode remover os registros DNS prematuramente. 
-  Fornecer a resolução recursiva apropriada para permitir a resolução de nomes de domínio externo.
-  Ser acessível (TCP e UDP na porta 53) dos clientes a que ela serve e ser capaz de acessar a Internet.
-  Ter proteção contra acesso da Internet, para atenuar as ameaças impostas por agentes externos.

> [AZURE.NOTE] Para obter um melhor desempenho, ao usar as VMs do Azure como servidores DNS, o IPv6 deve ser desabilitado e um [IP Público em Nível de Instância](../virtual-network/virtual-networks-instance-level-public-ip.md) deve ser atribuído a cada VM do servidor DNS.

<!---HONumber=AcomDC_0427_2016-->