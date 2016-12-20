---
title: "Tipos de Ponto de Extremidade do Gerenciador de Tráfego | Microsoft Docs"
description: "Este artigo explica os diferentes tipos de pontos de extremidade que podem ser usados com o Gerenciador de Tráfego do Azure"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 4e506986-f78d-41d1-becf-56c8516e4d21
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 2ced9e73a65160f4f3c8ba92affc143ca554d07c

---

# <a name="traffic-manager-endpoints"></a>Pontos de extremidade do Gerenciador de Tráfego
O Gerenciador de Tráfego do Microsoft Azure permite controlar como o tráfego de rede é distribuído para implantações de aplicativos executados em diferentes datacenters. Você configurar cada implantação de aplicativo como um “ponto de extremidade” no Gerenciador de Tráfego. Quando o Gerenciador de Tráfego recebe uma solicitação DNS, ele escolhe um ponto de extremidade disponível para retornar na resposta DNS. O Gerenciador de Tráfego baseia a escolha no status atual do ponto de extremidade e o método de roteamento de tráfego. Para obter mais informações, consulte [Como o Gerenciador de Tráfego Funciona](traffic-manager-how-traffic-manager-works.md).

Há três tipos de ponto de extremidade suportados pelo Gerenciador de Tráfego:

* **pontos de extremidade do Azure** são usados para os serviços hospedados no Azure.
* **pontos de extremidade externos** são usados para os serviços hospedados fora do Azure, no local ou com um provedor de hospedagem diferente.
* **pontos de extremidade aninhados** são usados para combinar os perfis do Gerenciador de Tráfego para criar esquemas de roteamento de tráfego mais flexíveis para suportar as necessidades de implantações maiores e mais complexas.

Não há nenhuma restrição sobre como os pontos de extremidade de diferentes tipos são combinados em um único perfil do Gerenciador de Tráfego. Cada perfil pode conter qualquer combinação de tipos de ponto de extremidade.

As seções a seguir descrevem cada tipo de ponto de extremidade com mais detalhes.

## <a name="azure-endpoints"></a>pontos de extremidade do Azure

Os pontos de extremidade do Azure são usados para serviços baseados no Azure no Gerenciador de Tráfego. Há suporte para os seguintes tipos de recursos do Azure:

* VMs IaaS “Clássicas” e serviços de nuvem PaaS.
* Aplicativos Web
* Recursos PublicIPAddress (que podem ser conectados às VMs diretamente ou por meio de um Azure Load Balancer). O publicIpAddress deve ter um nome DNS atribuído para ser usado no perfil do Gerenciador de Tráfego.

Os recursos de PublicIPAddress são recursos do Azure Resource Manager. Eles não existem no modelo de implantação clássico. Assim, eles têm suporte apenas em experiências do Azure Resource Manager do Gerenciador de Tráfego. Os outros tipos de ponto de extremidade têm suporte por meio do Gerenciador de Recursos e o modelo de implantação clássico.

