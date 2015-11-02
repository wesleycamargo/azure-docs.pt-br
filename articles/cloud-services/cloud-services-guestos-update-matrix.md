<properties 
   pageTitle="Saiba mais sobre as versões mais recentes de SO convidado do Azure | Microsoft Azure" 
   description="As últimas notícias de versão e a compatibilidade do SDK para o SO convidado dos serviços de nuvem do Azure." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="yuemlu" 
   manager="markkie" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="10/15/2015"
   ms.author="yuemlu"/>

# Matriz de Compatibilidade de Versões de SOs Convidados e do SDK do Azure
Fornece a você informações atualizadas sobre as versões mais recentes do SO convidado do Azure para serviços de nuvem. Essas informações o ajudarão a planejar seu caminho de atualização antes de um SO convidado ser desabilitado.

> [AZURE.IMPORTANT]Esta página se aplica às funções de trabalho e da Web dos Serviços de Nuvem, que são executados em um SO convidado. Ela não se aplica a Máquinas Virtuais IaaS. Se você configurar suas funções para usar atualizações automáticas de SO convidado, conforme descrito em [Configurações de atualização de SO convidado do Azure][], não é essencial ler esta página.

<!-- -->
<br />

> [AZURE.TIP]Assine o [Feed do RSS de atualização do SO de convidado][rss] para receber as notificações mais recentes sobre todas as alterações do SO convidado. As alterações mencionadas nesse feed serão integradas a esta página aproximadamente toda semana.


## Notícias atualizadas

###### **15 de outubro de 2015**
A distribuição do SO convidado de outubro está começando hoje, 15 de outubro de 2015, e deverá ser lançada em 13 de novembro de 2015.

As versões 4.24, 3.31 e 2.43 do SO convidado foram lançadas em 1 de outubro de 2015.

###### **9 de setembro de 2015**
A distribuição do SO convidado de setembro está começando hoje, 9 de setembro de 2015, e deverá ser lançada em 8 de outubro de 2015.

As versões 4.23, 3.30 e 2.42 do SO convidado foram lançadas em 9 de setembro de 2015.

###### **14 de agosto de 2015**
A distribuição do SO convidado de agosto está começando hoje, 14 de agosto de 2015, e deverá ser lançada em 11 de setembro de 2015.

###### **7 de agosto de 2015**
As versões 4.22, 3.29 e 2.41 do SO convidado foram lançadas em 7 de agosto de 2015.

###### **14 de julho de 2015**
A distribuição do SO convidado de julho está começando hoje, 14 de junho de 2015, e está planejado para ser lançado em 14 de julho de 2015.

SO convidado versões 4.21, 3.28 e 2.40 lançado em 9 de julho de 2015.

###### **15 de junho de 2015**.
A distribuição do SO convidado de junho está começando hoje, 15 de junho de 2015, e está projetado para ser lançado em 9 de julho de 2015.

As versões 4.20, 3.27 e 2.39 do SO convidado foram lançadas em 12 de junho de 2015.

###### **20 de maio de 20 2015**
A distribuição do SO convidado de maio está começando hoje, 20 de maio de 2015, e está projetado para ser lançado em 12 de junho de 2015.

###### **17 de abril de 2015**
As versões 4.19, 3.26 e 2.38 do SO convidado foram lançadas hoje.

