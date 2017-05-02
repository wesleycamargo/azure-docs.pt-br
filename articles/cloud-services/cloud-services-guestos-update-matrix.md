---
title: "Saiba mais sobre as versões mais recentes de SO convidado do Azure | Microsoft Docs"
description: "As últimas notícias de versão e a compatibilidade do SDK para o SO convidado dos serviços de nuvem do Azure."
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: 
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 4/18/2017
ms.author: raiye
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: d471f5826cc05fb52ff4c24184628235477c075d
ms.lasthandoff: 04/18/2017


---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Matriz de compatibilidade de versões de SOs Convidados e do SDK do Azure
Fornece a você informações atualizadas sobre as versões mais recentes do SO convidado do Azure para serviços de nuvem. Essas informações o ajudarão a planejar seu caminho de atualização antes de um SO convidado ser desabilitado. Se você configurar suas funções para usar atualizações *automáticas* de SO convidado, conforme descrito em [Configurações de atualização de SO convidado do Azure][Azure Guest OS Update Settings], não é essencial ler esta página.

> [!IMPORTANT]
> Esta página se aplica às funções de trabalho e da Web dos Serviços de Nuvem, que são executados em um SO convidado. Ela **não se aplica** a Máquinas Virtuais IaaS. 
> 
> 

<!-- -->

> [!TIP]
> Assine o [RSS feed de atualização do SO convidado][rss] para receber as notificações mais recentes sobre todas as alterações do SO convidado.
> 
> 

