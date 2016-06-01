<properties
    pageTitle="Usando aplicativos do App-V com o Azure RemoteApp| Microsoft Azure"
    description="Saiba como usar os aplicativos App-V no Azure RemoteApp."
    services="remoteapp"
	documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/12/2016" 
    ms.author="elizapo" />



# Usando os aplicativos App-V no Azure RemoteApp

Você pode usar os aplicativos do App-V em uma coleção híbrida do Azure RemoteApp, o que requer o ingresso no domínio.

Antes de começar, instale o cliente do App-V 5.1 com as atualizações mais recentes. Você precisará criar uma [imagem personalizada](remoteapp-create-custom-image.md) que inclua o cliente de App-V.

É fácil usar sua infraestrutura existente do App-V com o Azure RemoteApp. Como uma coleção híbrida é implantada em uma Rede Virtual do Azure com acesso ao controlador de domínio e as VMs são ingressadas no domínio, você poderá aproveitar a infraestrutura existente do App-V e os métodos de implantação para hospedar com facilidade o aplicativo App-V no Azure RemoteApp. Veja algumas considerações que você deve fazer com base no tipo de implantação do App-V que possui no momento:

| Opções de configuração | | Positivo | Negativo |
|-----------------------|-----------------------|------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Método de entrega | Streaming (por demanda) | O aplicativo é sempre a versão mais recente e está sempre atualizado | Latência da primeira vez |
| | Montado | Mais rápido; o aplicativo já está presente na VM | Inchaço - ocupa o espaço da imagem (limite de 127 GB) |
| Armazenamento de local de aplicativo | Conteúdo compartilhado | O aplicativo é executado na memória da instância do Azure RemoteApp | Consome memória e a conexão com o servidor de streaming (arquivo) em que o aplicativo reside |
| | Disco (em cache) | Execução rápida. O aplicativo não depende de disponibilidade da Fonte de Conteúdo | Inchaço - ocupa o espaço da imagem (limite de 127 GB) |
| Direcionamento | Usuário | Exige a infraestrutura completa do App-V autônomo | |
| | Global (máquina) | Pré-publicar ou destinar usando o servidor de Publicação | Você precisará atualizar a imagem do Azure se quiser atualizar o aplicativo (enorme). Ocupa algum espaço na imagem. |

 Depois de criar sua imagem personalizada e sua coleção híbrida, publique seu aplicativo, atribua usuários e aproveite seus aplicativos existentes do App-V hospedados no Azure RemoteApp entregues a qualquer dispositivo em qualquer lugar.

<!---HONumber=AcomDC_0518_2016-->