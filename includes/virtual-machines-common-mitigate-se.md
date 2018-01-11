
**Última atualização de documento**: 6 de janeiro às 18:30 PST.

A divulgação recente de uma [nova classe de vulnerabilidades de CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) conhecida como ataques de canal lateral de execução especulativa resultou em várias perguntas dos clientes que queriam saber com mais clareza.  

A infraestrutura que executa o Azure e isola as cargas de trabalho do cliente entre elas está protegida.  Isso significa que os outros clientes em execução no Azure não podem atacar o seu aplicativo usando essas vulnerabilidades.

## <a name="keeping-your-operating-systems-up-to-date"></a>Mantenha seus Sistemas Operacionais atualizados

Enquanto uma atualização do sistema operacional não é necessária para isolar os aplicativos em execução no Azure de outros clientes em execução no Azure, manter as versões de sistema operacional atualizadas é sempre uma prática recomendada. 

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
Se você estiver usando o Windows e hospedando código não confiável, também deverá habilitar um recurso do Windows chamado Sombreamento de Endereço Virtual do Kernel (KVA) que fornece proteção adicional contra vulnerabilidades de canal lateral de execução especulativa. Esse recurso está desativado por padrão e, se habilitado, pode afetar o desempenho. Siga as instruções [KB4072698 do Windows Server](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para Habilitar Proteções no Servidor. Se você estiver executando os Serviços de Nuvem do Azure, verifique se está executando o WA-GUEST-OS-5.15_201801-01 ou WA-GUEST-OS-4.50_201801-01 (disponível a partir de 10 de janeiro) e habilite a chave do Registro por meio de uma tarefa de inicialização.


### <a name="linux"></a>Linux
Se você estiver usando o Linux e hospedando código não confiável, também deverá atualizar o Linux para uma versão mais recente que implementa o isolamento de tabela de página de kernel (KPTI) e que separa as tabelas de página usadas pelo kernel daquelas que pertencem ao espaço do usuário. Essas mitigações exigem uma atualização do Sistema Operacional Linux e podem ser obtidas do provedor de distribuição, quando disponível. O seu provedor do Sistema Operacional pode informar se as proteções estão habilitadas ou desabilitadas por padrão.








## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira [Protegendo os clientes do Azure de vulnerabilidades de CPU](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).