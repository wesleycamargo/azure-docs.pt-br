---
title: Teste de Caneta | Microsoft Docs
description: "O artigo fornece uma visão geral do processo (teste) dos testes de penetração e de como executar o teste de caneta em seus aplicativos em execução na infraestrutura do Azure."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: bc8ca9bb349f41a6ae7371b4fc33cc09fb179c11
ms.contentlocale: pt-br
ms.lasthandoff: 07/28/2017

---
# <a name="pen-testing"></a>Teste de Caneta
Uma das grandes vantagens sobre o uso do Microsoft Azure para implantação e teste de aplicativos é que não é necessário montar uma infraestrutura local para desenvolver, testar e implantar seus aplicativos. Todos os cuidados com a infraestrutura são tomados pelos serviços da plataforma Microsoft Azure. Você não precisa se preocupar sobre requisição, aquisição e "posicionamento no rack e empilhamento" do seu próprio hardware local.

Isso é ótimo, mas você ainda precisará certificar-se de executar a inspeção de segurança normal. Uma das coisas que você precisa fazer é o teste de penetração para os aplicativos que implantar no Azure.

Talvez você já saiba que a Microsoft realiza [teste de penetração do nosso ambiente do Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Isso nos ajuda a melhorar nossa plataforma e guia as nossas ações em termos de melhorar os controles de segurança, introduzindo novos controles de segurança e melhorando nossos processos de segurança.

Nós não fazemos teste de caneta em seu aplicativo para você, mas entendemos que você desejará e precisará executar testes de caneta em seus próprios aplicativos. Isso é uma boa coisa, porque quando você aumenta a segurança de seus aplicativos, você ajuda a tornar todo o ecossistema do Azure mais seguro.

Quando você faz o teste de caneta em seus aplicativos, ele pode parecer um ataque para nós. Nós [monitoramos continuamente](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) em busca de padrões de ataque e iniciaremos um processo de resposta a incidente, se necessário. Não será bom para você e nem para nós se dispararmos uma resposta a incidente devido ao seu próprio teste de caneta de inspeção.

O que fazer?

Quando estiver pronto para fazer o teste de caneta em seus aplicativos hospedados no Azure, você deverá [nos informar](https://portal.msrc.microsoft.com/en-us/engage/pentest). Uma vez que soubermos que você executará testes específicos, nós não desligaremos seu acesso inadvertidamente (por exemplo, bloqueando o endereço IP do qual você está testando), desde que seus testes estejam em conformidade com os termos e condições de teste de caneta do Azure descritos nas [Regras de Engajamento do Teste de Penetração Unificada do Microsoft Cloud](https://technet.microsoft.com/en-us/mt784683).
Os testes padrão que você pode executar incluem:

* Testes em seus pontos de extremidade para revelar as [10 maiores vulnerabilidades do OWASP (Projeto de Segurança de Aplicativo Web Aberto)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Teste de fuzzing](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) de seus pontos de extremidade
* [Exame de portas](https://en.wikipedia.org/wiki/Port_scanner) de seus pontos de extremidade

Um tipo de teste que você não pode executar é qualquer tipo de ataque [DoS (negação de serviço)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Isso inclui iniciar um ataque DoS em si ou a realização de testes relacionados que podem determinar, demonstrar ou simular qualquer tipo de ataque DoS.

Você está pronto para começar com testes de caneta em seus aplicativos hospedados no Microsoft Azure? Se estiver, vá diretamente para a página [Visão Geral do Teste de Penetração](https://security-forms.azure.com/penetration-testing/terms) (e clique no botão Criar uma Solicitação de Teste na parte inferior da página). Você também encontrará mais informações sobre termos e condições para teste de caneta e links úteis sobre como relatar falhas de segurança relacionadas ao Azure ou qualquer outro serviço Microsoft.

