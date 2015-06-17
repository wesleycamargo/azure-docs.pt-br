<properties
   pageTitle="Desabilitar ou habilitar um ponto de extremidade do Gerenciador de Tráfego"
   description="Este artigo ajudará a desabilitar ou habilitar os pontos de extremidade de perfil do Gerenciador de Tráfego."
   services="traffic-manager"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2015"
   ms.author="cherylmc" />

# Desabilitar ou habilitar um ponto de extremidade do Gerenciador de Tráfego

Você também pode desabilitar pontos de extremidade individuais que fazem parte de um perfil do Gerenciador de Tráfego. Os pontos de extremidade incluem serviços de nuvem e sites. A desabilitação de um ponto de extremidade o mantém como parte do perfil, mas o perfil age como se o ponto de extremidade não estivesse incluído nele. Essa ação é muito útil para remover temporariamente um ponto de extremidade que esteja no modo de manutenção ou sendo reimplantado. Depois que o ponto de extremidade estiver funcionando novamente, ele poderá ser habilitado

[AZURE.NOTE] **A desabilitação de um ponto de extremidade não tem nada a ver com seu estado de implantação no Azure. Um ponto de extremidade íntegro permanecerá ativo e capaz de receber tráfego mesmo quando desabilitado no Gerenciador de Tráfego. Além disso, a desabilitação de um ponto de extremidade em um perfil não afeta seu status em outro perfil.**

## Para desabilitar um ponto de extremidade

1. No painel do Gerenciador de Tráfego no Portal de Gerenciamento, localize o perfil do Gerenciador de Tráfego que contém as configurações do ponto de extremidade que você deseja modificar e, em seguida, clique na seta à direita do nome do perfil. Isso abrirá a página de configurações do perfil.
1. Na parte superior da página, clique em **Pontos de extremidade** para exibir os pontos de extremidade incluídos em sua configuração. 
1. Clique no ponto de extremidade que você deseja desabilitar e, em seguida, clique em **Desabilitar** na parte inferior da página.
1. O tráfego deixará de fluir para o ponto de extremidade com base no TTL (Vida Útil) DNS configurado para o nome de domínio do Gerenciador de Tráfego. Você pode alterar a vida útil na página Configuração do perfil do Gerenciador de Tráfego.

## Para habilitar um ponto de extremidade


1. No painel do Gerenciador de Tráfego no Portal de gerenciamento, localize o perfil do Gerenciador de Tráfego que contém as configurações do ponto de extremidade que você deseja modificar e, em seguida, clique na seta à direita do nome do perfil. Isso abrirá a página de configurações do perfil.
1. Na parte superior da página, clique em **Pontos de extremidade** para exibir os pontos de extremidade incluídos em sua configuração.
1. Clique no ponto de extremidade que você deseja habilitar e, em seguida, clique em **Habilitar** na parte inferior da página.
1. O tráfego começará a fluir para o serviço novamente, conforme orientado pelo perfil.

## Consulte também

[Tarefas de configuração do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Visão geral do Gerenciador de Tráfego](../traffic-manager.md)

[Serviços de Nuvem](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327)


[Operações no Gerenciador de Tráfego (referência de API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

<!--HONumber=49--> 