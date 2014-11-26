<properties linkid="manage-services-networking-active-directory-forest" urlDisplayName="Active Directory forest" pageTitle="Install an Active Directory forest on an Azure virtual network" metaKeywords="" description="A tutorial that explains how to create a new Active Directory forest on a virtual machine (VM) on an Azure Virtual Network." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha"  solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft"  />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha" />

# Instalar uma nova floresta do Active Directory em uma rede virtual do Azure

Este tópico mostra como criar um novo ambiente do Active Directory do Windows Server em uma rede virtual do Azure em uma máquina virtual (VM) em uma [rede virtual do Azure][rede virtual do Azure]. Nesse caso, a rede virtual do Azure não está conectada a uma rede local.

Você também pode estar interessado nestes tópicos relacionados:

-   Você também pode [configurar uma VPN site a site usando o Assistente do Portal de Gerenciamento][configurar uma VPN site a site usando o Assistente do Portal de Gerenciamento] e instalar uma nova floresta ou estender uma floresta local para uma rede virtual do Azure. Para essas etapas, consulte [Instalar um controlador de domínio do Active Directory de réplica em uma rede virtual do Azure][Instalar um controlador de domínio do Active Directory de réplica em uma rede virtual do Azure].
-   Para obter diretrizes conceituais sobre como instalar os Serviços de Domínio Active Directory (AD DS) em uma rede virtual do Azure, consulte [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure][Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure].
-   Para obter orientações passo a passo para criar um ambiente de laboratório de teste no Azure que inclua o AD DS, consulte [Guia do laboratório de teste: Configuração base do Windows Server 2012 R2 no Azure][Guia do laboratório de teste: Configuração base do Windows Server 2012 R2 no Azure].

## Sumário

-   [Como isso difere do local?][Como isso difere do local?]
-   [Etapa 1: Criar uma rede virtual do Azure][Etapa 1: Criar uma rede virtual do Azure]
-   [Etapa 2: Criar uma VM para executar as funções de controlador de domínio e servidor DNS][Etapa 2: Criar uma VM para executar as funções de controlador de domínio e servidor DNS]
-   [Etapa 3: Instalar o Windows Server Active Directory][Etapa 3: Instalar o Windows Server Active Directory]
-   [Etapa 4: Configurar o servidor DNS da rede virtual do Azure][Etapa 4: Configurar o servidor DNS da rede virtual do Azure]
-   [Etapa 5: Criar VMs para membros de domínio e ingressar no domínio][Etapa 5: Criar VMs para membros de domínio e ingressar no domínio]

## <span id="differ"></span></a>Como isso difere do local?

Não há muita diferença entre instalar um controlador de domínio no Azure em comparação com o local. As diferenças principais estão listadas na tabela a seguir.

| Para configurar...                                      | Configuração local                                                                                                      | Rede Virtual do Azure                                                                                                              |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------|
| **Endereço IP do controlador de domínio**               | Atribuir um endereço IP estático nas propriedades do adaptador de rede                                                  | Obter endereço IP por meio do protocolo DHCP e opcionalmente executar o cmdlet Set-AzureStaticVNetIP cmdlet para torná-lo estático |
| **Resolverdor do cliente DNS**                          | Definir endereço de servidor DNS preferencial e alternativo nas propriedades do adaptador de rede de membros do domínio | Definir endereço de servidor DNS nas propriedades de rede virtual                                                                  |
| **Armazenamento de bancos de dado do Active Directory** | Como opção, altere o local de armazenamento padrão de C:\\                                                              | Você precisa alterar o local de armazenamento padrão de C:\\                                                                       |

## <span id="createvnet"></span></a>Etapa 1: Crie uma rede virtual do Azure

