---
title: Desabilitar ou habilitar um ponto de extremidade do Gerenciador de Tráfego| Microsoft Docs
description: Este artigo ajudará a desabilitar ou habilitar os pontos de extremidade de perfil do Gerenciador de Tráfego.
services: traffic-manager
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: tysonn

ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2016
ms.author: sewhee

---
# Desabilitar ou habilitar um ponto de extremidade do Gerenciador de Tráfego
Você também pode desabilitar pontos de extremidade individuais que fazem parte de um perfil do Gerenciador de Tráfego. Os pontos de extremidade incluem serviços de nuvem e sites. A desabilitação de um ponto de extremidade o mantém como parte do perfil, mas o perfil age como se o ponto de extremidade não estivesse incluído nele. Essa ação é muito útil para remover temporariamente um ponto de extremidade que esteja no modo de manutenção ou sendo reimplantado. Depois que o ponto de extremidade estiver funcionando novamente, ele poderá ser habilitado

> [!NOTE]
> **A desabilitação de um ponto de extremidade não tem nada a ver com seu estado de implantação no Azure. Um ponto de extremidade íntegro permanecerá ativo e capaz de receber tráfego mesmo quando desabilitado no Gerenciador de Tráfego. Além disso, a desabilitação de um ponto de extremidade em um perfil não afeta seu status em outro perfil.**
> 
> 

## Para desabilitar um ponto de extremidade
1. No painel do Gerenciador de Tráfego no portal clássico do Azure, localize o perfil do Gerenciador de Tráfego que contém as configurações do ponto de extremidade que você deseja modificar e, em seguida, clique na seta à direita do nome do perfil. Isso abrirá a página de configurações do perfil.
2. Na parte superior da página, clique em **Pontos de Extremidade** para exibir os pontos de extremidade incluídos em sua configuração.
3. Clique no ponto de extremidade que você deseja desabilitar e, em seguida, clique em **Desabilitar** na parte inferior da página.
4. O tráfego deixará de fluir para o ponto de extremidade com base no TTL (Vida Útil) DNS configurado para o nome de domínio do Gerenciador de Tráfego. Você pode alterar a vida útil na página Configuração do perfil do Gerenciador de Tráfego.

## Para habilitar um ponto de extremidade
1. No painel do Gerenciador de Tráfego no portal clássico do Azure, localize o perfil do Gerenciador de Tráfego que contém as configurações do ponto de extremidade que você deseja modificar e, em seguida, clique na seta à direita do nome do perfil. Isso abrirá a página de configurações do perfil.
2. Na parte superior da página, clique em **Pontos de Extremidade** para exibir os pontos de extremidade incluídos em sua configuração.
3. Clique no ponto de extremidade que você deseja habilitar e, em seguida, clique em **Habilitar** na parte inferior da página.
4. O tráfego começará a fluir para o serviço novamente, conforme orientado pelo perfil.

## Próximas etapas
[Gerenciador de Tráfego - Desabilitar, habilitar ou excluir um perfil](disable-enable-or-delete-a-profile.md)

[Solucionando problemas de estado degradado do Gerenciador de Tráfego](traffic-manager-troubleshooting-degraded.md)

[Considerações sobre desempenho do Gerenciador de Tráfego](traffic-manager-performance-considerations.md)

<!---HONumber=AcomDC_0824_2016-->