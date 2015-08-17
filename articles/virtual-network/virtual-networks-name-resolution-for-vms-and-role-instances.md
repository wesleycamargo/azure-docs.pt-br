<properties 
   pageTitle="Resolução para máquinas virtuais e instâncias de função"
   description="Cenários de resolução de nomes para o Azure IaaS, soluções híbridas, entre diferentes serviços de nuvem, Active Directory e usando o seu próprio servidor DNS"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2015"
   ms.author="joaoma" />

# Resolução de nomes de máquinas virtuais e instâncias de função

Dependendo de como você usar o Azure para hospedar o IaaS, o PaaS e as soluções híbridas, pode ser necessário permitir que as máquinas virtuais e as instâncias de função que você cria se comuniquem com outras máquinas virtuais e instâncias de função. Embora essa comunicação possa ser feita usando endereços IP, é muito mais simples usar nomes de host que possam ser facilmente lembrados. No entanto, esses nomes de host devem ser resolvidos para endereços IP de alguma forma para estabelecer a comunicação.

Quando as instâncias de função e as VMs hospedadas no Azure precisarem resolver nomes de hosts e nomes de domínio para endereços IP internos, elas podem usar um dos dois métodos:

- [Resolução de nomes fornecida pelo Azure](azure-provided-name-resolution)

- [Resolução de nome usando o seu próprio servidor DNS](name-resolution-using-your-own-DNS-server)

O tipo de resolução de nome que você usa depende de como as suas VMs e instâncias de função precisam se comunicar no seu serviço de nuvem e com outros serviços de nuvem.

**A tabela a seguir ilustra os cenários e as soluções de resolução de nome correspondentes:**