1.  Entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Crie uma rede virtual. Clique em **Redes** \> **Criar uma rede virtual**. Use os valores da tabela a seguir para concluir o assistente.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Nesta página do assistente…</th>
    <th align="left">Especifique esses valores</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Detalhes de rede virtual</strong></td>
    <td align="left"><p>Nome: Digite um nome para a sua rede virtual</p>
    <p>Região: Escolha a região mais próxima</p>
    <p>Grupo de afinidade: <strong>Crie um novo grupo de afinidade</strong></p>
    <p>Nome do grupo de afinidade: Digite um nome para o seu grupo de afinidade</p></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>DNS e VPN</strong></td>
    <td align="left"><p>Deixe o servidor DNS em branco</p>
    <p>Não selecione a opção de VPN</p></td>
    </tr>
    <tr class="odd">
    <td align="left"><strong>Espaços de endereço da rede virtual</strong></td>
    <td align="left"><p>Nome da sub-rede: Digite um nome para a sua sub-rede</p>
    <p>IP Inicial: <strong>10.0.0.0</strong></p>
    <p>CIDR: <strong>/24 (256)</strong></p></td>
    </tr>
    </tbody>
    </table>

## <span id="createvm"></span></a>Etapa 2: Crie uma VM para executar as funções de controlador de domínio e servidor DNS

1.  Clique em **Novo** \> **Computação** \> **Máquina Virtual** \> **Da Galeria**.
2.  Use os valores da tabela a seguir para concluir o assistente.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Nesta página do assistente…</th>
    <th align="left">Especifique esses valores</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Sistema operacional</strong></td>
    <td align="left">Select <strong>Windows Server 2012 R2 Datacenter</strong></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Configuração de máquina virtual</strong></td>
    <td align="left"><p>Data do lançamento: Data de hoje</p>
    <p>Nome da máquina: Especifique um único valor</p>
    <p>Camada: Standard</p>
    <p>Tamanho: Selecione um tamanho</p>
    <p>Nome do usuário: Digite um nome. Essa conta de usuário será um membro do grupo interno Administradores.</p>
    <p>Senha: Deve ter pelo menos 8 caracteres e incluir 3 dos seguintes tipos de caracteres:</p>
    <ul>
    <li>uma letra maiúscula</li>
    <li>uma letra minúscula</li>
    <li>um número</li>
    <li>um caractere especial</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left"><strong>Serviço de nuvem</strong></td>
    <td align="left"><p>Serviço de Nuvem: <strong>Crie um novo serviço de nuvem</strong></p>
    <p>Nome do serviço de nuvem: Aceitar valor padrão</p>
    <p>Region/AffinityGroup/VirtualNetwork: Selecione a rede virtual que você criou</p>
    <p>Sub-rede da rede virtual: Selecione a sub-rede da rede virtual que você criou.</p>
    <p>Conta de armazenamento: <strong>Use uma conta de armazenamento gerada automaticamente</strong></p>
    <p>Conjunto de disponibilidade: <strong>Nenhum</strong></p>
    <p>Pontos de extremidade: Aceitar valores padrão</p></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Agente de VM</strong></td>
    <td align="left">Selecione <strong>Instalar o Agente de VM</strong></td>
    </tr>
    </tbody>
    </table>

3.  O endereço IP dinâmico atribuído à VM por padrão é válido durante o serviço de nuvem. Mas ele mudará se a VM for desligada. Você pode atribuir um endereço IP estático [executando o cmdlet do PowerShell do Azure Set-AzureStaticVNetIP][executando o cmdlet do PowerShell do Azure Set-AzureStaticVNetIP] de forma que o endereço IP será mantido se você precisar desligar a VM alguma vez.
4.  Conecte um disco adicional à VM para armazenar o banco de dados, os logs e o SYSVOL do Active Directory.
  5.  Clique na **VM** \> **Anexar** \> **Anexar disco vazio**.
  6.  Especifique um tamanho (por exemplo, 10 GB) e aceite todos os outros valores padrão.
