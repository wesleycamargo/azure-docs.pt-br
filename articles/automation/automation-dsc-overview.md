---
title: "Visão Geral do DSC de Automação do Azure | Microsoft Docs"
description: "Uma visão geral da DSC (Configuração do Estado Desejado) da Automação do Azure, seus termos e problemas conhecidos"
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: "powershell dsc, configuração de estado desejada, powershell dsc azure"
ms.assetid: fd40cb68-c1a6-48c3-bba2-710b607d1555
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 02/02/2017
ms.author: magoedte;eslesar
translationtype: Human Translation
ms.sourcegitcommit: f0d3a5c1929bb6dafef735ae3f4291e329861cc9
ms.openlocfilehash: b750878703baf143a2e8247bdd01f358e462e598

---
# <a name="azure-automation-dsc-overview"></a>Visão geral do DSC da Automação do Azure

## <a name="what-is-azure-automation-dsc"></a>O que é o DSC da Automação do Azure?

Implantar e manter o estado desejado de seus servidores e recursos de aplicativos pode ser entediante e propenso a erros. Com o DSC (Configuração de Estado Desejado) de Automação do Azure, você pode implantar de forma consistente, monitorar de forma confiável e atualizar automaticamente o estado desejado de todos os seus recursos de TI, em grande escala, a partir da nuvem. Criado no PowerShell DSC, o DSC de Automação pode alinhar a configuração da máquina com um estado específico em máquinas físicas e virtuais (VMs), usando Windows ou Linux e na nuvem ou no local. Você pode habilitar com facilidade a entrega contínua dos serviços de TI com um controle consistente e gerenciar mudanças rápidas em seu ambiente de TI híbrido heterogêneo.

O DSC da Automação do Azure baseia-se nos conceitos básicos introduzidos no DSC do PowerShell para fornecer uma experiência de gerenciamento de configuração ainda mais fácil. O DSC da Automação do Azure oferece a mesma camada de gerenciamento para a [Configuração do Estado Desejado do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview) que a Automação do Azure oferece para scripts do PowerShell atualmente.