| **Cenário** | **Resolução de nomes fornecida por:** | **Para obter mais informações, consulte:** |
|------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Resolução de nomes entre as instâncias de função ou as máquinas virtuais no mesmo serviço de nuvem | Resolução de nomes fornecida pelo Azure | [Resolução de nomes fornecida pelo Azure](#azure-provided-name-resolution) |
| Resolução de nomes entre máquinas virtuais e instâncias de função localizadas na mesma rede virtual | Resolução de nomes fornecida pelo Azure – usando a resolução de FQDNorName usando o seu próprio servidor DNS | - [Resolução de nomes fornecida pelo Azure](#azure-provided-name-resolution) - [resolução de nomes usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) - [requisitos do servidor DNS](#dns-server-requirements) |
| Resolução de nomes entre máquinas virtuais e instâncias de função localizadas em redes virtuais diferentes | Resolução de nome usando o seu próprio servidor DNS | - [Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) - [requisitos do servidor DNS](#dns-server-requirements) |
| Entre instalações: Resolução de nomes entre instâncias de função ou máquinas virtuais no Azure e em computadores locais | Resolução de nome usando o seu próprio servidor DNS | - [Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) - [requisitos do servidor DNS](#dns-server-requirements) |
| Pesquisa inversa de IPs internos | Resolução de nome usando o seu próprio servidor DNS | - [Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) - [requisitos do servidor DNS](#dns-server-requirements) |
| Resolução de nomes para domínios personalizados (por exemplo, domínios do Active Directory, domínios que você registrar) | Resolução de nome usando o seu próprio servidor DNS | - [Resolução de nomes usando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) - [requisitos do servidor DNS](#dns-server-requirements) |
| Resolução de nomes entre instâncias de função localizadas em serviços de nuvem diferente, não em uma rede virtual | Não aplicável. Não há suporte para a conectividade entre máquinas virtuais e instâncias de função em diferentes serviços de nuvem fora de uma rede virtual. | Não aplicável. |

> [AZURE.NOTE]A resolução de nomes entre computadores na Internet e seus pontos de extremidade públicos é fornecida pelo Azure automaticamente e não requer nenhuma configuração.

## Resolução de nomes fornecida pelo Azure

Junto com a resolução para domínios da Internet pública, o Azure fornece resolução de nomes de host para máquinas virtuais e instâncias de função que residem no mesmo serviço de nuvem usando os nomes de host, e entre VMs e instâncias de função em diferentes serviços de nuvem que residem na mesma rede virtual usando FQDNs. Embora a resolução de nomes fornecida pelo Azure não requeira qualquer configuração, ela não é a escolha apropriada para todos os cenários de implantação, como mostrado na tabela acima.

> [AZURE.NOTE]No caso das funções web e de trabalho, você também pode acessar os endereços IP internos das instâncias de função, conforme o número de instâncias e o nome da função usando a API REST do Gerenciamento de Serviços do Azure. Para obter mais informações, consulte [Referência de API REST do Gerenciamento de Serviços](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Recursos e considerações

**Recursos:**

- Facilidade de uso: não é necessária nenhuma configuração para usar o serviço DNS fornecido pelo Azure.

- A resolução de nome de host é fornecida entre instâncias de função ou máquinas virtuais dentro do mesmo serviço de nuvem.

- A resolução de nomes é fornecida entre instâncias de função e VMs localizadas na mesma rede virtual, mas em diferentes serviços de nuvem, usando o FQDN da instância da função de destino ou a VM.

- Você pode criar os nomes de host que melhor descreverão as suas implantações, em vez de trabalhar com nomes gerados automaticamente.

- Há suporte para as pesquisas padrão de DNS para resolver nomes de domínio público.

**Considerações:**

- A resolução de nomes entre redes virtuais não está disponível.

- Só é possível registrar nomes de host e FQDNs para VMs e instâncias de função que residem nos primeiros 180 serviços de nuvem adicionados a uma rede virtual do Azure. Se você tiver mais de 180 serviços de nuvem, independentemente do número de máquinas virtuais e instâncias de função em cada serviço, você precisará fornecer o seu próprio servidor DNS para a resolução de nomes.

- Não há suporte para o uso de vários nomes de host para a mesma máquina virtual ou instância de função.

- A resolução de nomes entre locais não está disponível.

- O sufixo DNS criado pelo Azure não pode ser modificado.

- Você não pode registrar manualmente os seus próprios registros no DNS fornecido pelo Azure.

- Não há suporte para o WINS e NetBIOS. (Você não pode listar suas máquinas virtuais no navegador de rede no Windows Explorer).

- Os nomes de host devem ser compatíveis com DNS (eles devem usar somente 0-9, a-z e '-' e não podem começar ou terminar com um '-'. Consulte o RFC 3696, seção 2).

- O tráfego de consultas DNS é limitado pela VM. Se o seu aplicativo executar consultas frequentes de DNS em vários nomes de destino, é possível que algumas consultas atinjam o tempo limite. Para evitar isso, é recomendável usar o caching de cliente.

## Resolução de nome usando o seu próprio servidor DNS

Se os seus requisitos de resolução de nomes forem além dos recursos fornecidos pelo Azure, você tem a opção de usar o seu próprio servidor DNS. Quando você usa o seu próprio servidor DNS, você é responsável por gerenciar os registros necessários para os serviços de nuvem.

> [AZURE.NOTE]É recomendável evitar o uso de um servidor DNS externo, a menos que o cenário de implantação exija.

## Requisitos do servidor DNS

Se você planeja usar a resolução de nome que não é fornecida pelo Azure, o servidor DNS que você especificar deverá dar suporte ao seguinte: O servidor DNS deve aceitar o registro DNS dinâmico por meio de Dynamic DNS (DDNS).

- O servidor DNS deve ter a limpeza de registro desativada. Os endereços IP do Azure têm concessões longas, que podem resultar na remoção de registros do servidor DNS durante a limpeza.

- O servidor DNS deve estar com a recursão habilitada para permitir a resolução de nomes de domínio externo.

- O servidor DNS deve ser acessível (na TCP/UDP, porta 53) por clientes que solicitarem a resolução de nomes e pelos serviços e máquinas virtuais que registrarão seus nomes.

- Também é recomendável proteger o servidor DNS contra o acesso pela Internet, pois vários robôs procuram resolvedores DNS recursivo abertos.


## Especificar servidores DNS

Você pode especificar vários servidores DNS a serem usados por suas VMs e instâncias de função. No entanto, quando você fizer isso, os servidores DNS serão usados na ordem em que eles forem especificados em uma forma de failover (em vez de round robin). Para cada consulta DNS, o cliente experimentará primeiro o servidor DNS preferencial e tentará os servidores alternativos somente se aquele preferencial não responder. Por esse motivo, verifique se os servidores DNS listados estão na ordem correta para o seu ambiente.

> [AZURE.NOTE]Se alterar as configurações DNS em um arquivo de configuração de rede para rede virtual que já foi implantado, você precisará reiniciar cada máquina virtual para que as alterações entrem em vigor.

### Especificar um servidor DNS usando o Portal de Gerenciamento

Ao criar a sua rede virtual usando o Portal de Gerenciamento, você pode especificar o endereço IP e o nome do servidor DNS (ou servidores) que você deseja usar. Depois de criar a rede virtual, as máquinas virtuais e as instâncias de função implantadas na rede virtual são configuradas automaticamente com as configurações DNS especificadas, a menos que você especifique quais servidores DNS devem ser usados para a implantação. Para obter mais informações sobre como definir configurações de Rede Virtual do Azure, consulte [Sobre como configurar uma Rede Virtual no Portal de Gerenciamento](https://msdn.microsoft.com/library/azure/jj156074.aspx).

> [AZURE.NOTE]Você só pode usar até 9 servidores DNS.

### Especificar um servidor DNS usando arquivos de configuração

Você pode especificar as configurações DNS usando dois arquivos de configuração diferentes: o arquivo *Configuração de rede* e o arquivo *Configuração do serviço*.

O arquivo de configuração de rede é criado para cada rede virtual que você adiciona no Azure. Quando você adiciona instâncias de função ou máquinas virtuais a qualquer serviço de nuvem em uma rede virtual, as configurações DNS do seu arquivo de configuração de rede são aplicadas à instância de função ou à máquina virtual, a menos que o arquivo de configuração de serviço tenha as suas próprias configurações DNS.

O arquivo de configuração de serviço é criado para cada serviço de nuvem que você adiciona ao Azure. Quando você adicionar instâncias de função ou máquinas virtuais ao serviço de nuvem, as configurações DNS do seu arquivo de configuração de serviço serão aplicadas à instância de função ou à máquina virtual.

> [AZURE.NOTE]As configurações no arquivo de configuração de serviço substituem as configurações no arquivo de configuração de rede. Por exemplo, se uma VM for adicionada a um serviço de nuvem que faz parte de uma rede virtual, e o arquivo de configuração de rede e o arquivo de configuração de serviço têm configurações DNS, as configurações DNS no arquivo de configuração de serviço serão aplicadas à VM.


## Consulte também

[Esquema de configuração de serviço do Azure](https://msdn.microsoft.com/library/azure/ee758710) [Esquema de configuração de Rede Virtual](https://msdn.microsoft.com/library/azure/jj157100) [Sobre como definir configurações de Rede Virtual no Portal de Gerenciamento](https://msdn.microsoft.com/library/azure/jj156074.aspx) [Configurar uma Rede Virtual usando um arquivo de configuração de rede](https://msdn.microsoft.com/library/azure/jj156097.aspx) [Tarefas de configuração de Rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj156206.aspx)

<!---HONumber=August15_HO6-->