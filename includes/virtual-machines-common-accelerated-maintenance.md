

## <a name="what-is-happening"></a>O que está acontecendo?

Uma vulnerabilidade de segurança do setor com base em hardware foi [divulgada em 3 de janeiro](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html). Manter os clientes seguros é sempre nossa prioridade e estamos adotando etapas ativas para garantir que nenhum cliente do Azure seja exposto a essas vulnerabilidades.

Com a divulgação pública da vulnerabilidade de segurança, [aceleramos o cronograma de manutenção planejada](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) e começamos a reinicializar automaticamente as VMs que ainda precisavam da atualização.


## <a name="how-can-i-see-which-of-my-vms-are-already-updated"></a>Como ver quais das minhas VMs já estão atualizadas? 

Você pode ver o status de suas VMs e, se a reinicialização for concluída, poderá consultar o status delas na [Lista de VMs no portal do Azure](https://aka.ms/T08tdc). Suas VMs são listadas como "Já atualizadas", se a atualização tiver sido aplicada, ou "Agendadas" se a atualização ainda for necessária. Se você desejar ver apenas suas VMs "Agendadas", consulte a [Integridade do Serviço do Azure](https://portal.azure.com/).

## <a name="can-i-find-out-exactly-when-my-vms-will-be-rebooted"></a>Posso descobrir exatamente quando minhas VMs serão reinicializadas?

A melhor maneira de obter um alerta sobre a reinicialização é configurar [Eventos Agendados](https://docs.microsoft.com/azure/virtual-machines/windows/scheduled-events). Isso fornece uma notificação de 15 minutos sobre a desativação da VM devido à manutenção.

## <a name="can-i-manually-redeploy-now-to-perform-the-required-maintenance"></a>Posso reimplantar manualmente agora para executar a manutenção necessária? 

Não podemos garantir que uma VM reimplantada será alocada para um host atualizado. Sempre que possível, a malha do Azure tenta alocar VMs para hosts que já estão atualizados. É possível que uma nova implantação de uma VM pare em um host não atualizado, caso em que você pode estar sujeito a uma segunda reinicialização, forçada como parte da manutenção agendada. Como resultado, as reimplantações manuais não são recomendadas como uma solução alternativa.

## <a name="how-long-will-the-reboot-take"></a>Quanto tempo demora a reinicialização? 

A maioria das reinicializações leva aproximadamente **30 minutos**.

## <a name="does-the-guest-os-need-to-be-updated"></a>O sistema operacional convidado precisa ser atualizado? 

Essa atualização de infraestrutura do Azure resolve a vulnerabilidade divulgada no nível do hipervisor e não exige uma atualização nas imagens de VM Windows ou Linux. No entanto, como sempre, você deve continuar aplicando as melhores práticas de segurança para as imagens de VM. Consulte o fornecedor de seus sistemas operacionais para obter atualizações e instruções, conforme necessário. Para clientes de VM do Windows Server, as orientações agora estão publicadas e disponíveis [aqui](../articles/virtual-machines/windows/mitigate-se.md).

## <a name="will-there-be-a-performance-impact-as-a-result-of-resolving-this-update"></a>Resolver essa atualização causará algum impacto no desempenho?

A maioria dos clientes do Azure não percebeu um impacto significativo no desempenho com essa atualização. Trabalhamos para otimizar a CPU e o caminho de E/S de disco e não há impacto perceptível no desempenho após a correção ter sido aplicada. Um conjunto pequeno de clientes pode sofrer algum impacto de desempenho de rede. Isso pode ser resolvido com o uso da Rede Acelerada do Azure, para o [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) ou [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli), uma funcionalidade gratuita disponível para todos os clientes do Azure.

## <a name="i-follow-your-recommendations-for-high-availability-will-my-environment-remain-highly-available-during-the-reboot"></a>Estou seguindo as recomendações para alta disponibilidade. Meu ambiente permanecerá altamente disponível durante a reinicialização?

Sim. As máquinas virtuais implantadas em um conjunto de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais têm o constructo de UD (Domínios de Atualização). Ao fazer a manutenção, o Azure respeita a restrição de UD e não reinicia máquinas virtuais de UDs diferentes (dentro do mesmo conjunto de disponibilidade). Para obter mais informações sobre alta disponibilidade, veja [Gerenciar a disponibilidade de máquinas virtuais Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) ou [Gerenciar a disponibilidade de máquinas virtuais Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

## <a name="i-have-architected-my-business-continuitydisaster-recovery-plan-using-region-pairs-will-reboots-to-my-vms-occur-in-region-pairs-at-the-same-time"></a>Projetei meu plano de recuperação de desastre/continuidade dos negócios usando pares de regiões. Ocorrerão reinicializações de minhas VMs em pares de regiões ao mesmo tempo?

Normalmente, os eventos de manutenção planejada do Azure são distribuídos em regiões emparelhadas uma de cada vez para minimizar o risco de interrupção em ambas as regiões. No entanto, devido à natureza urgente dessa atualização de segurança, estamos distribuindo a atualização para todas as regiões simultaneamente.

## <a name="what-about-paas-services-on-azure"></a>E quanto aos serviços de PaaS no Azure?  

Os serviços de plataforma do Azure, incluindo Web e móvel, serviços de dados, IoT, sem servidor, etc., resolveram a vulnerabilidade. Não é necessária nenhuma ação para clientes que usam esses serviços.

## <a name="intel-released-additional-guidance-on-january-22-2018-related-to-the-security-vulnerabilities--will-this-guidance-cause-any-additional-maintenance-activities-by-azure"></a>A Intel lançou diretrizes adicionais em 22 de janeiro de 2018 relacionadas a vulnerabilidades de segurança.  Estas diretrizes causarão atividades de manutenção adicionais pelo Azure?  

As mitigações do Azure divulgadas anteriormente em 3 de janeiro de 2018 não são afetadas pelas [diretrizes atualizadas](https://newsroom.intel.com/news/root-cause-of-reboot-issue-identified-updated-guidance-for-customers-and-partners/) da Intel. Não haverá nenhuma atividade de manutenção adicional em VMs do cliente como resultado dessas novas informações.
 

## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira [Protegendo os clientes do Azure de vulnerabilidades de CPU](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).
