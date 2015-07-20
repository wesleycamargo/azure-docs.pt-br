
<properties
   pageTitle="Introdução ao balanceador de carga para a Internet | Microsoft Azure"
   description="Obtenha seu primeiro conjunto de balanceador de carga para a Internet para as suas Máquinas Virtuais ou Serviços de Nuvem."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma" />

# Introdução à configuração do balanceador de carga para a Internet

Os serviços de balanceamento de carga no Microsoft Azure funcionam com todos os tipos de locatário (IaaS ou PaaS) e todas os tipos de sistema operacional (Windows ou qualquer sistema operacional baseado em Linux com suporte).


## Configurando um balanceador de carga para a Internet para máquinas virtuais

Para balancear carga de tráfego de rede da Internet entre máquinas virtuais de um serviço de nuvem, você deve criar um conjunto de balanceamento de carga. Esse procedimento pressupõe que você já tenha criado as máquinas virtuais e que elas estejam todas no mesmo serviço de nuvem.

Siga as etapas abaixo para configurar um balanceador de carga definido para máquinas virtuais:

1. No Portal de Gerenciamento do Azure, clique em Máquinas Virtuais, clique no nome de uma máquina virtual no balanceamento de carga definido.
2.	Clique em Pontos de Extremidade e depois em Adicionar.

4.	Na página Adicionar um ponto de extremidade a uma máquina virtual, clique na seta para a direita.

4.	Na página Especificar os detalhes do ponto de extremidade:
	- Em Nome, digite um nome para o ponto de extremidade ou selecione da lista de pontos de extremidade predefinidos para protocolos comuns.
	-  Em Protocolo, selecione o protocolo necessário para o tipo de ponto de extremidade, TCP ou UDP, conforme necessário.
 	-  Em Porta pública e Porta Privada, digite os números de porta que você deseja que a máquina virtual use, conforme necessário. Você pode usar as regras de firewall e porta privada na máquina virtual para redirecionar o tráfego de uma maneira que seja apropriado ao seu aplicativo. A porta privada pode ser a mesma que a porta pública. Por exemplo, para um ponto de extremidade para o tráfego da web (HTTP), você pode atribuir a porta 80 à porta pública e privada.

5.	Selecione Criar um conjunto de balanceamento de carga e depois clique na seta para a direita.

6.	Na página Configurar o conjunto de balanceamento de carga, digite um nome para o conjunto e atribua os valores do comportamento de investigação do Balanceador de Carga do Azure. O Balanceador de Carga usa testes para determinar se as máquinas virtuais do conjunto de balanceamento de carga estão disponíveis para receber o tráfego de entrada.

7.	Clique na marca de seleção para criar o ponto de extremidade de balanceamento de carga. Você verá Sim na coluna Nome do conjunto de balanceamento de carga da página Pontos de extremidade da máquina virtual.

8.	No Portal de Gerenciamento, clique em Máquinas Virtuais, clique no nome de uma máquina virtual adicional no conjunto de balanceamento de carga, clique em Pontos de Extremidade e em Adicionar.

9.	Na página Adicionar um ponto de extremidade a uma máquina virtual, clique em Adicionar um ponto de extremidade a um conjunto existente de balanceamento de carga, selecione o nome do conjunto de balanceamento de carga e, em seguida, clique na seta para a direita.

10.	Na página Especificar os detalhes do ponto de extremidade, digite um nome para o ponto de extremidade e, em seguida, clique na marca de seleção. Para as outras máquinas virtuais no conjunto de balanceamento de carga, repita as etapas de 8 a 10.

Você também pode configurar pontos de extremidade com os cmdlets do Windows PowerShell a seguir:

- Add-AzureEndpoint

[Add-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495300)

- Get-AzureEndpoint

[Get-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495158)

- Remove-AzureEndpoint

[Remove-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495161)


## Configurando um balanceador para a Internet para serviços de nuvem


Serviços de nuvem são automaticamente configurados com um Balanceador de Carga e podem ser personalizados por meio do modelo de serviço

Você pode aproveitar o SDK do Azure para .NET 2.5 para atualizar seu serviço de nuvem. As configurações de ponto de extremidade para Serviços de Nuvem são feitas no [definição de serviço](https://msdn.microsoft.com/library/azure/gg557553.aspx).csdef.

O exemplo a seguir mostra como um arquivo servicedefinition.csdef é configurado para uma implantação na nuvem:

Ao verificar o trecho de código do arquivo. csdef gerado por uma implantação na nuvem, você pode ver o ponto de extremidade externo configurado para usar portas HTTP na porta 10000, 10001 e 10002.


	<ServiceDefinition name=“Tenant“>
   	<WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
  	</WorkerRole>
	</ServiceDefinition>




### Verificando status de integridade do balanceador de carga para serviços de nuvem


A seguir, um exemplo de como criar uma investigação:

	 	<LoadBalancerProbes>
    	<LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
 	 	</LoadBalancerProbes>

O balanceador de carga combina as informações do ponto de extremidade e as informações de investigação para criar uma URL na forma de http://{DIP VM}:80/Probe.aspx, que será usada para consultar a integridade do serviço.

O serviço notará que o mesmo IP o está acessando periodicamente. Isso é a solicitação de investigação de integridade proveniente do host do nó em que a VM está sendo executada. O serviço deve responder com um código de status HTTP 200 para que o balanceador de carga presuma que o serviço esteja íntegro. Qualquer outro código de status HTTP (por exemplo, 503) tira diretamente a VM da rotação.

A definição da investigação também controla a frequência da investigação. No caso acima, o balanceador de carga está investigando o ponto de extremidade a cada 5 segundos. Se nenhuma resposta positiva for recebida em 10 segundos (dois intervalos de investigação), a investigação será considerada inativa e a VM sairá da rotação. Da mesma forma, se o serviço estiver fora de rotação e uma resposta positiva for recebida, o serviço será colocado de volta à rotação imediatamente. Se o serviço estiver flutuando entre Íntegro/não íntegro, o balanceador de carga pode optar por atrasar a reintrodução do serviço na rotação até que ele esteja íntegro para um número de investigações.

Verifique o esquema de definição de serviço para a [investigação de integridade](https://msdn.microsoft.com/library/azure/jj151530.aspx) para obter mais informações.

## Próximas etapas

[Introdução à configuração de um balanceador de carga interno](load-balancer-internal-getstarted.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=July15_HO2-->