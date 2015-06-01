
<properties 
    pageTitle="Dimensionamento de informações de um VNET no RemoteApp"
    description="Saiba mais sobre os requisitos de endereço IP para o RemoteApp em execução com uma VNET" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/29/2015" 
    ms.author="elizapo" />



# Dimensionamento de informações de um VNET no RemoteApp

Quando você usa o RemoteApp com uma rede virtual (VNET), o RemoteApp usa endereços IP dentro da sub-rede. Com base na escala de seu serviço do RemoteApp, você precisa garantir que sua sub-rede tenha endereços IP suficientes disponíveis para máquinas virtuais do RemoteApp. Embora essa orientação de dimensionamento não seja perfeita, visto o modo como o RemoteApp faz a rotação dinâmica de máquinas virtuais para cima e para baixo dentro de uma coleção, ele ajudará a calcular o intervalo de sub-rede. Isso é especialmente importante já que, quando um serviço do RemoteApp é colocado em uma VNET, você não pode aumentar o tamanho da sub-rede sem remover o RemoteApp.

Para cada coleção de RemoteApp que você deseja executar em capacidade máxima você deve ter 100 endereços IP disponíveis. Por exemplo, se você tiver uma coleção do RemoteApp no plano Padrão desejar ter no máximo 500 usuários, você deve ter 100 endereços IP para essa coleção. Do mesmo modo, você precisa de 100 endereços IP para uma coleção de RemoteApp no plano Básico com 800 usuários. Se você planeja ter menos usuários (menos que o máximo), você pode reduzir os endereços IP necessários por coleção. O requisito de tamanho mínimo de sub-rede é 30 endereços IP (/27).

<!--HONumber=52-->