Esta versão contém [MS15-034](https://technet.microsoft.com/library/security/MS15-034), um patch **crítico** para servidores HTTP do Windows.

As versões 4.17, 3.24 e 2.36 do SO convidado serão desabilitadas em 17 de maio de 2015.

###### **6 de abril de 2015**
As versões 4.18, 3.25 e 2.37 do SO convidado foram lançadas em 2 de abril de 2015.

As versões 4.16, 3.23 e 2.35 do SO convidado serão desabilitadas em 2 de maio de 2015.


###### **11 de março de 2015**
As versões 4.17, 3.24 e 2.36 do SO convidado foram lançadas em 9 de março de 2015.

As versões 4.15, 3.22 e 2.34 do SO convidado tiveram sua data de desabilitação definida para 9 de abril de 2015.


###### **29 de janeiro de 2015**
As versões 4.14, 4.13, 3.21, 3.20, 2.33 e 2.32 do SO convidado (lançadas em novembro) tiveram suas datas de desabilitação adiadas. A matriz de liberação do SO convidado abaixo foi atualizada.


###### **13 de janeiro de 2015, atualizado em 15 de janeiro de 2015**
O SO convidado de dezembro foi lançado em 14 de janeiro de 2015.


###### **13 de janeiro de 2015**
A implantação do SO convidado de janeiro é projetada para iniciar em 19 de janeiro de 2015 ou após essa data, atualizando os serviços de nuvem com atualização automática em execução. O SO convidado de janeiro será liberado para implantação em algum momento em fevereiro. Ele desabilitará o SSL v3.0 por padrão, como sugerido na atualização de segurança de janeiro. Este artigo será atualizado quando informações adicionais estiverem disponíveis.

Como [anunciado anteriormente][ssl3 announcement], a atualização de segurança de janeiro para o SO convidado PaaS desabilitará o suporte a SSL v3.0 por padrão, conforme recomendado pela [Microsoft Security Advisory 3009008][]. Essa alteração ocorrerá adicionalmente a todas as outras atualizações mensais de segurança. Clientes PaaS com atualizações automáticas habilitadas podem validar se há ou não um impacto de compatibilidade do aplicativo quando o SSL v3.0 está desabilitado, executando este [script de correção][ssl3-fixit] que desabilitará proativamente o suporte a SSL v3.0.

###### **16 de dezembro de 2014. Atualizado em 7 de janeiro de 2015**
A edição do SO convidado de dezembro está prevista para começar em 9 de janeiro de 2015 ou após essa data.




## Informações de versão do SO convidado

Esta seção lista as versões de SO convidado com suporte no momento. Versões e famílias de SO convidado têm uma data de lançamento, uma data de desabilitação e uma data de validade. A partir da data de lançamento, uma versão de SO convidado pode ser selecionada manualmente no portal de gerenciamento. Um SO convidado é removido do portal de gerenciamento em sua data de desabilitação ou após essa data. Ele fica então "em transição", mas tem suporte com capacidade limitada de atualizar uma implantação. A data de validade é quando uma versão ou família está agendada para ser completamente removida do sistema do Azure. Os serviços de nuvem ainda em execução em uma versão quando ela expira serão interrompidos, excluídos ou sofrerão atualização forçada para uma versão mais recente, conforme detalhado na [Política de suporte e desativação do SO convidado do Azure][retirepolicy].

A Microsoft dá suporte a pelo menos duas versões recentes de cada família de SO convidado para a qual há suporte. A data de desabilitação de uma versão existente do SO convidado pode ser adiada para garantir que pelo menos duas versões permaneçam habilitadas para a implantação.

> [AZURE.WARNING]A desativação da família 1 do SO convidado começou em 1º de junho de 2013 e está programada para ser concluída em breve. Não crie novas instalações usando essa família do SO convidado, e atualize as instalações antigas que ainda a utilizam. Para obter mais informações, consulte [Informações de desativação da Família 1 do SO convidado do Azure][fam1retire]


### Família do SO convidado, versão e explicação de versão
As famílias de SO convidado são baseadas em versões lançadas do Microsoft Windows Server. O SO convidado é o sistema operacional subjacente sobre o qual os serviços de nuvem do Azure são executados. Cada SO convidado tem uma família, uma versão e um número de lançamento.

A **Família do SO convidado** corresponde a uma versão do sistema operacional Windows Server na qual o SO convidado se baseia. Por exemplo, a família 3 baseia-se no Windows Server 2012.

Uma **"Versão do SO convidado"** é a imagem do sistema operacional da família mais patches do [Microsoft Security Response Center (MSRC)][msrc] relevantes disponíveis na data em que a nova versão do SO convidado é produzida. Nem todos os patches estarão necessariamente incluídos. Os números começam em 0 e são incrementados em 1 cada vez que um novo conjunto de atualizações é adicionado. Os zeros à direita são mostrados apenas se forem importantes. Ou seja, a versão 2.10 é uma versão diferente e muito posterior à versão 2.1.

Uma **"Versão do SO convidado"** refere-se a um relançamento de uma versão de sistema operacional convidado. Um relançamento ocorre quando a Microsoft encontra, durante os testes, problemas que exigem alterações. A versão mais recente sempre substitui quaisquer lançamentos anteriores, públicos ou não. O portal de gerenciamento só permitirá que os usuários escolham o lançamento mais recente de uma determinada versão. Implantações executadas em uma versão anterior geralmente não sofrem atualização forçada, o que depende da gravidade do bug.

No exemplo abaixo, 2 é a família, 12 é a versão e "rel2" é o lançamento.

**Versão de SO convidado** - 2.12 rel2

**Cadeia de caracteres de configuração para esse lançamento** -WA-GUEST-OS-2.12\_201208-02

A cadeia de caracteres de configuração para um SO convidado tem inseridas nela essas mesmas informações, junto com uma data mostrando quais patches MSRC foram considerados para esse lançamento. Neste exemplo, os patches do MSRC gerados para Windows Server 2008 R2 até (e incluindo) agosto de 2012 foram considerados para inclusão. Apenas os patches que se aplicam especificamente a essa versão do Windows Server são incluídos. Por exemplo, se um patch de MSRC se aplica ao Microsoft Office, ele não será incluído porque esse produto não faz parte da imagem base do Windows Server.

## Lançamentos

## Versões da Família 4
**Windows Server 2012 R2**

Dá suporte a .NET 4.0, 4.5, 4.5.1, 4.5.2 (Observação 2)

| Versão do SO convidado | Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| ---------------- | -------------------------- | ---------------------- | ------------ | --- |
| 4\.25 | WA-GUEST-OS-4.25\_201510-01 | Projetado para 13 de novembro de 2015 | Será atualizado quando a versão 4.27 for lançada | TBD |
| 4\.24 | WA-GUEST-OS-4.24\_201509-01 | 1º de outubro de 2015 | Será atualizada quando a 4.26 for lançada | TBD |
| 4\.23 | WA-GUEST-OS-4.23\_201508-02 | 9 de setembro de 2015 | Será atualizada quando a 4.25 for lançada | TBD |
| 4\.22 | WA-GUEST-OS-4.22\_201507-02 | 7 de agosto de 2015 | 1º de novembro de 2015 | TBD |
| 4\.21 | WA-GUEST-OS-4.21\_201506-01 | 9 de julho de 2015 | 9 de outubro de 2015 | TBD |
| 4\.20 | WA-GUEST-OS-4.20\_201505-02 | 12 de Junho de 2015. | 7 de setembro de 2015 | TBD |
| 4\.19 | WA-GUEST-OS-4.19\_201504-01 | 17 de abril de 2015 | 9 de agosto de 2015 | TBD |
| 4\.18 | WA-GUEST-OS-4.18\_201503-01 | 2 de abril de 2015 | 12 de julho de 2015 | 14 de outubro de 2015 |
| 4\.17 | WA-GUEST-OS-4.17\_201502-01 | 9 de março de 2015 | 17 de maio de 2015 | 14 de outubro de 2015 |
| 4\.16 | WA-GUEST-OS-4.16\_201501-01 | 29 de janeiro de 2015 | 2 de maio de 2015 | 14 de outubro de 2015 |
| 4\.15 | WA-GUEST-OS-4.15\_201412-01 | 14 de janeiro de 2015 | 9 de abril de 2015 | 14 de outubro de 2015 |
| 4\.14 | WA-GUEST-OS-4.14\_201411-01 | 11 de novembro de 2014 | 28 de fevereiro de 2015 | 14 de outubro de 2015 |
| 4\.13 | WA-GUEST-OS-4.13\_201410-01 | 3 de novembro de 2014 | 14 de fevereiro de 2015 | 14 de outubro de 2015 |
| 4\.12 (Observação 1) | WA-GUEST-OS-4.12\_201409-02 | 6 de outubro de 2014 | 12 de outubro de 2014 | 23 de março de 2015 |
| 4\.11 (Observação 1) | WA-GUEST-OS-4.11\_201408-02 | 25 de agosto de 2014 | 11 de setembro de 2014 | 23 de março de 2015 |
| 4\.10 | WA-GUEST-OS-4.10\_201407-01 | 18 de julho de 2014 | 1º de dezembro de 2014 | 23 de março de 2015 |
| 4\.9 | WA-GUEST-OS-4.9\_201406-01 | 16 de junho de 2014 | 10 de novembro de 2014 | 23 de março de 2015 |
| 4\.8 | WA-GUEST-OS-4.8\_201405-01 | 1º de junho de 2014 | 1º de agosto de 2014 | 23 de março de 2015 |

## Versões da Família 3

**Windows Server 2012**

Dá suporte a .NET 4.0, 4.5

| Versão do SO convidado | Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| ---------------- | -------------------------- | ---------------------- | ------------ | --- |
| 3\.32 | WA-GUEST-OS-3.32\_201510-01 | Projetado para 13 de novembro de 2015 | Será atualizada quando a versão 3.34 for lançada | TBD |
| 3\.31 | WA-GUEST-OS-3.31\_201509-01 | 1º de outubro de 2015 | Será atualizada quando a 3.33 for lançada | TBD |
| 3\.30 | WA-GUEST-OS-3.30\_201508-02 | 9 de setembro de 2015 | Será atualizada quando a 3.32 for lançada | TBD |
| 3\.29 | WA-GUEST-OS-3.29\_201507-02 | 7 de agosto de 2015 | 1º de novembro de 2015 | TBD |
| 3\.28 | WA-GUEST-OS-3.28\_201506-01 | 9 de julho de 2015 | 9 de outubro de 2015 | TBD |
| 3\.27 | WA-GUEST-OS-3.27\_201505-02 | 12 de Junho de 2015. | 7 de setembro de 2015 | TBD |
| 3\.26 | WA-GUEST-OS-3.26\_201504-01 | 17 de abril de 2015 | 9 de agosto de 2015 | TBD |
| 3\.25 | WA-GUEST-OS-3.25\_201503-01 | 2 de abril de 2015 | 12 de julho de 2015 | 14 de outubro de 2015 |
| 3\.24 | WA-GUEST-OS-3.24\_201502-01 | 9 de março de 2015 | 17 de maio de 2015 | 14 de outubro de 2015 |
| 3\.23 | WA-GUEST-OS-3.23\_201501-01 | 29 de janeiro de 2015 | 2 de maio de 2015 | 14 de outubro de 2015 |
| 3\.22 | WA-GUEST-OS-3.22\_201412-01 | 14 de janeiro de 2015 | 9 de abril de 2015 | 14 de outubro de 2015 |
| 3\.21 | WA-GUEST-OS-3.21\_201411-01 | 11 de novembro de 2014 | 28 de fevereiro de 2015 | 14 de outubro de 2015 |
| 3\.20 | WA-GUEST-OS-3.20\_201410-01 | 3 de novembro de 2014 | 14 de fevereiro de 2015 | 14 de outubro de 2015 |
| 3\.19 (Observação 1) | WA-GUEST-OS-3.19\_201409-02 | 6 de outubro de 2014 | 12 de outubro de 2014 | 23 de março de 2015 |
| 3\.18 (Observação 1) | WA-GUEST-OS-3.18\_201408-02 | 25 de agosto de 2014 | 11 de setembro de 2014 | 23 de março de 2015 |
| 3\.17 | WA-GUEST-OS-3.17\_201407-01 | 18 de julho de 2014 | 1º de dezembro de 2014 | 23 de março de 2015 |
| 3\.16 | WA-GUEST-OS-3.16\_201406-01 | 16 de junho de 2014 | 10 de novembro de 2014 | 23 de março de 2015 |
| 3\.15 | WA-GUEST-OS-3.15\_201405-01 | 1º de junho de 2014 | 1º de agosto de 2014 | 23 de março de 2015 |


## Versões da Família 2

**Windows Server 2008 R2 SP1**

Dá suporte a .NET 3.5, 4.0

| Versão do SO convidado | Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| ---------------- | -------------------------- | ---------------------- | ------------ | --- |
| 2\.44 | WA-GUEST-OS-2.44\_201510-01 | Projetado para 13 de novembro de 2015 | Será atualizada quando a versão 2.46 for lançada | TBD |
| 2\.43 | WA-GUEST-OS-2.43\_201509-01 | 1º de outubro de 2015 | Será atualizada quando a 2.45 for lançada | TBD |
| 2\.42 | WA-GUEST-OS-2.42\_201508-02 | 9 de setembro de 2015 | Será atualizada quando a 2.44 for lançada | TBD |
| 2\.41 | WA-GUEST-OS-2.41\_201507-02 | 7 de agosto de 2015 | 1º de novembro de 2015 | TBD |
| 2\.40 | WA-GUEST-OS-2.40\_201506-01 | 9 de julho de 2015 | 9 de outubro de 2015 | TBD |
| 2\.39 | WA-GUEST-OS-2.39\_201505-02 | 12 de Junho de 2015. | 7 de setembro de 2015 | TBD |
| 2\.38 | WA-GUEST-OS-2.38\_201504-01 | 17 de abril de 2015 | 9 de agosto de 2015 | TBD |
| 2\.37 | WA-GUEST-OS-2.37\_201503-01 | 2 de abril de 2015 | 12 de julho de 2015 | 14 de outubro de 2015 |
| 2\.36 | WA-GUEST-OS-2.36\_201502-01 | 9 de março de 2015 | 17 de maio de 2015 | 14 de outubro de 2015 |
| 2\.35 | WA-GUEST-OS-2.35\_201501-01 | 29 de janeiro de 2015 | 2 de maio de 2015 | 14 de outubro de 2015 |
| 2\.34 | WA-GUEST-OS-2.34\_201412-01 | 14 de janeiro de 2015 | 9 de abril de 2015 | 14 de outubro de 2015 |
| 2\.33 | WA-GUEST-OS-2.33\_201411-01 | 11 de novembro de 2014 | 28 de fevereiro de 2015 | 14 de outubro de 2015 |
| 2\.32 | WA-GUEST-OS-2.32\_201410-01 | 3 de novembro de 2014 | 14 de fevereiro de 2015 | 14 de outubro de 2015 |
| 2\.31 (Observação 1) | WA-GUEST-OS-2.31\_201409-02 | 6 de outubro de 2014 | 12 de outubro de 2014 | 23 de março de 2015 |
| 2\.30 (Observação 1) | WA-GUEST-OS-2.30\_201408-02 | 25 de agosto de 2014 | 11 de setembro de 2014 | 23 de março de 2015 |
| 2\.29 | WA-GUEST-OS-2.29\_201407-01 | 18 de julho de 2014 | 1º de dezembro de 2014 | 23 de março de 2015 |
| 2\.28 | WA-GUEST-OS-2.28\_201406-01 | 16 de junho de 2014 | 10 de novembro de 2014 | 23 de março de 2015 |
| 2\.27 | WA-GUEST-OS-2.27\_201405-01 | 1º de junho de 2014 | 1º de agosto de 2014 | 23 de março de 2015 |




### Versões da Família 1
A **Família 1** foi [descontinuada][fam1retire].

#### Observação 1
Os lançamentos de agosto e setembro de 2014 foram liberados parcialmente devido a problemas encontrados com um [patch de MSRC][MS14-046] durante o período de lançamento. O problema não permite a instalação manual do .NET 3.5.1 nas famílias 3 e 4. O lançamento foi desabilitado como uma precaução para que clientes não possam selecioná-lo manualmente.

#### Observação 2
A partir de 19 de setembro de 2014, o .NET 4.5.2 não foi especificamente testado no SO convidado do Azure. Mas o SO convidado é basicamente equivalente ao Windows Server. As mesmas regras de compatibilidade que se aplicam ao produto do Windows Server, portanto, se aplicam às famílias do SO convidado equivalente. Se você encontrar uma exceção para essa política, entre em contato com o [Suporte do Azure][azuresupport]. A Microsoft fará um esforço comercialmente aceitável para solucionar seu problema. [Pacote de instalação manual para .NET 4.5.2][net install pkg].

## Atualizações do MSRC incluídas no SO convidado
A lista de correções incluídas em cada lançamento mensal de SO convidado está disponível [aqui][patches].

## Suporte a SDK

Esta tabela mostra qual família de SO convidado é compatível com quais versões do SDK do Azure. Para obter mais informações além desta tabela, consulte [Informações de suporte e desativação do SDK do Azure para .NET e APIs][retire policy sdk]. Qualquer informação desta lista tem precedência sobre as informações a seguir.

> [AZURE.IMPORTANT]Para garantir que seu serviço funcione conforme o esperado, implante-o em um lançamento do SO convidado que seja compatível com a versão do SDK do Azure usada para desenvolver o seu serviço. Se você não fizer isso, o serviço implantado pode exibir erros na nuvem que não eram aparentes no ambiente de desenvolvimento.

| Família do SO convidado | Versões do SDK com suporte |
| --------------- | ---------------------- |
| 4 | Versão 2.1 e posteriores |
| 3 | Versão 1.8 e posteriores |
| 2 | Versão 1.3 e posteriores |
| 1 | Versão 1.0 e posteriores |


## Processo de atualização de sistema do SO convidado
Esta página contém informações sobre as próximas versões do SO convidado. Os clientes indicaram que desejam saber quando um lançamento ocorre, porque suas funções de serviço de nuvem reinicializarão se elas estiverem definidas para atualização "Automática". Lançamentos de SO convidado normalmente ocorrem pelo menos 5 dias após o lançamento da atualização do MSRC, que por sua vez ocorre na segunda terça-feira de cada mês. As novas versões incluem todos os patches do MSRC relevantes para cada família de SOs convidados.

O Microsoft Azure está constantemente lançando atualizações. O SO convidado é apenas uma atualização desse tipo no pipeline. Uma versão pode ser afetada por um número muito grande de fatores para que se possa listá-los aqui. Além disso, o Azure é executado em literalmente centenas de milhares de computadores. Isso significa que é impossível fornecer uma data e hora exatas em que a função (ou funções) será reinicializada. Atualizaremos o [Feed RSS de atualização do SO convidado][rss] com as informações mais recentes que tivermos, mas considere esse horário como uma janela aproximada. Estamos cientes de que isso causa problemas para clientes e estamos trabalhando em um plano limitar ou programar as reinicializações.

Quando um novo lançamento do SO convidado é realizado, pode levar tempo para que ele se propague totalmente pelo Azure. Como os serviços são atualizados para o novo SO convidado, eles são reinicializados respeitando os domínios de atualização. Os serviços nos quais está definido o uso de atualizações "Automáticas" receberão um lançamento primeiro. Após a atualização, você verá a nova versão do SO convidado listada para seu serviço no Portal de Gerenciamento do Azure. Relançamentos podem ocorrer durante esse período. Algumas versões podem ser implantadas por longos períodos de tempo e reinicializações de atualização automática podem não ocorrer por muitas semanas após a data de lançamento oficial. A partir do momento em que um SO convidado está disponível você pode escolher explicitamente essa versão a partir do portal ou em seu arquivo de configuração.

Para uma grande variedade de informações valiosas sobre reinicializações e indicações para mais detalhes técnicos sobre atualizações de SO de Host e de Convidado, consulte a postagem no blog MSDN intitulada [Instância de Função reinicia devido a atualizações do SO][restarts].

Se você atualizar manualmente o SO convidado, leia [Política de desativação do SO convidado][retirepolicy].


## Política de suporte e desativação do SO convidado
A política de suporte e desativação do SO convidado é explicada [aqui][retirepolicy].
 
## Arquivo de notícias

###### **11 de novembro de 2014**

A edição de novembro (4.14, 3.21 e 2.33) foi lançada no dia 11 desse mês. Essa atualização foi enviada mais cedo, porque ela inclui a atualização do MSRC [Boletim de segurança Microsoft MS14-066 - crítica][MS14-066]. Suas funções de trabalho e da Web em atualização automática devem reinicializar uma vez ao longo dos próximos dias e receber essa correção.

###### **10 de novembro de 2014**
A data de desabilitação do lançamento de outubro (4.13, 3.20 e 2.32) foi atualizada com base nos comentários dos clientes. A data de desabilitação será sempre pelo menos dois meses posterior à data de lançamento.

###### **4 de novembro de 2014**
A edição de outubro (4.13 3.20 e 2.32) foi lançada em 4 de novembro de 2014. Ela inclui o patch MSRC que causou problemas com as versões de agosto e setembro. Para contornar esse problema, a versão de outubro inclui o .NET 3.5 e 3.5.1 pré-instalado, mas desabilitado. Scripts tentando instalar o .NET 3.5 ou 3.5.1 vão efetivamente reabilitá-lo e retornar "sucesso" como resultado da instalação do .NET, além de evitar o problema de instalação completa criado pelo patch do MSRC.

**20 de outubro de 2014. Atualizado em 4 de novembro de 2014** - o lançamento de setembro (4.12, 3.19, 2.31 e 1.39) parcialmente liberado devido ao mesmo [patch de MSRC MS14-046][MS14-046] causando falhas para aqueles tentando instalar o .NET 3.5 ou 3.5.1 na família 3 ou 4. O .NET 3.5.x NÃO tem suporte oficial por nenhuma das duas famílias, mas a Microsoft está respondendo à alteração no comportamento porque algumas instalações de clientes dependem dele e a alteração ocorreu sem aviso prévio. As datas de desabilitação de SOs convidados anteriores (junho e julho) serão adiadas adequadamente para que pelo menos dois SOs convidados totalmente liberados fiquem disponíveis e com suporte. Uma solução para o problema de instalação do .NET apareceu no lançamento de outubro de 2014.

O lançamento de outubro inclui o .NET 3.5 e 3.5.1 pré-instalados (mas desabilitados) e também inclui o patch de MSRC listado anteriormente. Scripts tentando instalar o .NET 3.5 ou 3.5.1 vão efetivamente reabilitá-lo e retornar "sucesso" como resultado para a instalação do .NET, além de evitar o problema de instalação criado pelo patch de MSRC.

Devido à liberação parcial dos duas últimos lançamentos, as pessoas utilizando atualização automática ou que têm distribuíram novas instalações pode estar usando qualquer um desses lançamentos de SO convidado. A tabela a seguir lista quais lançamentos do SO convidado permitem a instalação do .NET 3.5 ou 3.5.1 nas famílias 3 e 4. No momento, se um lançamento permite a instalação, isso significa que ele NÃO tem o patch de MSRC MS14-046 instalado.

| Versão do SO | Pode instalar o .NET 3.5 | Inclui o patch de MSRC [MS14-046][] |
| --- | --- | --- |
| Todas as versões posteriores do SO convidado | Sim | Sim |
| WA-GUEST-OS-4.13\_201410-01 | Sim | Sim |
| WA-GUEST-OS-4.12\_201409-02 | Não | Sim |
| WA-GUEST-OS-4.12\_201409-01 | Não | Sim |
| WA-GUEST-OS-4.11\_201408-02 | Sim | Não |
| WA-GUEST-OS-4.11\_201408-01 | Não | Sim |
| WA-GUEST-OS-4.10\_201407-01 | Sim | Não |

**20 de outubro de 2014** - Já que as versões de agosto e setembro foram distribuídas apenas parcialmente, observe o seguinte:

1. As alterações na codificação destacadas em Diferenças entre o SO convidado do Azure e padrão do Windows Server não foram distribuídos por todo o Azure. Os clientes que não utilizam as versões de agosto ou setembro receberão essas alterações na versão de outubro. 

2. Os sistemas operacionais convidados de agosto e setembro foram desabilitados no Portal de Gerenciamento. Você não pode escolhê-los manualmente. Isso serve para proteger contra problemas que podem surgir se você selecionar essa versão de SO convidado.

3. As datas desabilitadas algumas versões mais antigas foram ajustadas para frente. Isso é para garantir a disponibilidade contínua no portal e suporte para pelo menos duas versões já liberadas de SO convidado em cada família.

## Arquivo de versões

### Família 4

| Versão do SO convidado | Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| ---------------- | -------------------------- | ------------ | ------------ | --- |
| 4\.7 | WA-GUEST-OS-4.7\_201404-01 | 2 de maio de 2014 | 2 de julho de 2014 | 18 de agosto de 2014 |
| 4\.6 | WA-GUEST-OS-4.6\_201403-01 | 28 de março de 2014 | 9 de junho de 2014 | 18 de agosto de 2014 |
| 4\.5 | WA-GUEST-OS-4.5\_201402-01 | 21 de março de 2014 | 21 de maio de 2014 | 18 de agosto de 2014 |
| 4\.4 | WA-GUEST-OS-4.4\_201401-01 | 8 de fevereiro de 2014 | 8 de abril de 2014 | 14 de maio de 2014 |
| 4\.3 | WA-GUEST-OS-4.3\_201312-01 | 6 de janeiro de 2014 | 6 de março de 2014 | 14 de maio de 2014 |
| 4\.2 | WA-GUEST-OS-4.2\_201311-01 | 12 de dezembro de 2013 | 12 de fevereiro de 2014 | 14 de maio de 2014 |
| 4\.1 | WA-GUEST-OS-4.1\_201310-01 | 29 de outubro de 2013 | N/D | 14 de maio de 2014 |
| 4\.0 versão 3 | WA-GUEST-OS-4.0\_201309-03 | 9 de outubro de 2013. Tornado público em 18 de outubro. | N/D | 14 de maio de 2014 |
 


### Família 3

| Versão do SO convidado | Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| ---------------- | -------------------------- | ------------ | ------------ | --- |
| 3\.14 | WA-GUEST-OS-3.14\_201404-01 | 2 de maio de 2014 | 2 de julho de 2014 | 18 de agosto de 2014 |
| 3\.13 | WA-GUEST-OS-3.13\_201403-01 | 28 de março de 2014 | 9 de junho de 2014 | 18 de agosto de 2014 |
| 3\.12 | WA-GUEST-OS-3.12\_201402-01 | 21 de março de 2014 | 21 de maio de 2014 | 18 de agosto de 2014 |
| 3\.11 | WA-GUEST-OS-3.11\_201401-01 | 8 de fevereiro de 2014 | 8 de abril de 2014 | 14 de maio de 2014 |
| 3\.10 | WA-GUEST-OS-3.10\_201312-01 | 6 de janeiro de 2014 | 6 de março de 2014 | 14 de maio de 2014 |
| 3\.9 | WA-GUEST-OS-3.9\_201311-01 | 12 de dezembro de 2013 | 12 de fevereiro de 2014 | 14 de maio de 2014 |
| 3\.8 | WA-GUEST-OS-3.8\_201310-01 | 29 de outubro de 2013 | N/D | 14 de maio de 2014 |
| 3\.7 versão 3 | WA-GUEST-OS-3.7\_201309-03 | 9 de outubro de 2013 | N/D | 14 de maio de 2014 |
| 3\.7 versão 1 | WA-GUEST-OS-3.7\_201309-01 | 23 de setembro de 2013 | N/D | 14 de maio de 2014 |

### Família 2

| Versão do SO convidado | Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| ---------------- | -------------------------- | ------------ | ------------ | --- |
| 2\.26 | WA-GUEST-OS-2.26\_201404-01 | 2 de maio de 2014 | 2 de julho de 2014 | 18 de agosto de 2014 |
| 2\.25 | WA-GUEST-OS-2.25\_201403-01 | 28 de março de 2014 | 9 de junho de 2014 | 18 de agosto de 2014 |
| 2\.24 | WA-GUEST-OS-2.24\_201402-01 | 21 de março de 2014 | 21 de maio de 2014 | 18 de agosto de 2014 |
| 2\.23 | WA-GUEST-OS-2.23\_201401-01 | 8 de fevereiro de 2014 | 8 de abril de 2014 | 14 de maio de 2014 |
| 2\.22 | WA-GUEST-OS-2.22\_201312-01 | 6 de janeiro de 2014 | 6 de março de 2014 | 14 de maio de 2014 |
| 2\.21 | WA-GUEST-OS-2.21\_201311-01 | 12 de dezembro de 2013 | 12 de fevereiro de 2014 | 14 de maio de 2014 |
| 2\.20 | WA-GUEST-OS-2.20\_201310-01 | 29 de outubro de 2013 | N/D | 14 de maio de 2014 |
| 2\.19 versão 3 | WA-GUEST-OS-2.19\_201309-03 | 9 de outubro de 2013 | N/D | 14 de maio de 2014 |
| 2\.19 versão 1 | WA-GUEST-OS-2.19\_201309-01 | 23 de setembro de 2013 | N/D | 14 de maio de 2014 |


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
 

<!---HONumber=Oct15_HO4-->