O DSC de Automação do Azure permite que você [crie e gerencie e as Configurações do Estado Desejado do PowerShell](https://technet.microsoft.com/library/dn249918.aspx), importe os [Recursos DSC](https://technet.microsoft.com/library/dn282125.aspx) e gere Configurações do Nó do DSC (documentos MOF), tudo na nuvem. Esses itens da DSC são colocados no [servidor de pull de DSC](https://technet.microsoft.com/library/dn249913.aspx) da Automação do Azure para que os nós de destino recebam configurações automaticamente, em conformidade com o estado desejado, e reportem a respectiva conformidade. A Automação do Azure podem se destinar a máquinas virtuais ou físicas, na nuvem ou no local. 

Prefere assistir do que ler? Assista ao vídeo abaixo, de maio de 2015, quando a DSC de Automação do Azure foi anunciada pela primeira vez. **Observação:** embora os conceitos e ciclo de vida abordados neste vídeo estejam corretos, a DSC de Automação do Azure avançou muito desde que este vídeo foi gravado. Agora ele está disponível totalmente, tem uma interface do usuário mais ampla no Portal do Azure e dá suporte a vários recursos adicionais.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="azure-automation-dsc-terms"></a>Termos do DSC da Automação do Azure

### <a name="configuration"></a>Configuração

A DSC do PowerShell introduziu um novo conceito, chamado de configurações. As configurações permitem que você defina, por meio da sintaxe do PowerShell, o estado desejado para seu ambiente. Para usar a DSC a fim de configurar seu ambiente, primeiramente defina um bloco de script do Windows PowerShell usando a palavra-chave de configuração, seguido por um identificador e, em seguida, coloque chaves ({}) para delimitar o bloco.

![texto alternativo](./media/automation-dsc-overview/AADSC_1.png)

Dentro do bloco de configuração, você pode definir configurações de nó que especifiquem a configuração desejada para um conjunto de nós (computadores) em seu ambiente que devem ser configurados da mesma maneira. Assim, uma configuração de nó representa uma "função" que um ou mais nós devem assumir. Um bloco de configuração de nó começa com a palavra-chave do nó. Inclua depois dessa palavra-chave o nome da função, que pode ser uma variável ou expressão. Após o nome da função, use chaves {} para delimitar o bloco de configuração de nó.

![texto alternativo](./media/automation-dsc-overview/AADSC_2.png)

Dentro do bloco de configuração de nó, você pode definir blocos de recursos para configurar recursos específicos da DSC. Um bloco de recursos começa com o nome do recurso, seguido do identificador que você deseja especificar para o bloco e chaves {} para delimitar o bloco.

![texto alternativo](./media/automation-dsc-overview/AADSC_3.png)

Para obter informações mais detalhadas sobre a palavra-chave de configuração, confira: [Configurações DSC](https://msdn.microsoft.com/en-us/powershell/dsc/configurations "Configurações DSC")

A execução (compilação) de uma configuração DSC produz uma ou mais configurações de nós da DSC (documentos MOF), que são o que os nós da DSC aplicam para manter a conformidade com o estado desejado.

O DSC da Automação do Azure permite que você importe, crie e compile configurações do DSC na Automação do Azure, da mesma forma como runbooks podem ser importados, criados e iniciados na Automação do Azure.

> [!IMPORTANT]
> Uma configuração deve conter apenas um bloco de configuração com o mesmo nome que a configuração, no DSC de Automação do Azure.

### <a name="node-configuration"></a>Configuração de nó

Quando uma Configuração da DSC é compilada, uma ou mais configurações de nó são produzidas, dependendo dos blocos de Nó na configuração. Uma configuração de nó é o mesmo que um "MOF" ou "documento de configuração". As configurações de nó representam uma "função", como de servidor Web ou de trabalho, na qual um ou mais nós do estado desejado devem supor a conformidade ou ser verificados em relação à conformidade. Nomes de configurações de nó no DSC de Automação do Azure assumem a forma de “Configuration Name.NodeConfigurationBlockName”.

Os nós da DSC do PS ficam cientes de configurações de nós que eles devem aplicar via push da DSC ou métodos de pull. O DSC de Automação do Azure utiliza o método de pull da DSC, em que nós solicitam configurações de nó que devem aplicar do servidor de pull do DSC de Automação do Azure. Como os nós fazem a solicitação ao DSC de Automação do Azure, eles podem estar atrás de firewalls, ter todas as portas de entrada fechadas etc. Eles precisam apenas de acesso de saída para a Internet (diretamente ou por meio de um proxy).

### <a name="node"></a>Nó

Um nó DSC é qualquer computador que tenha sua configuração gerenciada pelo DSC. Esse computador pode ser uma VM do Azure Windows ou Linux, VM local/host físico, ou estar em outra nuvem pública. Os nós aplicam as configurações de nó para se tornarem conformes e manter a conformidade com o estado desejado que definem. Os nós também reportam a um servidor de relatório sua configuração e o status de conformidade.

A DSC de Automação do Azure torna a integração de nós fácil e flexível. A DSC de Automação do Azure permite alterar a configuração de nó atribuída a cada nó. Quando ocorre uma alteração na configuração de nó, o nó supõe automaticamente uma função diferente alterando sua configuração e começa a se reportar de acordo com sua nova função. 

### <a name="resource"></a>Recurso

Os recursos da DSC são blocos de construção que você pode usar para definir uma DSC (Configuração do Estado Desejado) do Windows PowerShell. A DSC vem com um conjunto de recursos internos, como aqueles para arquivos e pastas, recursos de servidor e funções, configurações do registro, variáveis de ambiente, serviços e processos. Para saber mais sobre a lista completa de recursos internos da DSC e como usá-los, consulte [Recursos internos da Configuração do Estado Desejado do Windows PowerShell](https://msdn.microsoft.com/powershell/dsc/builtinresource).

Os recursos da DSC também podem ser importados como parte dos módulos do PowerShell para estender o conjunto de recursos internos da DSC. Se uma configuração de nó ordenar que o nó deve conter referências a recursos não padrão, esses recursos serão rebaixados do servidor de pull da DSC pelos nós da DSC. Para saber como criar recursos personalizados, consulte [Criar recursos personalizados da Configuração do Estado Desejado do Windows PowerShell](https://msdn.microsoft.com/powershell/dsc/authoringresource).

O DSC de Automação do Azure é fornecida com todos os mesmos recursos internos do DSC que o DSC do PS. Recursos adicionais podem ser adicionados ao DSC de Automação do Azure importando-se módulos do PowerShell que contêm os recursos para a Automação do Azure.

### <a name="compilation-job"></a>Trabalho de compilação

Um trabalho de compilação no DSC de Automação do Azure é uma instância da compilação de uma configuração para criar uma ou mais configurações de nó. Eles são semelhantes aos trabalhos de runbook da Automação do Azure, com a exceção de que não executam realmente tarefa alguma, exceto a criação de configurações de nó. As configurações do nó criadas por um trabalho de compilação são colocadas automaticamente no servidor de pull do DSC de Automação do Azure e substituem versões anteriores das configurações de nó, se elas existirem para essa configuração. O nome de uma configuração de nó produzida por um trabalho de compilação assume a forma de “ConfigurationName.NodeConfigurationBlockName”. Por exemplo, a compilação da configuração a seguir geraria uma única configuração de nó chamada "MyConfiguration.webserver"

![texto alternativo](./media/automation-dsc-overview/AADSC_5.png)

> [!NOTE]
> Assim como os runbooks, as configurações podem ser publicadas. Isso não está relacionado a colocar itens do DSC no servidor de pull do DSC de Automação do Azure. Os trabalhos de compilação fazem com que itens do DSC sejam colocados no servidor de pull do DSC de Automação do Azure. Para obter mais informações sobre como "publicar" na Automação do Azure, consulte [Publicando um Runbook](https://msdn.microsoft.com/library/dn903765.aspx).


## <a name="azure-automation-dsc-lifecycle"></a>Ciclo de vida do DSC de Automação do Azure

Passar de uma conta de automação vazia para um conjunto gerenciado de nós configurados corretamente envolve um conjunto de processos para definir as configurações, transformá-las em configurações de nó e integrar os nós ao DSC deAutomação do Azure e a essas configurações de nó. O diagrama a seguir ilustra o ciclo de vida do DSC de Automação do Azure:

![texto alternativo](./media/automation-dsc-overview/DSCLifecycle.png)

A imagem a seguir ilustra o processo passo a passo detalhado no ciclo de vida de DSC. Ela inclui formas diferentes de importação e aplicação de uma configuração em nós na Automação do Azure, os componentes necessários para uma máquina local dar suporte a DSC e as interações entre diferentes componentes.

![Arquitetura da DSC](./media/automation-dsc-overview/dsc-architecture.png)

## <a name="gotchas--known-issues"></a>Problemas gerais conhecidos:

* A última versão do WMF 5 deve ser instalada para que o agente da DSC do PowerShell para Windows possa se comunicar com a Automação do Azure. A versão mais recente do agente DSC do PowerShell para Linux deve ser instalada para que o Linux possa se comunicar com a Automação do Azure.
* Se um computador já estiver registrado como um nó na DSC de Automação do Azure antes da atualização para o WMF5, cancele o registro dele na DSC de Automação do Azure e registre-o novamente após a conclusão da instalação do WMF 5. Exclua o arquivo $env:windir\system32\configuration\DSCEngineCache.mof antes de fazer o registro novamente
* Se o WMF 5 RTM for instalado sobre o WMF 5 Production Preview, a DSC poderá não funcionar corretamente. Para corrigir esse problema, execute o seguinte comando em uma sessão do PowerShell com privilégios elevados (execute como administrador): `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`
* Atualmente, a DSC de Automação do Azure não dá suporte a configurações DSC parcial ou compostas. No entanto, os recursos de composição da DSC podem ser importados e usados nas Configurações DSC de Automação do Azure como no PowerShell local, permitindo a reutilização da configuração.
* O servidor de pull do DSC do PowerShell tradicional espera que os zips do módulo sejam colocados no servidor de pull no formato **ModuleName_Version.zip"**. A Automação do Azure espera que módulos do PowerShell sejam importados com nomes no formato **ModuleName.zip**. Consulte [esta postagem de blog](https://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/) para obter mais informações sobre o formato do Módulo de Integração necessário para importar o módulo para a Automação do Azure.
* Os módulos do PowerShell importados para a Automação do Azure não podem conter arquivos .doc ou .docx. Alguns módulos do PowerShell contendo recursos da DSC contêm esses arquivos, para fins de ajuda. Esses arquivos devem ser removidos dos módulos antes da importação na Automação do Azure.
* Quando um nó é registrado pela primeira vez em uma conta de Automação do Azure ou o nó é alterado para ser mapeado para uma configuração de nó diferente no lado do servidor, seu status é ‘Compatível’. Isso ocorrerá mesmo se o status do nó não for compatível com a configuração de nó para o qual ele agora está mapeado. Depois que o nó envia seu primeiro relatório após o registro ou uma alteração de mapeamento de configuração de nó, o status do nó é confiável.
* Ao integrar uma VM com Windows do Azure para gerenciamento do DSC de Automação do Azure usando qualquer um dos nossos métodos de integração direta, pode levar até uma hora para que a VM apareça como um nó do DSC de Automação do Azure. Isso se deve à instalação do Windows Management Framework 5.0 na VM pela extensão DSC da VM do Azure, que precisa integrar a VM ao DSC de Automação do Azure.
* Após o registro, cada nó negocia automaticamente um certificado exclusivo para autenticação, que expira depois de um ano. Atualmente, o protocolo de registro DSC do PowerShell não pode renovar automaticamente certificados quando eles estão prestes a expirar, então você precisa registrar novamente os nós após um ano. Antes de registrar novamente, certifique-se de que cada nó está executando o Windows Management Framework 5.0 RTM. Se o certificado de autenticação de um nó expirar e o nó não for registrado novamente, o nó não será capaz de se comunicar com a Automação do Azure e será marcado como ‘Sem resposta’. O novo registro é feito da mesma maneira como você registrou o nó inicialmente. A realização de um novo registro a 90 dias ou menos do tempo de expiração do certificado, ou a qualquer momento após o tempo de expiração do certificado, vai resultar na geração e uso de um novo certificado.


## <a name="related-articles"></a>Artigos relacionados

* [Máquinas de integração para o gerenciamento pelo DSC de Automação do Azure](automation-dsc-onboarding.md)
* [Configurações de compilação no DSC de Automação do Azure](automation-dsc-compile.md)
* [cmdlets do DSC de Automação do Azure](https://msdn.microsoft.com/library/mt244122.aspx)
* [preço da DSC de Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
* [Implantação contínua em VMs de IaaS usando o DSC de Automação do Azure e o Chocolatey](automation-dsc-cd-chocolatey.md)




<!--HONumber=Feb17_HO1-->