Não está certo quanto ao que é o SO convidado ou como as versões do SO convidado funcionam? Leia [esta](#how-it-works) seção.

## <a name="news-updates"></a>Notícias atualizadas
###### <a name="april-18-2017"></a>**18 de abril de 2017**
A distribuição do SO convidado de abril começa em 18 de abril e está projetada para ser lançada em 9 de maio.

###### <a name="april-10-2017"></a>**10 de abril de 2017**
A distribuição do SO convidado de março começou em 14 de março de 2017, sendo lançada em 10 de abril de 2017.

###### <a name="january-10-2017"></a>**10 de janeiro de 2017**
O SO convidado de janeiro contém patches que afetam apenas o família 2 do SO (Windows 2008 Server R2). Portanto, lançamos apenas a imagem da Família de Sistemas Operacionais 2 (WA-GUEST-OS-2.59_201701-01) para este mês. Para todas as outras famílias de sistemas operacionais, o sistema operacional de dezembro (201612-01) permanece como a versão mais recente.

###### <a name="december-14-2016"></a>**14 de dezembro de 2016**
A distribuição do SO Convidado de dezembro começa em 14 de dezembro de 2016 e deve ser lançada em 13 de janeiro de 2017.

###### <a name="november-20-2016"></a>**20 de novembro de 2016**
A distribuição do SO convidado de novembro começa em 8 de novembro de 2016 e deve ser lançada em 8 de dezembro de 2016.

###### <a name="october-23-2016"></a>**23 de outubro de 2016**
O Windows Server 2016 será lançado como um SO da Família 5 em 1º de novembro de 2016, com suporte do .NET 4.6.


## <a name="releases"></a>Lançamentos
## <a name="family-5-releases"></a>Versões da Família 5
**Windows Server 2016**

.NET Framework instalado: 4.0, 4.5, 4.5.1, 4.5.2, 4.6, 4.6.1, 4.6.2

> [!NOTE]
> As datas com * estão sujeitas a alterações. 
> 
> A senha de RDP para a Família do sistema operacional 5 deverá ter um mínimo de 10 caracteres.
>

| Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| --- | --- | --- | --- |
| WA-GUEST-OS-5.5_201703-01 |10 de abril de 2017 |Post 5.7 |TBD | 
| WA-GUEST-OS-5.4_201612-01 |10 de janeiro de 2017 |Post 5.6 |TBD | 
| WA-GUEST-OS-5.3_201611-01 |14 de dezembro de 2016 |Post 5.5 |TBD |
|~~WA-GUEST-OS-5.2_201610-02~~ |1 de novembro de 2016 |10 de abril de 2017 |TBD |

## <a name="family-4-releases"></a>Versões da Família 4
**Windows Server 2012 R2**

É compatível com .NET 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> As datas com * estão sujeitas a alterações
> 
> 

| Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| --- | --- | --- | --- |
| WA-GUEST-OS-4.40_201703-01 |10 de abril de 2017 |Post 4.42 |TBD |
| WA-GUEST-OS-4.39_201612-01 |10 de janeiro de 2017 |Post 4.41 |TBD |
| WA-GUEST-OS-4.38_201611-01 |14 de dezembro de 2016 |Post 4.40 |TBD |
|~~WA-GUEST-OS-4.37_201610-02~~ |16 de novembro de 2016 |10 de abril de 2017 |TBD |
|~~WA-GUEST-OS-4.36_201609-01~~ |13 de outubro de 2016 |Jan 14 2017 |TBD |
|~~WA-GUEST-OS-4.35_201608-01~~ |13 de setembro de 2016 |16 de dezembro de 2016 |TBD |
|~~WA-GUEST-OS-4.34_201607-01~~ |8 de agosto de 2016 |13 de novembro de 2016 |TBD |
|~~WA-GUEST-OS-4.33_201606-01~~ |13 de julho de 2016 |13 de outubro de 2016 |TBD |
|~~WA-GUEST-OS-4.32_201605-01~~ |10 de junho de 2016 |8 de setembro de 2016 |TBD |
|~~WA-GUEST-OS-4.31_201604-01~~ |2 de maio de 2016 |13 de agosto de 2016 |TBD |
|~~WA-GUEST-OS-4.30_201603-01~~ |7 de abril de 2016 |10 de julho de 2016 |TBD |
|~~WA-GUEST-OS-4.29_201602-02~~ |12 de março de 2016 |2 de junho de 2016 |TBD |


## <a name="family-3-releases"></a>Versões da Família 3
**Windows Server 2012**

É compatível com .NET 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> As datas com * estão sujeitas a alterações
> 
> 

| Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| --- | --- | --- | --- |
| WA-GUEST-OS-3.47_201703-01 |10 de abril de 2017 |Post 3.49 |TBD |
| WA-GUEST-OS-3.46_201612-01 |10 de janeiro de 2017 |Post 3.48 |TBD |
| WA-GUEST-OS-3.45_201611-01 |14 de dezembro de 2016 |Post 3.47 |TBD |
| WA-GUEST-OS-3.44_201610-02 |16 de novembro de 2016 |1º de maio de 2017 |TBD |
| ~~WA-GUEST-OS-3.43_201609-01~~ |13 de outubro de 2016 |Jan 14 2017 |TBD |
| ~~WA-GUEST-OS-3.42_201608-01~~ |13 de setembro de 2016 |16 de dezembro de 2016 |TBD |
| ~~WA-GUEST-OS-3.41_201607-01~~ |8 de agosto de 2016 |13 de novembro de 2016 |TBD |
| ~~WA-GUEST-OS-3.40_201606-01~~ |13 de julho de 2016 |13 de outubro de 2016 |TBD |
| ~~WA-GUEST-OS-3.39_201605-01~~ |10 de junho de 2016 |8 de setembro de 2016 |TBD |
| ~~WA-GUEST-OS-3.38_201604-01~~ |2 de maio de 2016 |13 de agosto de 2016 |TBD |
| ~~WA-GUEST-OS-3.37_201603-01~~ |7 de abril de 2016 |10 de julho de 2016 |TBD |
| ~~WA-GUEST-OS-3.36_201602-02~~ |12 de março de 2016 |2 de junho de 2016 |TBD |


## <a name="family-2-releases"></a>Versões da Família 2
**Windows Server 2008 R2 SP1**

Dá suporte a .NET 3.5, 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> As datas com * estão sujeitas a alterações
> 
> 

| Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| --- | --- | --- | --- |
| WA-GUEST-OS-2.60_201703-01 |10 de abril de 2017 |Post 2.62 |TBD |
| WA-GUEST-OS-2.59_201701-01 |10 de janeiro de 2017 |Post 2.61 |TBD |
| WA-GUEST-OS-2.58_201612-01 |10 de janeiro de 2017 |Post 2.60 |TBD |
|~~WA-GUEST-OS-2.57_201611-01~~ |14 de dezembro de 2016 |10 de abril de 2017 |TBD |
|~~WA-GUEST-OS-2.56_201610-02~~ |16 de novembro de 2016 |10 de fevereiro de 2017 |TBD |
|~~WA-GUEST-OS-2.55_201609-01~~ |13 de outubro de 2016 |Jan 14 2017 |TBD |
|~~WA-GUEST-OS-2.54_201608-01~~ |13 de setembro de 2016 |16 de dezembro de 2016 |TBD |
|~~WA-GUEST-OS-2.53_201607-01~~ |8 de agosto de 2016 |13 de novembro de 2016 |TBD |
|~~WA-GUEST-OS-2.52_201606-01~~ |13 de julho de 2016 |13 de outubro de 2016 |TBD |
|~~WA-GUEST-OS-2.51_201605-01~~ |10 de junho de 2016 |8 de setembro de 2016 |TBD |
|~~WA-GUEST-OS-2.50_201604-01~~ |2 de maio de 2016 |13 de agosto de 2016 |TBD |
|~~WA-GUEST-OS-2.49_201603-01~~ |7 de abril de 2016 |10 de julho de 2016 |TBD |
|~~WA-GUEST-OS-2.48_201602-02~~|12 de março de 2016 |2 de junho de 2016 |TBD |


## <a name="msrc-patch-updates"></a>Atualizações de patch do MSRC
A lista de patches incluídos em cada lançamento mensal de SO convidado está disponível [aqui][patches].

## <a name="sdk-support"></a>Suporte a SDK
Embora a [política de desativação do SDK do Azure][retire policy sdk] indique que somente as versões acima da 2.2 têm suporte, as famílias de SO convidado específicas permitem que você use as versões anteriores. Você sempre deve usar o SDK mais recente com suporte. 

| Família do SO convidado | Versões compatíveis do SDK |
| --- | --- |
| 5 |Versão 2.9.5.1+ |
| 4 |Versão 2.1+ |
| 3 |Versão 1.8+ |
| 2 |Versão 1.3+ |
| 1 |Versão 1.0+ |

## <a name="guest-os-release-information"></a>Informações de versão do SO convidado
Existem três datas que são importantes para as versões de SO Convidado: data de **lançamento**, data de **desabilitação** e data de **validade**. Um SO convidado será considerado disponível quando estiver no Portal e puder ser selecionado como o SO convidado de destino. Quando um SO Convidado chega à data de **desabilitação** , ele é removido do Azure. No entanto, qualquer Serviço de nuvem que tiver o SO convidado como algo ainda funcionará normalmente. 

A janela entre a data de **desabilitação** e a de **validade** fornece um intervalo para que você faça a transição facilmente de um SO Convidado para um mais recente. Se você estiver usando *automático* como o SO Convidado, sempre estará na versão mais recente e não precisará se preocupar com sua validade. 

Quando a data de **validade** vence, qualquer Serviço de Nuvem ainda usando o SO Convidado será interrompido, excluído ou forçado a atualizar. Você pode ler mais sobre a política de desativação [aqui][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Família do SO convidado, explicação de versão
As famílias de SO convidado são baseadas em versões lançadas do Microsoft Windows Server. O SO convidado é o sistema operacional subjacente sobre o qual os serviços de nuvem do Azure são executados. Cada SO convidado tem uma família, uma versão e um número de lançamento. 

* **Guest OS family**  
  Uma versão do sistema operacional Windows Server na qual o SO Convidado se baseia. Por exemplo, a *família 3* baseia-se no Windows Server 2012.
* **Versão do SO Convidado**  
  Específica de uma imagem da família de SO convidado mais os patches relevantes do [MSRC (Microsoft Security Response Center)][msrc] disponíveis na data em que a nova versão do SO convidado é produzida. Nem todos os patches estarão necessariamente incluídos. 
  
    Os números começam em 0 e são incrementados em 1 cada vez que um novo conjunto de atualizações é adicionado. Os zeros à direita são mostrados apenas se forem importantes. Ou seja, a versão 2.10 é uma versão diferente e muito posterior à versão 2.1.
* **Versão do SO convidado**  
  Um relançamento de uma versão do SO Convidado. Um relançamento ocorre quando a Microsoft encontra, durante os testes, problemas que exigem alterações. A versão mais recente sempre substitui quaisquer lançamentos anteriores, públicos ou não. O portal clássico do Azure só permitirá que os usuários escolham o lançamento mais recente de uma determinada versão. Implantações executadas em uma versão anterior geralmente não sofrem atualização forçada, o que depende da gravidade do bug. 

No exemplo abaixo, 2 é a família, 12 é a versão e "rel2" é o lançamento.

**Versão de SO convidado** - 2.12 rel2

**Cadeia de caracteres de configuração para essa versão** - WA-GUEST-OS-2.12_201208-02

A cadeia de caracteres de configuração para um SO convidado tem inseridas nela essas mesmas informações, junto com uma data mostrando quais patches MSRC foram considerados para esse lançamento. Neste exemplo, os patches do MSRC gerados para Windows Server 2008 R2 até (e incluindo) agosto de 2012 foram considerados para inclusão. Apenas os patches que se aplicam especificamente a essa versão do Windows Server são incluídos. Por exemplo, se um patch de MSRC se aplica ao Microsoft Office, ele não será incluído porque esse produto não faz parte da imagem base do Windows Server. 

## <a name="guest-os-system-update-process"></a>Processo de atualização de sistema do SO convidado
Esta página contém informações sobre as próximas versões do SO convidado. Os clientes indicaram que desejam saber quando um lançamento ocorre, porque suas funções de serviço de nuvem reinicializarão se elas estiverem definidas para atualização "Automática". Lançamentos de SO convidado normalmente ocorrem pelo menos 5 dias após o lançamento da atualização do MSRC, que por sua vez ocorre na segunda terça-feira de cada mês. As novas versões incluem todos os patches do MSRC relevantes para cada família de SOs convidados. 

O Microsoft Azure está constantemente lançando atualizações. O SO convidado é apenas uma atualização desse tipo no pipeline. Uma versão pode ser afetada por um número muito grande de fatores para que se possa listá-los aqui. Além disso, o Azure é executado em literalmente centenas de milhares de computadores. Isso significa que é impossível fornecer uma data e hora exatas em que a função (ou funções) será reinicializada. Atualizaremos o [RSS feed de atualização do SO convidado][rss] com as informações mais recentes que tivermos, mas considere esse horário como uma janela aproximada. Estamos cientes de que isso causa problemas para clientes e estamos trabalhando em um plano limitar ou programar as reinicializações. 

Quando um novo lançamento do SO convidado é realizado, pode levar tempo para que ele se propague totalmente pelo Azure. Como os serviços são atualizados para o novo SO convidado, eles são reinicializados respeitando os domínios de atualização. Os serviços nos quais está definido o uso de atualizações "Automáticas" receberão um lançamento primeiro. Após a atualização, você verá a nova versão do SO convidado listada para seu serviço no portal clássico do Azure. Relançamentos podem ocorrer durante esse período. Algumas versões podem ser implantadas por longos períodos de tempo e reinicializações de atualização automática podem não ocorrer por muitas semanas após a data de lançamento oficial. A partir do momento em que um SO convidado está disponível você pode escolher explicitamente essa versão a partir do portal ou em seu arquivo de configuração. 

Para uma grande variedade de informações valiosas sobre reinicializações e indicações para mais detalhes técnicos sobre atualizações de SO de host e convidados, consulte a postagem no blog do MSDN intitulada [Instância de Função reinicia devido a atualizações do SO][restarts].

Se você atualizar manualmente o SO convidado, leia [Política de desativação do SO convidado][retirepolicy].

## <a name="guest-os-supportability-and-retirement-policy"></a>Política de suporte e desativação do SO convidado
A política de suporte e desativação do SO convidado é explicada [aqui][retirepolicy].

[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/en-us/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure.md
[rss]: http://sxp.microsoft.com/feeds/3.0/msdntn/WindowsAzureOSUpdates
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: http://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: http://azure.microsoft.com/support/options/
[net install pkg]: http://www.microsoft.com/download/details.aspx?id=42643
[msrc]: http://www.microsoft.com/security/msrc/default.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md


