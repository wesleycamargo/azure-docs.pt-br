<properties
   pageTitle="ACS de Pools de Agente Público e Privado | Microsoft Azure"
   description="Como os pools de agente público e privado funcionam com um cluster do Serviço de Contêiner do Azure."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contêineres, Microsserviços, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="adegeo"/>

# Pools de agente

O Serviço de Contêiner do Azure divide agentes em vários pools. E, enquanto cada máquina virtual em um pool é idêntica, cada pool tem sua própria sub-rede. Portanto, cada pool pode ser marcado como público ou privado, afetando a acessibilidade entre computadores no seu serviço de contêiner.

### Agentes privados

Nós de agentes privados são executados por meio de uma rede não roteável. Esta rede só é acessível por meio da zona de administrador ou por meio do roteador de borda de zona pública. Por padrão, o DC/SO inicia aplicativos em nós de agente privada. Consulte a [documentação do DC/SO](https://dcos.io/docs/1.7/administration/securing-your-cluster/) para obter mais informações sobre segurança de rede.

### Agentes públicos

Nós de agente público executam aplicativos e serviços DC/OS por meio de uma rede acessível publicamente. Consulte a [documentação do DC/SO](https://dcos.io/docs/1.7/administration/securing-your-cluster/) para obter mais informações sobre segurança de rede.

## Usando pools de agente

Por padrão, **Maratona** implanta qualquer novo aplicativo a nós de agente *particular*. Você deve explicitamente implantar o aplicativo para o nó *público*. Infelizmente, a implantação para o nó público não é tão direta quanto a marcação de uma caixa. Durante a criação do novo aplicativo, acesse a guia **Opcional** e insira **slave\_public** em **Funções de Recurso Aceitas**. Esse processo está documentado [aqui](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) e na documentação do [DC\\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/).

## Próximas etapas

Leia mais informações sobre [como gerenciar seus contêineres de DC/OS](container-service-mesos-marathon-ui.md).

<!---HONumber=AcomDC_0824_2016-->