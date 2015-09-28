<properties
   pageTitle="Atualização de aplicativo a Malha do Serviço: tópicos avançados"
   description="Este artigo aborda alguns tópicos avançados relativos à atualização de um aplicativo Malha do Serviço."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>

# Atualização de aplicativo a Malha do Serviço: tópicos avançados

## Modo de atualização manual

> [AZURE.NOTE]O modo manual não monitorado somente deve ser considerado em caso de atualização com falha ou suspensa. O modo monitorado é o modo de atualização recomendado para aplicativos da Malha do Serviço.

O Service Fabric fornece vários modos de atualização para dar suporte a cluster de desenvolvimento e de produção. Cada uma das opções de implantação são ideais para ambientes diferentes. A atualização de aplicativo monitorado sem interrupção é a atualização mais comum a ser usada na produção. Quando a política de atualização é especificada, a Malha do Serviço confirma a integridade do aplicativo antes que a atualização prossiga. Em determinadas situações, onde há mais personalização ou a necessidade de uma política de avaliação de integridade mais complexa, ou uma atualização não convencional (o aplicativo já está com perda de dados, etc.), o administrador do aplicativo pode usar o modo de atualização de aplicativo manual sem interrupção para ter controle total sobre o andamento da atualização através de vários domínios de atualização. Finalmente, a atualização do aplicativo automatizada sem interrupção é útil para desenvolver ou testar o ambiente para fornecer um ciclo rápido de iteração durante o desenvolvimento do serviço.

**Manual**: para a atualização de aplicativo na UD atual e altera o modo de atualização para manual não monitorado. O administrador precisa chamar manualmente **MoveNextApplicationUpgradeDomainAsync** para continuar com a atualização ou disparar uma reversão, iniciando uma nova atualização. Depois que a atualização entra no modo manual, ela permanece no modo manual até que uma nova atualização seja iniciada. O comando **GetApplicationUpgradeProgressAsync** retorna FABRIC\_APPLICATION\_UPGRADE\_STATE\_ROLLING\_FORWARD\_PENDING.

## Atualizando um pacote Diff

Um aplicativo Malha do Serviço pode ser atualizado pelo provisionamento com um pacote de aplicativo completo e independente. Também é possível atualizar um aplicativo usando um pacote diff que contém somente os arquivos de aplicativo atualizados e arquivos de manifesto do serviço e de manifesto do aplicativo atualizados.

Um pacote de aplicativo completo contém todos os arquivos necessários para iniciar e executar um aplicativo da Malha do Serviço. Um pacote Diff contém somente os arquivos alterados entre o último provisionamento e a atualização atual, além de arquivos completos de manifesto do aplicativo e do serviço. Qualquer referência no manifesto do aplicativo ou no manifesto de serviço que a Malha do Serviço não pode localizar no layout de compilação será pesquisada na ImageStore (link a ser definido).

Pacotes de aplicativo completos são necessários para a primeira instalação de um aplicativo no cluster. Atualizações subsequentes podem ser um pacote de aplicativo completo ou um pacote diff.

Hipóteses em que usar um pacote diff seria uma boa opção:

* Um pacote diff é preferível quando você tem um pacote de aplicativo grande que faz referência a vários arquivos de manifesto do serviço e/ou vários pacotes de código, de configuração ou de dados.

* Um pacote diff é preferível quando você tiver um sistema de implantação que gera o layout de compilação diretamente do seu processo de compilação do aplicativo. Nesse caso, embora nada no código tenha mudado, assemblies recém-criados terão uma soma de verificação diferente. O uso de um pacote de aplicativo completo exige que você atualize a versão de todos os pacotes de código. Com o uso de um pacote diff, você fornece somente os arquivos alterados e os arquivos de manifesto em que a versão foi alterada.

## Próximas etapas

[Tutorial de atualização](service-fabric-application-upgrade-tutorial.md)

[Parâmetros de atualização](service-fabric-application-upgrade-parameters.md)

[Serialização de dados](service-fabric-application-upgrade-data-serialization.md)

[Solucionar problemas de atualização de aplicativo](service-fabric-application-upgrade-troubleshooting.md)
 

<!---HONumber=Sept15_HO3-->