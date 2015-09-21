# IP interno estático

Suas máquinas virtuais (VMs) de IaaS e instâncias de função de PaaS em uma rede virtual recebem automaticamente um endereço IP interno de um intervalo que você especificar. Esse endereço é mantido pelas VMs e instâncias de função, até que elas sejam desativadas. Você pode desativar uma VM ou instância de função interrompendo-a no PowerShell, na CLI do Azure ou no portal do Azure. Nesses casos, depois que a instância de função ou a VM for iniciada novamente, ela receberá um endereço IP disponível da infraestrutura do Azure, que pode não ser o mesmo que ela tinha anteriormente. Em alguns casos, você não deseja que uma VM ou instância de função contenha um endereço IP dinâmico; por exemplo, se a sua VM executará um DNS ou um controlador de domínio.

>[AZURE.NOTE]Um endereço IP interno dinâmico permanece com a VM se você desligar a VM do sistema operacional convidado. As VMs são desativadas somente quando interrompidas do portal do Azure, PowerShell ou CLI do Azure.

<!---HONumber=Sept15_HO2-->