#### Para criar pontos de extremidade públicos no dispositivo virtual

1. Entre no portal clássico do Azure.

- Clique em **Máquinas Virtuais** e, em seguida, selecione a máquina virtual que está sendo usada como seu dispositivo virtual.

- Clique em **Pontos de Extremidade**. A página **Pontos de extremidade** lista todos os pontos de extremidade para a máquina virtual.

- Clique em **Adicionar**. A caixa de diálogo **Adicionar coadministrador** é exibida. Clique na seta para continuar.

- Para o **nome**, digite o seguinte nome para o ponto de extremidade: **WinRMHttps**.

- Para o **Protocolo**, especifique **TCP**.

- Para a **Porta Pública**, digite os números de porta que você deseja usar para a conexão.

- Para a **Porta Privada**, digite **5986**.

- Clique na marca de seleção para salvar o ponto de extremidade.

Após a criação do ponto de extremidade, você poderá exibir seus detalhes para determinar o endereço IP Virtual Público (VIP). Registre esse endereço.

<!---HONumber=AcomDC_0128_2016-->