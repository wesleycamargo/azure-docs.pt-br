<properties
   pageTitle="Apontar um domínio de Internet da empresa para um domínio do Gerenciador de Tráfego"
   description="Este artigo ajudará a indicar o nome de domínio de sua empresa para um nome de domínio do Gerenciador de Tráfego."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma;cherylmc" />

# Apontar um domínio de Internet da empresa para um domínio do Gerenciador de Tráfego

Para apontar o nome de domínio da empresa para um nome de domínio do Gerenciador de Tráfego, modifique o registro de recurso DNS no servidor DNS da Internet para usar o tipo de registro CNAME, que mapeia o nome de domínio da empresa para o nome de domínio do seu perfil do Gerenciador de Tráfego. Você pode ver o nome do domínio do Gerenciador de Tráfego na seção **Geral** da página Configuração do perfil do Gerenciador de Tráfego.

Por exemplo, para apontar o nome de domínio de empresa **www.contoso.com** para o nome de domínio do Gerenciador de Tráfego **contoso.trafficmanager.net**, você atualizaria seu registro de recurso DNS para o seguinte:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Todas as solicitações de tráfego para *www.contoso.com* agora serão direcionadas para *contoso.trafficmanager.net*.

>[AZURE.IMPORTANT]Não é possível indicar um domínio de segundo nível, como *contoso.com*, para o domínio do Gerenciador de Tráfego. Essa é uma limitação do protocolo DNS, que não permite registros CNAME para nomes de domínio de segundo nível.

## Consulte também

[Sobre os métodos de balanceamento de carga do Gerenciador de Tráfego](traffic-manager-load-balancing-methods.md)

[Tarefas de Configuração do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Visão geral do Gerenciador de Tráfego](traffic-manager-overview.md)
 

<!---HONumber=July15_HO4-->