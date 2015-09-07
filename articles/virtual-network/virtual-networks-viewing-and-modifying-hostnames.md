<properties 
   pageTitle="Exibindo e modificando os nomes do host"
	description="Descrição"
	services="virtual-network"
	documentationCenter="na"
	authors="joaoma"
	manager="jdial"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/25/2015"
	ms.author="joaoma"/>

# Exibindo e modificando os nomes do host

Para permitir que as instâncias de função sejam referenciadas pelo nome do host, você deve definir o valor para o nome do host no arquivo de configuração de serviço para cada função. Você pode fazer isso adicionando o nome do host desejado ao atributo **vmName** do elemento **Função**. O valor do atributo **vmName** é usado como base para o nome do host de cada instância de função. Por exemplo, se **vmName** for *webrole* e houver três instâncias dessa função, os nomes do host das instâncias serão *webrole0*, *webrole1* e *webrole2*. Você não precisa especificar um nome do host para máquinas virtuais no arquivo de configuração, porque o nome do host para uma máquina virtual é preenchido com base no nome da máquina virtual. Para obter mais informações sobre como configurar um serviço do Microsoft Azure, consulte [Esquema de configuração do serviço do Azure (arquivo .cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## Exibindo nomes do host

Você pode exibir os nomes de host de máquinas virtuais e instâncias de função em um serviço de nuvem usando várias ferramentas: o Portal do Azure, o arquivo de configuração de serviço, a área de trabalho remota e a [API REST do Gerenciamento de Serviços do Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Portal do Azure

Você pode usar o portal do Azure para exibir os nomes do host para as máquinas virtuais na página do painel da máquina virtual. Tenha em mente que o painel mostra um valor para **Nome** e **Nome do Host**. Embora inicialmente eles sejam os mesmos, alterar o nome do host não alterará o nome da máquina virtual ou da instância de função.

As instâncias de função também podem ser localizadas no Portal do Azure, mas quando você lista as instâncias em um serviço de nuvem, o nome do host não é exibido. Você verá um nome para cada instância, mas esse nome não representa o nome do host.

### Arquivo de configuração de serviço

Você pode baixar o arquivo de configuração de serviço para um serviço implantado na página **Configurar** do serviço no portal do Azure. Em seguida, procure o atributo **vmName** para o elemento **Nome da função** para ver o nome do host. Tenha em mente que esse nome do host é usado como base para o nome do host de cada instância de função. Por exemplo, se **vmName** for *webrole* e houver três instâncias dessa função, os nomes do host das instâncias serão *webrole0*, *webrole1* e *webrole2*.

### Área de Trabalho Remota

Após habilitar a área de trabalho remota (Windows), comunicação remota do Windows PowerShell (Windows) ou as conexões SSH (Linux e Windows) para suas máquinas virtuais ou instâncias de função, você pode exibir o nome do host de uma conexão de área de trabalho remota ativa de várias maneiras:

- Digite o nome do host no prompt de comando ou no terminal do SSH.

- Digite ipconfig /all no prompt de comando (somente Windows).

- Exiba o nome do computador nas configurações do sistema (somente Windows).

### API REST de Gerenciamento de Serviços do Azure

Em um cliente REST, siga estas instruções:

1. Certifique-se de que você tenha um certificado do cliente para se conectar ao Portal do Azure. Para obter um certificado do cliente, siga as etapas apresentadas em [Como baixar e importar configurações de publicação e informações de assinatura] (https://msdn.microsoft.com/library/dn385850(v=nav.70).aspx).

1. Defina uma entrada de cabeçalho chamada x-ms-version com um valor de 2013-11-01.

1. Envie uma solicitação no seguinte formato: https://management.core.windows.net/<subscrition-id>/services/hostedservices/<service-name>?embed-detail=true

1. Procure o elemento **HostName** para cada elemento **RoleInstance**.

>[AZURE.WARNING]Você também pode exibir o sufixo de domínio interno para o seu serviço de nuvem na resposta da chamada REST, verificando o elemento **InternalDnsSuffix** ou executando ipconfig /all em um prompt de comando em uma sessão de área de trabalho remota (Windows) ou ainda executando cat /etc/resolv.conf em um terminal SSH (Linux).

## Modificando um nome de host

Você pode modificar o nome de host para qualquer máquina virtual ou instância de função, carregando um arquivo de configuração de serviço modificado ou renomeando o computador em uma sessão de área de trabalho remota.

## Próximas etapas

[Resolução de nomes (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Esquema de configuração de serviço do Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Esquema de configuração de Rede Virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Especificar configurações de DNS usando arquivos de configuração de rede](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

<!---HONumber=August15_HO9-->