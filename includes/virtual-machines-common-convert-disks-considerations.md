
* A conversão requer uma reinicialização da VM, programe então a migração de suas VMs durante uma janela de manutenção já existente. 

* A conversão não é reversível. 

* Não deixe de testar a conversão. Migre uma máquina virtual de teste antes de executar a migração na produção.

* Durante a conversão, você pode desalocar a VM. A VM recebe um endereço IP novo quando é iniciada após a conversão. Se necessário, você pode [atribuir um endereço IP estático](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) à VM.

* Os VHDs originais e a conta de armazenamento usados pela VM antes da conversão não são excluídos. Eles continuam a incorrer em encargos. Para evitar ser cobrado por esses artefatos, exclua os blobs VHD originais depois de verificar que a conversão foi concluída.

* Examine a versão mínima do agente de VM do Azure necessária para oferecer suporte ao processo de conversão. Para saber mais sobre como verificar e atualizar a versão do seu agente, confira [Suporte de versão mínima para agentes de VM no Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)