Ao usar os pontos de extremidade do Azure, o Gerenciador de Tráfego detecta quando uma VM IaaS “Clássica”, serviço de nuvem ou Aplicativo Web é interrompido e iniciado. Esse status é refletido no status de ponto de extremidade. Consulte detalhes em [Monitoramento do ponto de extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md#endpoint-and-profile-status). Quando o serviço subjacente é interrompido, o Gerenciador de Tráfego não executa verificações de integridade do ponto de extremidade nem direciona o tráfego para o ponto de extremidade. Nenhum evento de cobrança do Gerenciador de Tráfego ocorre para a instância parada. Quando o serviço é reiniciado, a cobrança é retomada e o ponto de extremidade está qualificado para receber tráfego. Essa detecção não se aplica aos pontos de extremidade PublicIpAddress.

## <a name="external-endpoints"></a>pontos de extremidade externos

Pontos de extremidade externos são usados para serviços fora do Azure. Por exemplo, um serviço hospedado localmente ou com um provedor diferente. Pontos de extremidade externos podem ser usados sozinhos ou combinados com os Pontos de Extremidade do Azure no mesmo perfil do Gerenciador de Tráfego. Combinar pontos de extremidade do Azure com pontos de extremidade Externos permite vários cenários:

* Em um modelo ativo-ativo ou ativo-passivo de failover, use o Azure para proporcionar maior redundância para um aplicativo local existente.
* Para reduzir a latência de aplicativo para os usuários em todo o mundo, estenda um aplicativo local existente para locais geográficos adicionais no Azure. Para obter mais informações, consulte [Roteamento de tráfego por “Desempenho” do Gerenciador de Tráfego](traffic-manager-routing-methods.md#performance-traffic-routing-method).
* Usando o Azure para fornecer capacidade adicional para um aplicativo local existente, seja continuamente ou como uma solução “intermitência para a nuvem” para atender um pico de demanda.

Em alguns casos, pode ser útil usar pontos de extremidade externos para fazer referência aos serviços do Azure (consulte exemplos em [	Perguntas Frequentes](#faq)). Neste caso, as verificações de integridade são cobradas à taxa dos pontos de extremidade do Azure, não à taxa dos pontos de extremidade Externos. No entanto, diferentemente dos pontos de extremidade do Azure, se você parar ou excluir o serviço subjacente, a cobrança da verificação de integridade continuará até você desabilitar ou excluir o ponto de extremidade no Gerenciador de tráfego.

## <a name="nested-endpoints"></a>pontos de extremidade aninhados

Pontos de extremidade aninhados combinam vários perfis do Gerenciador de Tráfego para criar esquemas flexíveis de roteamento de tráfego e dar suporte às necessidades de implantações maiores e complexas. Com os pontos de extremidade aninhados, um perfil “filho” é adicionado como um ponto de extremidade a um perfil “pai”. Os perfis pai e filho podem conter outros pontos de extremidade de qualquer tipo, incluindo outros perfis aninhados. Para obter mais informações, consulte [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Aplicativos Web como pontos de extremidade

Algumas considerações adicionais se aplicam ao configurar os Aplicativos Web como pontos de extremidade no Gerenciador de Tráfego:

1. Somente Aplicativos Web na SKU “Standard” ou superior estão qualificados para o uso com o Gerenciador de Tráfego. Falha nas tentativas de adicionar um Aplicativo Web de uma SKU inferior. Fazer o downgrade da SKU de um aplicativo Web existente resulta em o Gerenciador de tráfego não enviar mais tráfego para esse Aplicativo Web.
2. Quando um ponto de extremidade recebe uma solicitação HTTP, ele usa o cabeçalho “host” na solicitação para determinar qual aplicativo Web deve atender à solicitação. O cabeçalho de host contém o nome DNS usado para iniciar a solicitação, por exemplo, “contosoapp.azurewebsites.net”. Para usar um nome DNS diferente com seu Aplicativo Web, o nome DNS deve ser registrado como um nome de domínio personalizado para o Aplicativo. Ao adicionar um ponto de extremidade do Aplicativo Web como um ponto de extremidade do Azure, o nome DNS do perfil do Gerenciador de Tráfego é registrado automaticamente para o Aplicativo. Esse registro é removido automaticamente quando o ponto de extremidade é excluído.
3. Cada perfil do Gerenciador de Tráfego pode ter, no máximo, um ponto de extremidade do aplicativo Web de cada região do Azure. Para contornar essa restrição, você pode configurar um Aplicativo Web como um ponto de extremidade Externo. Consulte mais informações em [Perguntas Frequentes](#faq).

## <a name="enabling-and-disabling-endpoints"></a>Habilitando e desabilitando os pontos de extremidade

Desabilitar um ponto de extremidade no Gerenciador de Tráfego pode ser útil para remover temporariamente o tráfego de um ponto de extremidade que esteja no modo de manutenção ou sendo reimplantado. Quando o ponto de extremidade estiver em execução novamente, ele poderá ser reabilitado.

Os pontos de extremidade podem ser habilitados e desabilitados por meio do portal do Gerenciador de Tráfego, PowerShell, CLI ou API REST, todos com suporte no Gerenciador de Recursos e no modelo de implantação clássico.

> [!NOTE]
> Desabilitar um ponto de extremidade do Azure não tem nenhuma relação com o estado de implantação no Azure. Um serviço do Azure, como uma VM ou aplicativo Web, permanece em execução e capaz de receber tráfego mesmo quando desabilitado no Gerenciador de Tráfego. O tráfego pode ser tratado diretamente para a instância do serviço, em vez de por meio do nome DNS do perfil do Gerenciador de Tráfego. Para obter mais informações, consulte [como o Gerenciador de Tráfego funciona](traffic-manager-how-traffic-manager-works.md).

A qualificação atual de cada ponto de extremidade para receber tráfego depende dos seguintes fatores:

* O status do perfil (habilitado/desabilitado)
* O status de ponto de extremidade (habilitado/desabilitado)
* Os resultados das verificações de integridade para o ponto de extremidade

Para obter detalhes, consulte [Monitoramento do ponto de extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Como o Gerenciador de Tráfego funciona no nível do DNS, ele não consegue influenciar as conexões existentes com qualquer ponto de extremidade. Quando um ponto de extremidade não estiver disponível, o Gerenciador de Tráfego direcionará novas conexões para outro ponto de extremidade disponível. No entanto, o host atrás do ponto de extremidade desabilitado ou não íntegro pode continuar recebendo tráfego por meio de conexões existentes até que as sessões sejam encerradas. Os aplicativos devem limitar a duração da sessão para permitir a drenagem do tráfego de conexões existentes.

Se todos os pontos de extremidade em um perfil estiverem desabilitados ou se o próprio perfil estiver desabilitado, o Gerenciador de Tráfego enviará uma resposta “NXDOMAIN” para uma nova consulta DNS.

## <a name="faq"></a>Perguntas frequentes

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Posso usar o Gerenciador de Tráfego com pontos de extremidade de várias assinaturas?

Não é possível usar pontos de extremidade de várias assinaturas com Aplicativos Web do Azure. Os Aplicativos Web do Azure exigem que qualquer nome de domínio personalizado usado com Aplicativos Web seja usado somente em uma única assinatura. Não é possível usar Aplicativos Web de várias assinaturas com o mesmo nome de domínio.

Para outros tipos de ponto de extremidade, é possível usar o Gerenciador de Tráfego com pontos de extremidade de mais de uma assinatura. O modo de configurar o Gerenciador de Tráfego depende de se você está usando o modelo de implantação clássico ou a experiência do Resource Manager.

* No Gerenciador de Recursos, os pontos de extremidade de qualquer assinatura podem ser adicionados ao Gerenciador de Tráfego, desde que a pessoa que configura o perfil do Gerenciador de Tráfego tenha o acesso de leitura ao ponto de extremidade. Essas permissões podem ser concedidas usando o [RBAC (controle de acesso baseado em função) do Azure Resource Manager](../active-directory/role-based-access-control-configure.md).
* Na interface do modelo de implantação clássico, o Gerenciador de Tráfego requer que os Serviços de Nuvem ou os Aplicativos Web configurados como pontos de extremidade do Azure residam na mesma assinatura que o perfil do Gerenciador de Tráfego. Pontos de extremidade de Serviço de Nuvem em outras assinaturas podem ser adicionados ao Gerenciador de Tráfego como pontos de extremidade “externos”. Esses pontos de extremidade externos são cobrados como pontos de extremidade do Azure, em vez de à taxa externa.

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Posso usar o Gerenciador de Tráfego com os slots de “Preparo” do Serviço de Nuvem?

Sim. Os slots de “preparo” do Serviço de Nuvem podem ser configurados no Gerenciador de Tráfego como pontos de extremidade Externos. Verificações de integridade ainda são cobradas à taxa de Pontos de Extremidade do Azure. Como o tipo do ponto de extremidade externo está em uso, as alterações no serviço subjacente não serão selecionadas automaticamente. Com pontos de extremidade externos, o Gerenciador de Tráfego não consegue detectar quando o Serviço de Nuvem é parado ou excluído. Portanto, o Gerenciador de Tráfego continua cobrando por verificações de integridade até que o ponto de extremidade seja desabilitado ou excluído.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>O Gerenciador de Tráfego dá suporte aos pontos de extremidade IPv6?

No momento, o Gerenciador de Tráfego não oferece servidores de nome endereçáveis por IPv6. No entanto, o Gerenciador de Tráfego ainda pode ser usado por clientes IPv6 que se conectem a pontos de extremidade IPv6. Um cliente não faz solicitações DNS diretamente ao Gerenciador de tráfego. Em vez disso, o cliente usa um serviço DNS recursivo. Um cliente somente IPv6 envia solicitações para o serviço DNS recursivo via IPv6. Então o serviço recursivo deve conseguir contatar os servidores de nome do Gerenciador de Tráfego usando IPv4.

O Gerenciador de Tráfego responde com o nome DNS do ponto de extremidade. Para dar suporte a um ponto de extremidade IPv6, deve haver um registro DNS AAAA apontando o nome DNS do ponto de extremidade para o endereço IPv6. Verificações de integridade do Gerenciador de Tráfego dão suporte apenas a endereços IPv4. O serviço deve expor um ponto de extremidade IPv4 no mesmo nome DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Posso usar o Gerenciador de Tráfego com mais de um Aplicativo Web na mesma região?

Normalmente, o Gerenciador de Tráfego é usado para direcionar o tráfego para os aplicativos implantados em regiões diferentes. No entanto, ele também pode ser usado onde um aplicativo tem mais de uma implantação na mesma região. Os pontos de extremidade do Gerenciador de Tráfego do Azure não permitem adicionar mais de um ponto de extremidade do Aplicativo Web da mesma região do Azure ao mesmo perfil do Gerenciador de Tráfego.

As etapas a seguir fornecem uma solução alternativa para essa restrição:

1. Verifique se seus pontos de extremidade estão em diferentes “unidades de escala” do aplicativo Web'. Um nome de domínio deve mapear para um único site em uma unidade de escala. Portanto, dois aplicativos Web na mesma unidade de escala não podem compartilhar um perfil do Gerenciador de Tráfego.
2. Adicione seu nome de domínio intuitivo como um nome de host personalizado a cada Aplicativo Web. Cada Aplicativo Web deve estar em uma unidade de escala diferente. Todos os Aplicativos Web devem pertencer à mesma assinatura.
3. Adicione um (e somente um) ponto de extremidade do Aplicativo Web ao perfil do Gerenciador de Tráfego, como um ponto de extremidade do Azure.
4. Adicione cada ponto de extremidade do Aplicativo Web extra ao seu perfil do Gerenciador de Tráfego como um ponto de extremidade externo. Pontos de extremidade externos só podem ser adicionados usando o modelo de implantação do Gerenciador de Recursos.
5. Crie um registro DNS CNAME em seu domínio intuitivo que aponte para o seu nome DNS de perfil do Gerenciador de Tráfego (<...>.trafficmanager.net).
6. Acesse seu site usando o nome de domínio intuitivo, não o nome DNS do perfil do Gerenciador de Tráfego.

## <a name="next-steps"></a>Próximas etapas

* Saiba [como funciona o Gerenciador de Tráfego](traffic-manager-how-traffic-manager-works.md).
* Saiba mais sobre [o monitoramento de ponto de extremidade e failover automático](traffic-manager-monitoring.md)do Gerenciador de Tráfego.
* Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)do Gerenciador de Tráfego.




<!--HONumber=Nov16_HO3-->


