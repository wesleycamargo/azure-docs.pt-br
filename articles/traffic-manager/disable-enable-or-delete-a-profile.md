<properties
   pageTitle="Desabilitar, habilitar ou excluir um perfil do Gerenciador de Tráfego"
   description="Este artigo o ajudará a trabalhar com os perfis do Gerenciador de Tráfego."
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma;cherylmc" />

# Desabilitar, habilitar ou excluir um perfil

## Desabilitar ou habilitar um perfil

Você pode desabilitar um perfil existente do Gerenciador de Tráfego para que ele não referencie solicitações de usuários para seus pontos de extremidade configurados. Quando você desabilita um perfil do Gerenciador de Tráfego, o próprio perfil e as informações contidas nele permanecem intactos e podem ser editados na interface do Gerenciador de Tráfego. Quando quiser reabilitar o perfil, você poderá fazer isso facilmente no Portal de Gerenciamento, e as referências serão retomadas. Quando você cria um perfil do Gerenciador de Tráfego no Portal de Gerenciamento, ele é automaticamente habilitado.

### Para desabilitar um perfil

1. Modifique o registro de recurso DNS no servidor DNS da Internet para usar o tipo de registro e ponteiro apropriados para outro nome ou o endereço IP de um local específico na Internet. Em outras palavras, altere o registro de recurso DNS no servidor DNS da Internet para que ele não use um registro de recurso CNAME que aponte para o nome de domínio de seu perfil do Gerenciador de Tráfego.
1. O tráfego deixará de ser direcionado para os pontos de extremidade por meio das configurações de perfil do Gerenciador de Tráfego.
1. Selecione o perfil que você deseja desabilitar. Para selecionar o perfil, na página do Gerenciador de Tráfego, realce o perfil clicando na coluna ao lado do nome do perfil. Não clique no nome do perfil ou na seta ao lado do nome, pois você será levado para a página de configurações do perfil.
1. Depois de selecionar o perfil, clique em Desabilitar na parte inferior da página.

### Para habilitar um perfil

1. Selecione o perfil que você deseja habilitar. Para selecionar o perfil, na página do Gerenciador de Tráfego, realce o perfil clicando na coluna ao lado do nome do perfil. Não clique no nome do perfil ou na seta ao lado do nome, pois você será levado para a página de configurações do perfil.
1. Depois de selecionar o perfil, clique em Habilitar na parte inferior da página.
1. Modifique o registro de recurso DNS no servidor DNS da Internet para usar o tipo de registro CNAME, que mapeia o nome de domínio da empresa para o nome de domínio de seu perfil do Gerenciador de Tráfego. Para obter mais informações, consulte [Apontar um domínio de Internet da empresa para um domínio do Gerenciador de Tráfego](../point-a-company-internet-domain-to-a-traffic-manager-domain.md).
1. O tráfego começará a ser direcionado para os pontos de extremidade novamente.

## Excluir um perfil


### Para excluir um perfil

1. Verifique se o registro de recurso DNS no servidor DNS da Internet não usa mais um registro de recurso CNAME que aponta para o nome de domínio de seu perfil do Gerenciador de Tráfego.
1. Selecione o perfil que você deseja excluir. Para selecionar o perfil, na página do Gerenciador de Tráfego, realce o perfil 
1. clicando na coluna ao lado do perfil. Não clique no nome do perfil ou na seta ao lado do nome, pois você será levado para a página de configurações do perfil.
1. Depois de selecionar o perfil, clique em Excluir na parte inferior da página.

## Consulte também

[Gerenciador de Tráfego](../traffic-manager.md)

[Tarefas de Configuração do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/hh744830.aspx)


 

<!---HONumber=July15_HO2-->