<properties
   pageTitle="ACS de pools de agentes do DC/OS públicos e privados | Microsoft Azure"
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

# Pools de Agentes DC/OS para o Serviço de Contêiner do Azure

O Serviço de Contêiner do Azure DC/OS divide agentes em pools públicos ou privados. Uma implantação pode ser feita para um pool, afetando a acessibilidade entre computadores no seu serviço de contêiner. Os computadores podem ser expostos à Internet (público) ou mantidos internamente (privado). Este artigo fornece uma visão geral sobre o motivo da existência de um pool público e privado.

### Agentes privados

Nós de agentes privados são executados por meio de uma rede não roteável. Esta rede só é acessível por meio da zona de administrador ou por meio do roteador de borda de zona pública. Por padrão, o DC/SO inicia aplicativos em nós de agente privada. Consulte a [documentação do DC/SO](https://dcos.io/docs/1.7/administration/securing-your-cluster/) para obter mais informações sobre segurança de rede.

### Agentes públicos

Nós de agente público executam aplicativos e serviços DC/OS por meio de uma rede acessível publicamente. Consulte a [documentação do DC/SO](https://dcos.io/docs/1.7/administration/securing-your-cluster/) para obter mais informações sobre segurança de rede.

## Usando pools de agente

Por padrão, **Maratona** implanta qualquer novo aplicativo a nós de agente *particular*. Você deve implantar explicitamente o aplicativo para o nó *público* durante a criação do aplicativo. Selecione a guia **Opcional** e insira **slave\_public** para o valor **Funções de Recurso Aceitas**. Esse processo está documentado [aqui](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) e na documentação do [DC\\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/).

## Próximas etapas

Leia mais informações sobre [como gerenciar seus contêineres de DC/OS](container-service-mesos-marathon-ui.md).

Saiba como [abrir o firewall](container-service-enable-public-access.md) fornecido pelo Azure para permitir acesso público para o contêiner do seu DC/OS.

<!---HONumber=AcomDC_0907_2016-->