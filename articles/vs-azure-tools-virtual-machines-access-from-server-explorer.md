<properties 
   pageTitle="Acessando máquinas virtuais do Azure por meio do Gerenciador de Servidores"
	description="Obtenha uma visão geral de como exibir e gerenciar máquinas virtuais (VMs) do Azure no Gerenciador de Servidores no Visual Studio."
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tglee"/>
<tags 
   ms.service="multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="multiple"
	ms.date="08/13/2015"
	ms.author="kempb"/>

# Acessando máquinas virtuais do Azure por meio do Gerenciador de Servidores

Usando o Gerenciador de Servidores no Visual Studio, você pode exibir informações sobre as máquinas virtuais hospedadas pelo Azure.

## Acessando máquinas virtuais no Gerenciador de Servidores

Se tiver máquinas virtuais hospedadas pelo Azure, você pode acessá-las no Gerenciador de Servidores. Você primeiro deve entrar sua assinatura do Azure para exibir os serviços móveis. Por exemplo, no Gerenciador de Servidores, você pode abrir o menu de atalho do nó do Azure e escolher **Conectar ao Microsoft Azure**.

### Para obter informações sobre suas máquinas virtuais

1. No Gerenciador de Servidores, escolha uma máquina virtual e pressione a tecla F4 para mostrar a janela de propriedades.
 
    A tabela a seguir mostra quais propriedades estão disponíveis, mas elas são somente leitura. Para alterá-las, use o portal de gerenciamento.

	|Propriedade|Descrição|
	|---|---|
	|Nome DNS|A URL com o endereço de Internet da máquina virtual.|
	|Ambiente|Para uma máquina virtual, o valor dessa propriedade é sempre produção.|
	|Nome|O nome da máquina virtual.|
	|Tamanho|O tamanho da máquina virtual, que reflete a quantidade de memória e espaço em disco disponível. Para obter mais informações, consulte Como configurar os tamanhos de uma máquina virtual.|
	|Status|Os valores incluem Inicial, Iniciado, Parando, Parado e Recuperando Status. Se Recuperando Status aparecer, o status atual é desconhecido. Os valores para essa propriedade diferem dos valores que são usados no portal de gerenciamento.|
	|SubscriptionID|A ID de assinatura para sua conta do Azure. Você pode mostrar essas informações no portal de gerenciamento ao exibir as propriedades de uma assinatura.|
	
1. Escolha um nó do ponto de extremidade e exiba a janela **Propriedades**.

1. A tabela a seguir descreve as propriedades de pontos de extremidade disponíveis, mas eles são somente leitura. Para adicionar ou editar os pontos de extremidade para uma máquina virtual, use o portal de gerenciamento.

	|Propriedade|Descrição|
	|---|---|
	|Nome|Um identificador para o ponto de extremidade.|
	|Porta privada|A porta para acesso à rede interna para o seu aplicativo.|
	|Protocolo|O protocolo de camada de transporte que este ponto de extremidade usa: TCP ou UDP.|
	|Porta pública|A porta usada para acesso público ao seu aplicativo.|

## Próximas etapas

[Usando a Área de Trabalho Remota com as funções do Azure](http://go.microsoft.com/fwlink/p/?LinkID=623091)

<!---HONumber=August15_HO9-->