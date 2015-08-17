<properties 
   pageTitle="Especificando as configurações de DNS em um arquivo de configuração de serviço"
   description="Descrição"
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
   ms.date="05/28/2015"
   ms.author="joaoma" />

# Especificando as configurações de DNS em um arquivo de configuração de serviço

## Elementos DNS

Um arquivo de configuração de serviço pode conter um elemento DnsServers com uma lista de endereços IPv4 para os servidores do Sistema de Nome de Domínio (DNS) que o serviço usará. As configurações no arquivo de configuração de serviço têm precedência sobre as configurações no arquivo de configuração de rede. Para obter mais informações, consulte [Esquema de configuração de serviço do Azure (arquivo .cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Elemento NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

>[AZURE.WARNING]O atributo **nome** no elemento **DnsServer** é usado apenas como um nome de referência. Ele não representa o nome do host para o servidor DNS. Cada valor do atributo **DnsServer** deve ser exclusivo em toda a assinatura do Microsoft Azure.

## Consulte também

[Esquema de configuração de serviço do Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Esquema de configuração de Rede Virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Configurar uma Rede Virtual usando arquivos de configuração de rede](http://go.microsoft.com/fwlink/?LinkId=248094)

[Sobre as configurações de rede virtual no Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkId=248092)

<!---HONumber=August15_HO6-->