7.  Faça logon na VM e formate o disco adicional.
  8.  Clique em **Conectar** para fazer logon na VM, em **Abrir** para criar uma sessão RDP e em **Conectar** novamente.
  9.  Altere as credenciais para o novo nome de usuário e a senha que você especificou.
  10. No Gerenciador de Servidores, clique em **Ferramentas** \> **Gerenciamento do Computador**.
  11. Clique em **Gerenciamento de Disco** e em **Ok** para inicializar o novo disco.
  12. Clique com o botão direito do mouse no nome do disco e clique em **Novo Volume Simples**. Conclua o assistente para formatar a nova unidade.

## <span id="installad"></span></a>Etapa 3: Instale o Windows Server Active Directory

[Instale o AD DS][Instale o AD DS] usando a mesma rotina que você usa no local (ou seja, você pode usar a interface do usuário, um arquivo de resposta ou o Windows PowerShell). Você precisa fornecer credenciais de administrador para instalar uma nova floresta. Para especificar o local do banco de dados, dos logs e do SYSVOL do Active Directory, altere o local de armazenamento padrão da unidade do sistema operacional pra o disco de dados adicional que você conectou à VM.

Após a instalação do DC, conecte-se novamente à VM e faça logon no DC. Não se esqueça de especificar credenciais de domínio.

</p>
## <span id="dns"></span></a>Etapa 4: Configure o servidor DNS da rede virtual do Azure

1.  Clique em **Redes Virtuais**, clique duas vezes na rede virtual que você criou e clique em **Configurar**.
2.  Em **Servidores DNS**, digite o nome e o DIP do DC e clique em **Salvar**.
3.  Selecione a VM e clique em **Reiniciar** para disparar a VM e definir as configurações de resolvedor DNS com o endereço IP do novo servidor DNS.

## <span id="domainmembers"></span></a>Etapa 5: Crie VMs para membros de domínio e ingressar no domínio

Crie VMs adicionais para provisionar computadores membro do domínio. Você pode usar a interface do usuário ou o PowerShell do Azure. Se você usar a interface do usuário, basta seguir as mesmas etapas que você usou para criar a primeira VM. Em seguida, una as VMs ao domínio como você faria no local. Se usar o PowerShell do Azure, você poderá provisionar VMs e ingressá-las no domínio quando forem iniciadas pela primeira vez.

Esse exemplo irá criar uma VM de domínio associado chamada de DC2 que executa o Windows Server 2012 R2 Datacenter. Após a DC2 ser provisionada, faça logon como Adm do Domínio e promova-a para ser uma réplica de DC.

