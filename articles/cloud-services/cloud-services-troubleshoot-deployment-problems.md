<properties
 pageTitle="Solucionar problemas de implantação do Serviço de Nuvem | Microsoft Azure"
 description="Existem alguns problemas comuns que você experimentar ao implantar um Serviço de Nuvem no Azure. Este artigo fornece soluções para alguns deles."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="01/20/2016"
   ms.author="daleche" />

# Como solucionar possíveis problemas de implantação do Serviço de Nuvem

Quando você implanta um pacote de aplicativos do Serviço de Nuvem no Azure, é possível obter informações sobre a implantação no painel **Propriedades** no portal do Azure. É possível usar os detalhes nesse painel para ajudá-lo a solucionar problemas com o serviço de nuvem, e você pode fornecer essas informações para o suporte do Azure ao abrir uma nova solicitação de suporte.

Você pode encontrar o painel **Propriedades** da seguinte maneira:

* No portal do Azure: clique na implantação do seu serviço de nuvem, clique em **Todas as configurações**, e, em seguida, clique em **propriedades**.
* No portal clássico do Azure: clique na implantação do seu serviço de nuvem, clique em **PAINEL**, localize no canto inferior direito da página (em **vis~ao rápida**). Lembre-se de que não há nenhum texto de "Propriedades" para esse painel.

> [AZURE.NOTE]É possível copiar o conteúdo do painel Propriedades na área de transferência clicando no ícone no canto superior direito do painel.

## Contate o Suporte ao Cliente do Azure

Se você precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure MSDN e Excedente de Pilha](http://azure.microsoft.com/support/forums/).

Como alternativa, você também pode registrar um incidente de suporte do Azure. Acesse o [site de Suporte do Azure](http://azure.microsoft.com/support/options/) e clique em **Obter Suporte**. Para obter informações sobre como usar o Suporte do Azure, leia as [Perguntas Frequentes de Suporte do Microsoft Azure](http://azure.microsoft.com/support/faq/).



## Problema: não consigo acessar meu site mesmo que minha implantação tenha sido iniciada e todas as instâncias de função estejam prontas

O link da URL do site exibido no portal não inclui a porta. A porta padrão para sites é 80. No entanto, se o seu aplicativo for configurado para ser executado em uma porta diferente, você deverá adicionar a porta correta à URL ao acessar o site.

1. No portal do Azure, clique na implantação do serviço de nuvem.
2. No painel **Propriedades** do portal do Azure, verifique as portas para as instâncias de função (em Pontos de Extremidade de Entrada).
3. Se a porta não for *80*, adicione o valor de porta correto à URL ao acessar o aplicativo. Para especificar uma porta não padrão, digite a URL, seguida de dois pontos (:) e do número da porta sem espaços.

## Problema: minhas instâncias de funções foram reiniciadas sem que eu tenha realizado nenhuma ação

A recuperação de serviço ocorre automaticamente quando o Azure detecta nós problemáticos e move as instâncias de função para novos nós. Quando isso ocorrer, você poderá ver suas instâncias de função serem reiniciadas automaticamente. Para descobrir se a recuperação de serviço ocorreu:

1. No portal do Azure, clique na implantação do serviço de nuvem.
2. No painel **Propriedades** do portal do Azure, examine as informações e determine se a recuperação de serviço ocorreu durante o tempo em que você observou a reinicialização das funções.

As funções também serão reiniciadas aproximadamente uma vez por mês durante as atualizações do SO Host e do SO Convidado. Para saber mais, confira a postagem no blog [A instância de função é reinicializada devido às atualizações do SO](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## Problema: não consigo fazer uma permuta de VIP. Recebi uma mensagem de erro

Uma permuta de VIP não é permitida se uma atualização de implantação estiver em andamento. As atualizações de implantação podem ocorrer automaticamente quando:

* Um novo sistema operacional convidado está disponível e você está configurado para receber atualizações automáticas
* A recuperação de serviço ocorre

Para descobrir se uma atualização automática está impedindo que você faça uma permuta de VIP:

1. No portal do Azure, clique na implantação do serviço de nuvem.
2. No painel **Propriedades** do portal do Azure, observe o valor de **Status**. Se ele estiver **Pronto**, verifique a **Última operação** para ver se ocorreu uma operação recentemente que possa impedir a permuta de VIP.
3. Repita as etapas 1 e 2 para a implantação de produção.
4. Se uma atualização automática estiver em andamento, aguarde sua conclusão antes de tentar fazer a permuta de VIP.

## Problema: uma instância de função está alternando entre os estados Iniciado, Inicializando, Ocupado e Parado

Essa condição pode indicar um problema com o código do aplicativo, pacote ou arquivo de configuração. Se for true, você deverá ver o Status sendo alterado em intervalos de alguns minutos e o portal do Azure poderá indicar algo como **Reciclando**, **Ocupado** ou **Inicializando**. Isso indica que há algo errado com o aplicativo que está impedindo a execução da instância de função.

Para obter mais informações sobre como solucionar esse problema, veja a postagem do blog [Dados de Diagnóstico de Computação do Azure PaaS] e [Problemas Comuns que Fazem as Funções Reciclarem](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)

## Problema: meu aplicativo parou de funcionar

1. No portal do Azure, clique na instância de função.
2. No painel **Propriedades** do portal do Azure, considere as seguintes condições para resolver o problema:
   * Se a instância de função tiver sido interrompida recentemente (é possível verificar o valor de **Anular contagem**), a implantação pode estar sendo atualizada. Aguarde para ver se a instância de função retoma o funcionamento por conta própria.
   * Se a instância de função estiver Ocupada, verifique o código do aplicativo para ver se o evento [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) foi manipulado. Talvez seja necessário adicionar ou corrigir algum código que manipula esse evento.
   * Examine os dados de diagnóstico e os cenários de solução de problemas na postagem do blog [Dados de Diagnóstico de Computação do Azure PaaS].

>[AZURE.WARNING]Se você reiniciar o serviço de nuvem, redefina as propriedades da implantação apagando de maneira efetiva as informações do problema original.

## Próximas etapas

Confira mais [artigos sobre solução de problemas](..\?tag=top-support-issue&service=cloud-services) para serviços de nuvem.

Para saber como solucionar problemas das funções do Serviço de Nuvem usando os dados de diagnóstico do computador Azure PaaS, veja a [série de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

<!---HONumber=AcomDC_0121_2016-->