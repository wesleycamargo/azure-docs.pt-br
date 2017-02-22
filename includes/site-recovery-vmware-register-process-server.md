1. Conecte-se para a máquina virtual de servidor de processo usando a Conexão de área de trabalho remota.
2. Uma vez o logon na completa, você vê a ferramenta de configuração do servidor de processo é iniciada automaticamente e requer que você insira o seguinte
  * Nome totalmente qualificado (FQDN) ou endereço IP do servidor de configuração
  * Porta na qual o servidor de configuração está escutando. O valor deve ser 443
  * Senha de Conexão para se conectar ao servidor de configuração.
  * Porta de transferência de dados a ser configurado para esse servidor de processo. Deixe o valor padrão como está a menos que você alterou-lo para um número de porta diferente em seu ambiente.

    ![Registrar o servidor de processo](./media/site-recovery-vmware-register-process-server/register-ps.png)
3. Clique no botão salvar para salvar a configuração.
4. Quando o registro for concluído, o servidor de processo inicia aparecendo em seu servidor de configuração e agora pode ser usado para failback.

> [!TIP]
> O utilitário de configuração do servidor de processo pode ser iniciado clicando duas vezes o **cspsconfigtool** atalho disponível na área de trabalho da máquina virtual.


<!--HONumber=Feb17_HO1-->