Você pode executar o Get-AzureVMImage para obeter nomes de imagem. Por exemplo, para retornar uma lista de imagens para o Windows Server 2012 R2, execute o Get-AzureVMImage | where-object {$\_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"}.

    '

    cls

    Set-AzureSubscription -SubscriptionName "Free Trial" -currentstorageaccountname 'constorageaccount'
    Select-AzureSubscription -SubscriptionName "Free Trial"

    #Deploy a new VM and join it to the domain
    #-------------------------------------------
    #Specify my DC's DNS IP (10.0.0.4)
    $myDNS = New-AzureDNS -Name 'DC1' -IPAddress '10.0.0.4'

    # OS Image to Use
    $image = 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd'
    $service = 'DC2'
    $AG = 'YourAffinityGroup'
    $vnet = 'YourVirtualNetwork'
    $pwd = 'P@ssw0rd'
    $size = 'Small'

    #VM Configuration
    $vmname = 'DC2'
    $MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

    New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

Se reexecutar o script, você precisará fornecer um valor exclusivo para $service. Você pode executar Test-AzureName -Service *nome do serviço*, que retornará se o nome já estiver sendo usado.

## Consulte também

-   [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure][1]

-   [Configure uma rede virtual somente em nuvem no Portal de Gerenciamento][Configure uma rede virtual somente em nuvem no Portal de Gerenciamento]

-   [Configure um VPN site a site no Portal de Gerenciamento][Configure um VPN site a site no Portal de Gerenciamento]

-   [Instalar um controlador de domínio do Active Directory de réplica em uma rede virtual do Azure][2]

-   [Windows Azure IaaS para profissionais de TI: (01) Conceitos básicos sobre máquina virtual][Windows Azure IaaS para profissionais de TI: (01) Conceitos básicos sobre máquina virtual]

-   [Windows Azure IaaS para profissionais de TI: (05) Criando redes virtuais e conectividade entre instalações][Windows Azure IaaS para profissionais de TI: (05) Criando redes virtuais e conectividade entre instalações]

-   [Rede Virtual do Azure][rede virtual do Azure]

-   [Como instalar e configurar o PowerShell do Azure][Como instalar e configurar o PowerShell do Azure]

-   [PowerShell do Azure][PowerShell do Azure]

-   [Cmdlets gerenciamento do Azure][Cmdlets gerenciamento do Azure]

-   [Definir o endereço IP estático da VM do Azure][Definir o endereço IP estático da VM do Azure]

-   [Como atribuir um IP estático para uma VM do Azure][Como atribuir um IP estático para uma VM do Azure]

-   [Instalar uma nova floresta do Active Directory][Instale o AD DS]

-   [Introdução à virtualização de Serviços de Domínio Ative Directory (AD DS) (nível 100)][Introdução à virtualização de Serviços de Domínio Ative Directory (AD DS) (nível 100)]

-   [Guia de Laboratório de Teste: Configuração base do Windows Server 2012 R2 no Azure][Guia do laboratório de teste: Configuração base do Windows Server 2012 R2 no Azure]

  [rede virtual do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj156007.aspx
  [configurar uma VPN site a site usando o Assistente do Portal de Gerenciamento]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn133795.aspx
  [Instalar um controlador de domínio do Active Directory de réplica em uma rede virtual do Azure]: http://www.windowsazure.com/pt-br/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
  [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj156090.aspx
  [Guia do laboratório de teste: Configuração base do Windows Server 2012 R2 no Azure]: http://www.microsoft.com/pt-br/download/details.aspx?id=41684
  [Como isso difere do local?]: #differ
  [Etapa 1: Criar uma rede virtual do Azure]: #createvnet
  [Etapa 2: Criar uma VM para executar as funções de controlador de domínio e servidor DNS]: #createvm
  [Etapa 3: Instalar o Windows Server Active Directory]: #installad
  [Etapa 4: Configurar o servidor DNS da rede virtual do Azure]: #dns
  [Etapa 5: Criar VMs para membros de domínio e ingressar no domínio]: #domainmembers
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  [executando o cmdlet do PowerShell do Azure Set-AzureStaticVNetIP]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn630228.aspx
  [Instale o AD DS]: http://technet.microsoft.com/library/jj574166.aspx
  [1]: http://msdn.microsoft.com/library/azure/jj156090.aspx
  [Configure uma rede virtual somente em nuvem no Portal de Gerenciamento]: http://msdn.microsoft.com/pt-br/library/dn631643.aspx
  [Configure um VPN site a site no Portal de Gerenciamento]: http://msdn.microsoft.com/pt-br/library/dn133795.aspx
  [2]: http://azure.microsoft.com/pt-br/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
  [Windows Azure IaaS para profissionais de TI: (01) Conceitos básicos sobre máquina virtual]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01
  [Windows Azure IaaS para profissionais de TI: (05) Criando redes virtuais e conectividade entre instalações]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05
  [Como instalar e configurar o PowerShell do Azure]: http://www.windowsazure.com/pt-br/documentation/articles/install-configure-powershell/
  [PowerShell do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj156055.aspx
  [Cmdlets gerenciamento do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj152841
  [Definir o endereço IP estático da VM do Azure]: http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address
  [Como atribuir um IP estático para uma VM do Azure]: http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/
  [Introdução à virtualização de Serviços de Domínio Ative Directory (AD DS) (nível 100)]: http://technet.microsoft.com/pt-br/library/hh831734.aspx
