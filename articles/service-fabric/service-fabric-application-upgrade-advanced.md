<properties
   pageTitle="Atualização de aplicativos: tópicos avançados | Microsoft Azure"
   description="Este artigo aborda alguns tópicos avançados relativos à atualização de um aplicativo Malha do Serviço."
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
   ms.date="05/13/2016"
   ms.author="subramar"/>

# Atualização de aplicativos do Service Fabric: tópicos avançados

## Adicionando ou removendo serviços durante uma atualização de aplicativo

Se um novo serviço for adicionado a um aplicativo que já está implantado e for publicado como uma atualização, o serviço será adicionado ao aplicativo implantado (sem que a atualização afete os serviços que já faziam parte do aplicativo). No entanto, uma instância do serviço que foi adicionado terá que ser iniciada para que o novo serviço seja ativado (usando o cmdlet `New-ServiceFabricService`).

Serviços também podem ser removidos de um aplicativo como parte de uma atualização. No entanto, é necessário garantir que todas as instâncias atuais do serviço (que será removido como parte da atualização) sejam interrompidas antes de prosseguir com a atualização (usando o cmdlet `Remove-ServiceFabricService`).

## Modo de atualização manual

> [AZURE.NOTE]  O modo manual não monitorado só deverá ser considerado em caso de atualização com falha ou suspensa. O modo monitorado é o modo de atualização recomendado para aplicativos do Service Fabric.

O Service Fabric do Azure fornece vários modos de atualização para dar suporte a clusters de desenvolvimento e de produção. Cada uma das opções de implantação são ideais para ambientes diferentes.

A atualização de aplicativo monitorado sem interrupção é a atualização mais comum a ser usada em produção. Quando a política de atualização é especificada, a Malha do Serviço confirma a integridade do aplicativo antes que a atualização prossiga.

 O administrador do aplicativo pode usar o modo de atualização de aplicativo sem interrupção manual para ter controle total sobre o progresso da atualização usando vários domínios de atualização. Esse modo é útil para determinadas situações em que uma política de avaliação de integridade mais personalizada ou complexa é necessária, ou quando há uma atualização não convencional (por exemplo, o aplicativo já apresenta perda de dados).

Finalmente, a atualização do aplicativo automatizada sem interrupção é útil para desenvolver ou testar o ambiente para fornecer um ciclo rápido de iteração durante o desenvolvimento do serviço.

## Alterar para o modo de atualização manual
**Manual**: para a atualização do aplicativo no UD atual e altera o modo de atualização para Manual Não Monitorado. O administrador precisa chamar manualmente **MoveNextApplicationUpgradeDomainAsync** para continuar com a atualização ou disparar uma reversão iniciando uma nova atualização. Depois que a atualização entra no modo manual, ela permanece no modo manual até que uma nova atualização seja iniciada. O comando **GetApplicationUpgradeProgressAsync** retorna FABRIC\_APPLICATION\_UPGRADE\_STATE\_ROLLING\_FORWARD\_PENDING.

## Atualizar usando um pacote diff

Um aplicativo Malha do Serviço pode ser atualizado pelo provisionamento com um pacote de aplicativo completo e independente. Também é possível atualizar um aplicativo usando um pacote diff que contém somente os arquivos de aplicativo atualizados, os arquivos de manifesto do serviço e os de manifesto do aplicativo atualizados.

Um pacote de aplicativo completo contém todos os arquivos necessários para iniciar e executar um aplicativo da Malha do Serviço. Um pacote diff contém somente os arquivos alterados entre o último provisionamento e a atualização atual, além de arquivos completos de manifesto do aplicativo e do serviço. Qualquer referência no manifesto do aplicativo ou no manifesto de serviço que o Service Fabric não conseguir localizar no layout de compilação será pesquisada no repositório de imagens.

São necessários pacotes de aplicativo completos para a primeira instalação de um aplicativo no cluster. Atualizações subsequentes podem ser um pacote de aplicativo completo ou um pacote diff.

Hipóteses em que usar um pacote diff seria uma boa opção:

* Será melhor usar um pacote diff quando você tiver um pacote de aplicativo grande que faça referência a vários arquivos de manifesto do serviço e/ou a vários pacotes de código, de configuração ou de dados.

* Será melhor usar um pacote diff quando você tiver um sistema de implantação que gere o layout de compilação diretamente do seu processo de compilação do aplicativo. Nesse caso, embora nada no código tenha mudado, assemblies recém-criados terão uma soma de verificação diferente. O uso de um pacote de aplicativo completo exige que você atualize a versão de todos os pacotes de código. Com o uso de um pacote diff, você fornece somente os arquivos alterados e os arquivos de manifesto em que a versão foi alterada.

Quando um aplicativo é atualizado usando o Visual Studio, o pacote diff é publicado automaticamente. Se quiser criar um pacote diff manualmente (por exemplo, para atualizar usando o PowerShell), você deverá atualizar o aplicativo e os manifestos de serviço, mas incluir apenas os pacotes que foram alterados no pacote de aplicativos final.

Por exemplo, vamos começar com o aplicativo a seguir (números de versão fornecidos para facilitar a compreensão):

```text
app1       	1.0.0
  service1 	1.0.0
    code   	1.0.0
    config 	1.0.0
  service2 	1.0.0
    code   	1.0.0
    config 	1.0.0
```

Agora, vamos supor que você quisesse atualizar apenas o pacote de códigos de service1 usando um pacote diff usando o PowerShell. Agora, seu aplicativo atualizado será semelhante ao seguinte:

```text
app1       	2.0.0      <-- new version
  service1 	2.0.0      <-- new version
    code   	2.0.0      <-- new version
    config 	1.0.0
  service2 	1.0.0
    code   	1.0.0
    config 	1.0.0
```

Nesse caso, você atualiza o manifesto do aplicativo para 2.0.0 e o manifesto do serviço para service1 para refletir a atualização do pacote de códigos. A estrutura de pastas para o pacote de aplicativos seria semelhante à seguinte:

```text
app1/
  service1/
    code/
```

## Próximas etapas

[Atualizar seu Aplicativo Usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você a fazer uma atualização de aplicativo usando o Visual Studio.

[Atualizando seu aplicativo usando o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você uma atualização de aplicativo usando o PowerShell.

Controle como seu aplicativo é atualizado usando [Parâmetros de Atualização](service-fabric-application-upgrade-parameters.md).

Torne suas atualizações de aplicativo compatíveis aprendendo a usar a [Serialização de Dados](service-fabric-application-upgrade-data-serialization.md).

Corrija problemas comuns em atualizações de aplicativo consultando as etapas em [Solucionando Problemas de Atualizações de Aplicativo](service-fabric-application-upgrade-troubleshooting.md).
 

<!---HONumber=AcomDC_0518_2016-->