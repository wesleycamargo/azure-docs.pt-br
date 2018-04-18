---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/03/2018
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 81357bce92bb8bd2f77f7aaabc8e3b1d49047a1b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
**Última atualização do documento**: 3 de abril, 15h PST.

A divulgação recente de uma [nova classe de vulnerabilidades de CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) conhecida como ataques de canal lateral de execução especulativa resultou em várias perguntas dos clientes que queriam saber com mais clareza.  

A Microsoft implantou atenuações em todos os nossos serviços de nuvem. A infraestrutura que executa o Azure e isola as cargas de trabalho do cliente entre elas está protegida.  Isso significa que outros clientes em execução no Azure não podem atacar seu aplicativo usando essas vulnerabilidades.

Além disso, o Azure está expandindo o uso de [preservação da manutenção da memória](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#memory-preserving-maintenance) sempre que possível, pausando a VM por até 30 segundos, enquanto o host é atualizado ou a VM é movida para um host já atualizado.  A preservação da manutenção da memória também minimiza o impacto para o cliente e elimina a necessidade de reinicializações.  O Azure utilizará esses métodos ao fazer atualizações de todo o sistema para o host.

> [!NOTE] 
> No final de fevereiro de 2018, a Intel Corporation publicou [Diretrizes de Revisão de Microcódigo](https://newsroom.intel.com/wp-content/uploads/sites/11/2018/03/microcode-update-guidance.pdf) atualizadas sobre o status de suas versões de microcódigo, o que melhora a estabilidade e atenua as vulnerabilidades recentes divulgadas pelo [Projeto Zero da Google](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html). As mitigações implementadas pelo Azure em [3 de janeiro de 2018](https://azure.microsoft.com/en-us/blog/securing-azure-customers-from-cpu-vulnerability/) não são afetadas pela atualização de microcódigo da Intel. A Microsoft já implementou mitigações substanciais para proteger os clientes do Azure contra outras máquinas virtuais do Azure.  
>
> O microcódigo da Intel aborda a variante 2 do Spectre ([CVE-2017-5715](https://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5715) ou injeção de destino de branch) para proteger contra ataques que só são aplicáveis quando você executa cargas de trabalho compartilhadas ou não confiáveis dentro das VMs no Azure. Nossos engenheiros estão testando a estabilidade para minimizar impactos no desempenho do microcódigo, antes de disponibilizá-lo para os clientes do Azure.  Como muito poucos clientes executam cargas de trabalho não confiáveis em suas VMs, a maioria dos clientes não precisará habilitar essa funcionalidade depois de liberada. 
>
> Esta página será atualizada à medida que outras informações estiverem disponíveis.  






## <a name="keeping-your-operating-systems-up-to-date"></a>Mantenha seus Sistemas Operacionais atualizados

Enquanto uma atualização do sistema operacional não é necessária para isolar os aplicativos em execução no Azure de outros clientes em execução no Azure, manter as versões de sistema operacional atualizadas é sempre uma prática recomendada. Os [pacotes cumulativos de atualizações de segurança para o Windows](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) de janeiro de 2018 e posteriores contêm atenuações referentes essas vulnerabilidades.

Nas seguintes ofertas, aqui estão as nossas ações recomendadas para atualizar seu Sistema Operacional: 

<table>
<tr>
<th>Oferta</th> <th>Ação recomendada </th>
</tr>
<tr>
<td>Serviços de nuvem do Azure </td>  <td>Habilite a atualização automática ou verifique se você está executando a versão mais recente do Sistema Operacional Convidado.</td>
</tr>
<tr>
<td>Máquinas Virtuais do Linux do Azure</td> <td>Instalar atualizações do provedor de sistema operacional, quando disponível. </td>
</tr>
<tr>
<td>Máquinas Virtuais do Windows do Azure </td> <td>Verifique se você está executando um aplicativo de antivírus com suporte antes de instalar as atualizações do Sistema Operacional. Contate o fornecedor do software antivírus para obter informações sobre a compatibilidade.<p> Instale o [Rollup de segurança de janeiro](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002). </p></td>
</tr>
<tr>
<td>Outros Serviços de PaaS do Azure</td> <td>Não é necessária nenhuma ação para clientes que usam esses serviços. O Azure mantém automaticamente suas versões de Sistema Operacional atualizadas. </td>
</tr>
</table>

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Orientações adicionais se você estiver executando código não confiável 

Não é necessária nenhuma ação adicional por parte do cliente, a menos que você esteja executando código não confiável. Se você permitir algum código não confiável (por exemplo, você permite que um dos seus clientes carregue um trecho de código ou binário que você, depois, executará na nuvem dentro de seu aplicativo), em seguida, as seguintes etapas adicionais devem ser tomadas.  


### <a name="windows"></a>Windows 
Se você estiver usando o Windows e hospedando código não confiável, também deverá habilitar um recurso do Windows chamado Sombreamento de Endereço Virtual do Kernel (KVA) que fornece proteção adicional contra vulnerabilidades de canal lateral de execução especulativa (especificamente para a variante 3 Meltdown, [CVE-2017-5754](https://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5754), ou carregamento de cache de dados mal-intencionados). Esse recurso está desativado por padrão e, se habilitado, pode afetar o desempenho. Siga as instruções [KB4072698 do Windows Server](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para Habilitar Proteções no Servidor. Caso esteja executando os Serviços de Nuvem do Azure, verifique se está executando WA-GUEST-OS-5.15_201801-01 ou WA-GUEST-OS-4.50_201801-01 (disponível a partir de 10 de janeiro de 2018) e habilite a chave do Registro por meio de uma tarefa de inicialização.


### <a name="linux"></a>Linux
Se você estiver usando o Linux e hospedando código não confiável, também deverá atualizar o Linux para uma versão mais recente que implementa o isolamento de tabela de página de kernel (KPTI) e que separa as tabelas de página usadas pelo kernel daquelas que pertencem ao espaço do usuário. Essas mitigações exigem uma atualização do Sistema Operacional Linux e podem ser obtidas do provedor de distribuição, quando disponível. O seu provedor do Sistema Operacional pode informar se as proteções estão habilitadas ou desabilitadas por padrão.



## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira [Protegendo os clientes do Azure de vulnerabilidades de CPU](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).
