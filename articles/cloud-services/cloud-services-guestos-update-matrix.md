<properties 
   pageTitle="Saiba mais sobre as versões mais recentes de SO convidado do Azure | Microsoft Azure" 
   description="As últimas notícias de versão e a compatibilidade do SDK para o SO convidado dos serviços de nuvem do Azure." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="yuemlu" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="07/13/2016"
   ms.author="raiye"/>

# Matriz de compatibilidade de versões de SOs Convidados e do SDK do Azure
Fornece a você informações atualizadas sobre as versões mais recentes do SO convidado do Azure para serviços de nuvem. Essas informações o ajudarão a planejar seu caminho de atualização antes de um SO convidado ser desabilitado. Se você configurar suas funções para usar atualizações *automáticas* de SO convidado, conforme descrito em [Configurações de atualização de SO convidado do Azure][], não é essencial ler esta página.

> [AZURE.IMPORTANT] Esta página se aplica às funções de trabalho e da Web dos Serviços de Nuvem, que são executados em um SO convidado. Ela **não se aplica** a Máquinas Virtuais IaaS.

<!-- -->

> [AZURE.TIP] Assine o [Feed do RSS de atualização do SO de convidado][rss] para receber as notificações mais recentes sobre todas as alterações do SO convidado.

Não está certo quanto ao que é o SO convidado ou como as versões do SO convidado funcionam? Leia [esta](#how-it-works) seção.

## Notícias atualizadas
###### **9 de agosto de 2016**
A distribuição do SO convidado de agosto está começando em 9 de agosto de 2016 e deve ser lançada em 8 de setembro de 2016.

###### **13 de julho de 2016**
A distribuição do SO Convidado de julho começa em 13 de julho de 2016 e está planejado para ser lançado em 12 de agosto de 2016.

###### **15 de junho de 2016**
A distribuição do SO convidado de junho começa dia 15 de junho de 2016, e está projetada para ser lançada em 14 de julho de 2016.

###### **17 de maio de 2016**
A distribuição do SO convidado de maio começa a partir de 17 de maio de 2016, e está projetada para ser lançada em 10 de junho de 2016.

###### **18 de abril de 2016**
A distribuição do SO convidado de abril começa em 18 de abril de 2016 e está projetada para ser lançada em 12 de maio de 2016.

###### **14 de março de 2016**
A distribuição do SO convidado de março começa em 14 de março de 2016, e está projetada para ser lançada em 8 de abril de 2016.

###### **22 de fevereiro de 2016**
A distribuição do SO Convidado de fevereiro começa em 22 de fevereiro de 2016 e deve ser lançada em 9 de fevereiro de 2016.

###### **18 de janeiro de 2016**
A distribuição do SO Convidado de janeiro começa em 18 de janeiro de 2016 e deve ser lançada em 12 de fevereiro de 2016.

###### **4 de janeiro de 2016**
O SO convidado 201511-02 de novembro foi lançado em 4 de janeiro de 2016 para implantação. Esta versão do SO não está definida como o SO padrão para a atualização automática. Sendo assim, o tempo de provisionamento da implantação do SO convidado para a versão 201511-02 de novembro seria um pouco maior.

## Lançamentos

## Versões da Família 4
**Windows Server 2012 R2**

É compatível com .NET 4.0, 4.5, 4.5.1, 4.5.2

>[AZURE.NOTE] As datas com * estão sujeitas a alterações

| Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| ------------------------------ | --------------- | ------------- | ---- |
| WA-GUEST-OS-4.35\_201608-01 | 8 de setembro de 2016* | Post 4.37 | TBD |
| WA-GUEST-OS-4.34\_201607-01 | 8 de agosto de 2016 | Post 4.36 | TBD |
| WA-GUEST-OS-4.33\_201606-01 | 13 de julho de 2016 | Post 4.35 | TBD |
| WA-GUEST-OS-4.32\_201605-01 | 10 de junho de 2016 | 8 de setembro de 2016 | TBD |
| WA-GUEST-OS-4.31\_201604-01 | 2 de maio de 2016 | 13 de agosto de 2016 | TBD |
| WA-GUEST-OS-4.30\_201603-01 | 7 de abril de 2016 | 10 de julho de 2016 | TBD |
| WA-GUEST-OS-4.29\_201602-02 | 12 de março de 2016 | 2 de junho de 2016 | TBD |
| WA-GUEST-OS-4.28\_201601-01 | 12 de fevereiro de 2016 | 7 de maio de 2016 | TBD | 
| WA-GUEST-OS-4.27\_201512-01 | 12 de janeiro de 2016 | 12 de abril de 2016 | TBD |
| ~~WA-GUEST-OS-4.26\_201511-02~~ | 4 de janeiro de 2016 | 12 de março de 2016 | TBD |
| ~~WA-GUEST-OS-4.26\_201511-01~~ | 10 de dezembro de 2015 | 12 de março de 2016 | TBD |
| ~~WA-GUEST-OS-4.25\_201510-01~~ | 6 de novembro de 2015 | 12 de fevereiro de 2016 | TBD |
| ~~WA-GUEST-OS-4.24\_201509-01~~ | 1º de outubro de 2015 | 10 de janeiro de 2016 | TBD |
| ~~WA-GUEST-OS-4.23\_201508-02~~ | 9 de setembro de 2015 | 6 de dezembro de 2015 | TBD |
| ~~WA-GUEST-OS-4.22\_201507-02~~ | 7 de agosto de 2015 | 1º de novembro de 2015 | TBD |
| ~~WA-GUEST-OS-4.21\_201506-01~~ | 9 de julho de 2015 | 9 de outubro de 2015 | TBD |
| ~~WA-GUEST-OS-4.20\_201505-02~~ | 12 de Junho de 2015. | 7 de setembro de 2015 | TBD |
| ~~WA-GUEST-OS-4.19\_201504-01~~ | 17 de abril de 2015 | 9 de agosto de 2015 | TBD |

## Versões da Família 3

**Windows Server 2012**

É compatível com .NET 4.0, 4.5, 4.5.1, 4.5.2

>[AZURE.NOTE] As datas com * estão sujeitas a alterações

| Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| ------------------------------ | -------------- | ------------- | --- |
| WA-GUEST-OS-3.42\_201608-01 | 8 de setembro de 2016* | Post 3.44 | TBD |
| WA-GUEST-OS-3.41\_201607-01 | 8 de agosto de 2016 | Post 3.43 | TBD |
| WA-GUEST-OS-3.40\_201606-01 | 13 de julho de 2016 | Post 3.42 | TBD |
| WA-GUEST-OS-3.39\_201605-01 | 10 de junho de 2016 | 8 de setembro de 2016 | TBD |
| WA-GUEST-OS-3.38\_201604-01 | 2 de maio de 2016 | 13 de agosto de 2016 | TBD |
| WA-GUEST-OS-3.37\_201603-01 | 7 de abril de 2016 | 10 de julho de 2016 | TBD |
| WA-GUEST-OS-3.36\_201602-02 | 12 de março de 2016 | 2 de junho de 2016 | TBD |
| WA-GUEST-OS-3.35\_201601-01 | 12 de fevereiro de 2016 | 7 de maio de 2016 | TBD |
| WA-GUEST-OS-3.34\_201512-01 | 12 de janeiro de 2016 | 12 de abril de 2016 | TBD |
| ~~WA-GUEST-OS-3.33\_201511-02~~ | 4 de janeiro de 2016 | 12 de março de 2016 | TBD |
| ~~WA-GUEST-OS-3.33\_201511-01~~ | 10 de dezembro de 2015 | 12 de março de 2016 | TBD |
| ~~WA-GUEST-OS-3.32\_201510-01~~ | 6 de novembro de 2015 | 12 de fevereiro de 2016 | TBD |
| ~~WA-GUEST-OS-3.31\_201509-01~~ | 1º de outubro de 2015 | 10 de janeiro de 2016 | TBD |
| ~~WA-GUEST-OS-3.30\_201508-02~~ | 9 de setembro de 2015 | 6 de dezembro de 2015 | TBD |
| ~~WA-GUEST-OS-3.29\_201507-02~~ | 7 de agosto de 2015 | 1º de novembro de 2015 | TBD |
| ~~WA-GUEST-OS-3.28\_201506-01~~ | 9 de julho de 2015 | 9 de outubro de 2015 | TBD |
| ~~WA-GUEST-OS-3.27\_201505-02~~ | 12 de Junho de 2015. | 7 de setembro de 2015 | TBD |
| ~~WA-GUEST-OS-3.26\_201504-01~~ | 17 de abril de 2015 | 9 de agosto de 2015 | TBD |


## Versões da Família 2

**Windows Server 2008 R2 SP1**

Dá suporte a .NET 3.5, 4.0, 4.5, 4.5.1, 4.5.2

>[AZURE.NOTE] As datas com * estão sujeitas a alterações

| Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| ------------------------------ | ------------- | ------------  | --- |
| WA-GUEST-OS-2.54\_201608-01 | 8 de setembro de 2016* | Post 2.56 | TBD |
| WA-GUEST-OS-2.53\_201607-01 | 8 de agosto de 2016 | Post 2.55 | TBD |
| WA-GUEST-OS-2.52\_201606-01 | 13 de julho de 2016 | Post 2.54 | TBD |
| WA-GUEST-OS-2.51\_201605-01 | 10 de junho de 2016 | 8 de setembro de 2016 | TBD |
| WA-GUEST-OS-2.50\_201604-01 | 2 de maio de 2016 | 13 de agosto de 2016 | TBD |
| WA-GUEST-OS-2.49\_201603-01 | 7 de abril de 2016 | 10 de julho de 2016 | TBD |
| WA-GUEST-OS-2.48\_201602-02 | 12 de março de 2016 | 2 de junho de 2016 | TBD |
| WA-GUEST-OS-2.47\_201601-01 | 12 de fevereiro de 2016 | 7 de maio de 2016 | TBD |
| WA-GUEST-OS-2.46\_201512-01 | 12 de janeiro de 2016 | 12 de abril de 2016 | TBD |
| ~~WA-GUEST-OS-2.45\_201511-02~~ | 4 de janeiro de 2016 | 12 de março de 2016 | TBD |
| ~~WA-GUEST-OS-2.45\_201511-01~~ | 10 de dezembro de 2015 | 12 de março de 2016 | TBD |
| ~~WA-GUEST-OS-2.44\_201510-01~~ | 6 de novembro de 2015 | 12 de fevereiro de 2016 | TBD |
| ~~WA-GUEST-OS-2.43\_201509-01~~ | 1º de outubro de 2015 | 10 de janeiro de 2016 | TBD |
| ~~WA-GUEST-OS-2.42\_201508-02~~ | 9 de setembro de 2015 | 6 de dezembro de 2015 | TBD |
| ~~WA-GUEST-OS-2.41\_201507-02~~ | 7 de agosto de 2015 | 1º de novembro de 2015 | TBD |
| ~~WA-GUEST-OS-2.40\_201506-01~~ | 9 de julho de 2015 | 9 de outubro de 2015 | TBD |
| ~~WA-GUEST-OS-2.39\_201505-02~~ | 12 de Junho de 2015. | 7 de setembro de 2015 | TBD |
| ~~WA-GUEST-OS-2.38\_201504-01~~ | 17 de abril de 2015 | 9 de agosto de 2015 | TBD |

## Atualizações de patch do MSRC
A lista de correções incluídas em cada lançamento mensal de SO convidado está disponível [aqui][patches].

## Suporte a SDK

Embora a [política de desativação do SDK do Azure][retire policy sdk] indique que somente as versões acima da 2.2 têm suporte, as famílias de SO Convidado específicas permitem que você use as versões anteriores. Você sempre deve usar o SDK mais recente com suporte.

| Família do SO convidado | Versões compatíveis do SDK |
| --------------- | ----------------------- |
| 4 | Versão 2.1+ |
| 3 | Versão 1.8+ |
| 2 | Versão 1.3+ |
| 1 | Versão 1.0+ |

## Informações de versão do SO convidado
Existem três datas que são importantes para as versões de SO Convidado: data de **lançamento**, data de **desabilitação** e data de **validade**. Um SO convidado será considerado disponível quando estiver no Portal e puder ser selecionado como o SO convidado de destino. Quando um SO Convidado chega à data de **desabilitação**, ele é removido do Azure. No entanto, qualquer Serviço de nuvem que tiver o SO convidado como algo ainda funcionará normalmente.

A janela entre a data de **desabilitação** e a de **validade** fornece um intervalo para que você faça a transição facilmente de um SO Convidado para um mais recente. Se você estiver usando *automático* como o SO Convidado, sempre estará na versão mais recente e não precisará se preocupar com sua validade.

Quando a data de **validade** vence, qualquer Serviço de Nuvem ainda usando o SO Convidado será interrompido, excluído ou forçado a atualizar. Você pode ler mais sobre a política de desativação [aqui][retirepolicy].

## Família do SO convidado, explicação de versão
As famílias de SO convidado são baseadas em versões lançadas do Microsoft Windows Server. O SO convidado é o sistema operacional subjacente sobre o qual os serviços de nuvem do Azure são executados. Cada SO convidado tem uma família, uma versão e um número de lançamento.

- **Família de SO Convidado** Uma versão do sistema operacional Windows Server na qual o SO Convidado se baseia. Por exemplo, a *família 3* baseia-se no Windows Server 2012.

- **Versão do SO Convidado** Específica da imagem da família de SO Convidado mais os patches relevantes do [MSRC (Microsoft Security Response Center)][msrc] disponíveis na data em que a nova versão do SO Convidado é produzida. Nem todos os patches estarão necessariamente incluídos.

    Os números começam em 0 e são incrementados em 1 cada vez que um novo conjunto de atualizações é adicionado. Os zeros à direita são mostrados apenas se forem importantes. Ou seja, a versão 2.10 é uma versão diferente e muito posterior à versão 2.1.

- **Versão do SO convidado** Um relançamento de uma versão do SO Convidado. Um relançamento ocorre quando a Microsoft encontra, durante os testes, problemas que exigem alterações. A versão mais recente sempre substitui quaisquer lançamentos anteriores, públicos ou não. O portal clássico do Azure só permitirá que os usuários escolham o lançamento mais recente de uma determinada versão. Implantações executadas em uma versão anterior geralmente não sofrem atualização forçada, o que depende da gravidade do bug.

No exemplo abaixo, 2 é a família, 12 é a versão e "rel2" é o lançamento.

**Versão de SO convidado** - 2.12 rel2

**Cadeia de caracteres de configuração para esse lançamento** -WA-GUEST-OS-2.12\_201208-02

A cadeia de caracteres de configuração para um SO convidado tem inseridas nela essas mesmas informações, junto com uma data mostrando quais patches MSRC foram considerados para esse lançamento. Neste exemplo, os patches do MSRC gerados para Windows Server 2008 R2 até (e incluindo) agosto de 2012 foram considerados para inclusão. Apenas os patches que se aplicam especificamente a essa versão do Windows Server são incluídos. Por exemplo, se um patch de MSRC se aplica ao Microsoft Office, ele não será incluído porque esse produto não faz parte da imagem base do Windows Server.

## Processo de atualização de sistema do SO convidado
Esta página contém informações sobre as próximas versões do SO convidado. Os clientes indicaram que desejam saber quando um lançamento ocorre, porque suas funções de serviço de nuvem reinicializarão se elas estiverem definidas para atualização "Automática". Lançamentos de SO convidado normalmente ocorrem pelo menos 5 dias após o lançamento da atualização do MSRC, que por sua vez ocorre na segunda terça-feira de cada mês. As novas versões incluem todos os patches do MSRC relevantes para cada família de SOs convidados.

O Microsoft Azure está constantemente lançando atualizações. O SO convidado é apenas uma atualização desse tipo no pipeline. Uma versão pode ser afetada por um número muito grande de fatores para que se possa listá-los aqui. Além disso, o Azure é executado em literalmente centenas de milhares de computadores. Isso significa que é impossível fornecer uma data e hora exatas em que a função (ou funções) será reinicializada. Atualizaremos o [Feed RSS de atualização do SO convidado][rss] com as informações mais recentes que tivermos, mas considere esse horário como uma janela aproximada. Estamos cientes de que isso causa problemas para clientes e estamos trabalhando em um plano limitar ou programar as reinicializações.

Quando um novo lançamento do SO convidado é realizado, pode levar tempo para que ele se propague totalmente pelo Azure. Como os serviços são atualizados para o novo SO convidado, eles são reinicializados respeitando os domínios de atualização. Os serviços nos quais está definido o uso de atualizações "Automáticas" receberão um lançamento primeiro. Após a atualização, você verá a nova versão do SO convidado listada para seu serviço no portal clássico do Azure. Relançamentos podem ocorrer durante esse período. Algumas versões podem ser implantadas por longos períodos de tempo e reinicializações de atualização automática podem não ocorrer por muitas semanas após a data de lançamento oficial. A partir do momento em que um SO convidado está disponível você pode escolher explicitamente essa versão a partir do portal ou em seu arquivo de configuração.

Para uma grande variedade de informações valiosas sobre reinicializações e indicações para mais detalhes técnicos sobre atualizações de SO de Host e de Convidado, consulte a postagem no blog MSDN intitulada [Instância de Função reinicia devido a atualizações do SO][restarts].

Se você atualizar manualmente o SO convidado, leia [Política de desativação do SO convidado][retirepolicy].


## Política de suporte e desativação do SO convidado
A política de suporte e desativação do SO convidado é explicada [aqui][retirepolicy].

[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/pt-BR/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Configurações de atualização de SO convidado do Azure]: cloud-services-how-to-configure.md
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
 

<!---HONumber=AcomDC_0817_2016-->