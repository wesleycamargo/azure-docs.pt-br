<properties
   pageTitle="Atualização de aplicativos do Service Fabric | Microsoft Azure"
   description="Este artigo fornece uma introdução à atualização de um aplicativo do Service Fabric, incluindo a escolha de modos de atualização e as verificações de integridade executadas."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>


# Atualização de aplicativos do Service Fabric

Um aplicativo Malha do Serviço é uma coleção de serviços. Durante uma atualização, a Malha do Serviço compara o novo [manifesto do aplicativo](service-fabric-application-model.md#describe-an-application) com a versão anterior e determina quais serviços as atualizações do aplicativo exigem. Isso é determinado comparando os números de versão nos manifestos do serviço com a versão anterior. Se um serviço não foi alterado, ele não foi atualizado.

## Visão geral das atualizações sem interrupção

Em uma atualização do aplicativo sem interrupção, a atualização é executada em estágios. Em cada estágio, a atualização é aplicada a um subconjunto de nós no cluster, chamado de domínio de atualização. Como resultado, o aplicativo permanece disponível durante a atualização. Durante a atualização, o cluster pode conter uma combinação de versões antigas e novas. Por esse motivo, as duas versões devem ser compatíveis uma com a outra. Se não forem compatíveis, o administrador do aplicativo é responsável por realizar uma atualização de várias fases para manter a disponibilidade. Isso é feito ao fazer uma atualização com uma versão intermediária do aplicativo que é compatível com a versão anterior antes de atualizar para a versão final.

Os domínios de atualização ficam especificados no manifesto do cluster quando este é configurado. Você não deve supor que os domínios de atualização receberão atualizações em uma ordem específica. Um domínio de atualização é uma unidade lógica de implantação para um aplicativo. Os domínios de atualização permitem que os serviços permaneçam com alta disponibilidade durante uma atualização.

Atualizações com interrupção são possíveis se a atualização for aplicada a todos os nós no cluster, que é o caso quando o aplicativo tem somente um domínio de atualização. Isso não é recomendável porque o serviço ficaria paralisado e não estaria disponível no momento da atualização. Além disso, o Azure não fornece qualquer garantia quando um cluster é configurado com apenas um domínio de atualização.

## Verificações de integridade durante atualizações

Para uma atualização, as políticas de integridade precisam ser configuradas (ou valores padrão podem ser usados). Uma atualização é considerada bem sucedida quando todos os domínios de atualização são atualizados dentro do tempo limite especificado e todos os domínios de atualização são considerados saudáveis. Um domínio de atualização íntegro significa ele passa por todas as verificações de integridade especificadas na política de integridade, por exemplo, uma política de integridade pode garantir que todos os serviços em uma instância do aplicativo devam ser <em>íntegros</em>, já que a integridade é definida pela Malha do Serviço.

As políticas e verificações de integridade durante a atualização feita pelo Service Fabric são independentes do serviço e do aplicativo. Ou seja, nenhum teste específico de serviço é verificado. Por exemplo, o serviço tem um requisito de taxa de transferência mínima. No entanto, a Malha do Serviço não tem as informações para testar isso; portanto, não verificará a taxa de transferência conforme definida para o seu aplicativo. Confira os [artigos de integridade](service-fabric-health-introduction.md) para saber as verificações que são executadas; as verificações durante a atualização incluem se o pacote de aplicativo foi copiado corretamente, se a instância foi iniciada e assim por diante.

A integridade do aplicativo é uma agregação das entidades filho do aplicativo. Em resumo, a Malha do Serviço avalia a integridade do aplicativo por meio da integridade relatada no aplicativo, bem como todas as integridades dos serviços para o aplicativo. A integridade dos serviços de aplicativo é também avaliada agregando a integridade de seus filhos, como a réplica do serviço. Depois que a política de integridade do aplicativo for atendida, a atualização pode continuar ou, se a política de integridade for violada, há falha na atualização do aplicativo.

## Modos de atualização

O modo comum (e recomendável) para atualização é o monitorado. Monitorado executa a atualização em um domínio de atualização e se todas as verificações de integridade forem aprovadas (pela política especificada), ele passa para o próximo domínio de atualização automaticamente e assim por diante. Se as verificações de integridade falharem e/ou tempos limite forem atingidos, a atualização será revertida para o domínio de atualização ou o modo será alterado para UnmonitoredManual se essa for a opção selecionada no momento da atualização.

UnmonitoredManual precisaria de intervenção manual após cada atualização em um domínio de atualização para disparar a atualização no domínio de atualização seguinte. Não há nenhuma verificação de integridade da Malha do Serviço que seja executada e dependa de interventor para executar as verificações de integridade ou de status antes de iniciar a atualização no domínio de atualização seguinte.

## Fluxograma de atualização de aplicativo

O fluxograma abaixo ajuda na compreensão do processo de atualização de um aplicativo Malha do Serviço. Mais especificamente, o fluxo descreve como os tempos limite, incluindo *HealthCheckStableDuration*, *HealthCheckRetryTimeout* e *UpgradeHealthCheckInterval*, ajudam a controlar quando a atualização em um domínio de atualização é considerada um êxito ou falha.

![O processo de atualização de um aplicativo a Malha do Serviço][image]


## Próximas etapas

[Tutorial de atualização](service-fabric-application-upgrade-tutorial.md)

[Parâmetros de atualização](service-fabric-application-upgrade-parameters.md)

[Serialização de dados](service-fabric-application-upgrade-data-serialization.md)

[Atualização Manual e Atualização com Pacotes Diff](service-fabric-application-upgrade-advanced.md)

[Solucionar problemas de atualização de aplicativo ](service-fabric-application-upgrade-troubleshooting.md)



[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
 

<!---HONumber=AcomDC_1125_2015-->