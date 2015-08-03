<properties
  pageTitle="Conectando funções dos Serviços de Nuvem do Azure a um controlador de domínio do AD personalizado hospedado no Azure"
  description="Saiba como conectar suas funções web/de trabalho a um domínio do AD personalizado usando o Powershell e a extensão de domínio do AD"
  services="cloud-services"
  documentationCenter=""
  authors="VMak"
  manager="MadhanA"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/05/2015"
    ms.author="vmaker"/>

# Conectando funções dos Serviços de Nuvem do Azure a um controlador de domínio do AD personalizado hospedado no Azure

## Guia por etapas para conectar suas funções de trabalho/web do Azure a um controlador de domínio personalizado hospedado no Azure

Primeiro iremos definir a uma Rede Virtual (VNET) no Azure. Em seguida, adicionaremos um Controlador de Domínio Active Directory (hospedado em uma Máquina Virtual do Azure) à VNET. Em seguida, adicionaremos funções de serviço de nuvem existentes à VNET pré-criada e as conectaremos posteriormente ao controlador de domínio.

Antes de começar, algumas das coisas que você precisa ter em mente: 1 Este tutorial usa o Powershell, portanto, certifique-se de que o Powershell do Azure esteja instalado e pronto. Para obter ajuda na configuração do Powershell do Azure, consulte [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md). 2. As instâncias do controlador de domínio do AD e a função web/de trabalho precisam estar na VNET.

Siga este guia passo a passo e, se tiver algum problema, deixe um comentário abaixo. Alguém entrará em contato com você (sim, lemos comentários).

## Criar uma rede virtual

Você pode criar uma Rede Virtual do Azure usando o Portal do Azure ou o Powershell. Para este tutorial, usaremos o Powershell. Para criar uma Rede virtual usando o Portal do Azure, consulte [Criar uma rede virtual](../create-virtual-network.md).

    #Create Virtual Network

    $vnetStr =
    @"<?xml version="1.0" encoding="utf-8"?>
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
            <AddressSpace>
              <AddressPrefix>[your-address-prefix]</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="[your-subnet-name]">
                <AddressPrefix>[your-subnet-range]</AddressPrefix>
              </Subnet>
            </Subnets>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
    "@;

    $vnetConfigPath = "<path-to-vnet-config>"
    Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath;

## Criar uma Máquina Virtual

Depois de ter concluído a configuração da rede virtual, você precisará criar um controlador de domínio do AD. Para este tutorial, vamos configurar um controlador de domínio do AD em uma máquina virtual do Azure.

Para fazer isso, crie uma máquina virtual por meio do Powershell usando os comandos a seguir.

    #Initialize variables

    $vnetname = '<your-vnet-name>'
    $subnetname = '<your-subnet-name>'
    $vmsvc1 = ‘<your-hosted-service>’
    $vm1 = ‘<your-vm-name>’
    $username = ‘<your-username>’
    $password = ‘<your-password>’
    $ affgrp = ‘<your- affgrp>’

    #Create a VM and add it to the Virtual Network

    New-AzureQuickVM -Windows -ServiceName $vmsvc1 -name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname


## Promova a máquina virtual a um controlador de domínio
Para configurar a máquina virtual como um controlador de domínio do AD, você precisará fazer logon na VM e configurá-la.

Para fazer logon na VM, você pode obter o arquivo RDP por meio do Powershell. Use os comandos a seguir.

    #Get RDP file

    Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>

Depois de fazer logon na VM, configure sua Máquina virtual como um Controlador de domínio do AD, seguindo o guia passo a passo [Como configurar o Controlador de domínio do AD do cliente](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## Adicionar a implantação do serviço de nuvem na rede virtual

Em seguida, você precisa adicionar a implantação do serviço de nuvem à VNET que acabou de criar. Para fazer isso, modifique seu cscfg do serviço de nuvem adicionando as seções relevantes ao seu cscfg usando o Visual Studio ou o editor de sua escolha.

    <ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
        <Role name="[role-name]">
        <Instances count="[number-of-instances]" />
      </Role>
      <NetworkConfiguration>

        <!--optional-->
        <Dns>
          <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
        </Dns>
        <!--optional-->

        <!--VNET settings-->
        <VirtualNetworkSite name="[virtual-network-name]" />
        <AddressAssignments>
          <InstanceAddress roleName="[role-name]">
            <Subnets>
              <Subnet name="[subnet-name]" />
            </Subnets>
          </InstanceAddress>
        </AddressAssignments>
        <!--VNET settings-->

      </NetworkConfiguration>
    </ServiceConfiguration>

Em seguida, compile o projeto de serviços de nuvem e implante-o no Azure. Para obter ajuda com a implantação do pacote de serviços de nuvem no Azure, consulte [Como criar e implantar um Serviço de Nuvem](cloud-services-how-to-create-deploy.md#deploy)

## Conecte a sua função web/de trabalho ao domínio personalizado usando a extensão de domínio do AD

Quando seu projeto de serviço de nuvem for implantado no Azure, conecte suas instâncias de função ao domínio do AD personalizado usando a extensão de domínio do AD. Para adicionar a extensão de domínio do AD à sua implantação de serviços de nuvem existente e ingressar no domínio personalizado, execute os seguintes comandos do Powershell:

    #Initialize domain variables

    $domain = ‘<your-domain-name>’;
    $dmuser = ‘$domain<your-username>’;
    $dmpswd = '<your-domain-password>';
    $dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force;
    $dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd);

    #Add AD Domain Extension to the cloud service roles

    Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35;

E isso é tudo.

Os serviços de nuvem agora devem ter ingressado no seu controlador de domínio personalizado. Se você gostaria de saber mais sobre as diferentes opções disponíveis para configurar a extensão do domínio do AD, use a Ajuda do PS conforme mostrado abaixo.

    help Set-AzureServiceADDomainExtension;
    help New-AzureServiceADDomainExtensionConfig;

Também gostaríamos de receber seus comentários sobre se seria útil ter uma extensão que promovesse uma máquina virtual para um controlador de domínio. Então, se você acha que seria, informe-nos na seção de comentários.

Esperamos que você tenha achado este material útil!
 

<!---HONumber=July15_HO4-->