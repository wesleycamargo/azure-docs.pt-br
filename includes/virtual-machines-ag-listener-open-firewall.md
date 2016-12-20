Nesta etapa, você pode criar uma regra de firewall para abrir a porta de investigação para o ponto de extremidade com balanceamento de carga (59999 conforme especificado anteriormente), e outra regra para abrir a porta do ouvinte do grupo de disponibilidade. Como você criou o ponto de extremidade com balanceamento de carga nas VMs do Azure que contêm réplicas do grupo de disponibilidade, você precisa abrir a porta de investigação e a porta do ouvinte nas respectivas máquinas virtuais do Azure.

1. Em máquinas virtuais que hospedam réplicas, inicie o **Firewall do Windows com segurança avançada**.
2. Clique com o botão direito do mouse em **Regras de Entrada** e clique em **Nova Regra**.
3. Na página **Tipo de Regra**, selecione **Porta** e clique em **Avançar**.
4. Na página **Protocolo e Portas**, selecione **TCP** e digite **59999** na caixa **portas locais específicas**. Em seguida, clique em **Avançar**.
5. Na página **Ação**, mantenha selecionado **Permitir a conexão** e clique em **Avançar**.
6. Na página **Perfil**, aceite as configurações padrão e clique em **Avançar**.
7. Na página **Nome**, especifique um nome de regra, como **Porta de investigação do ouvinte AlwaysOn** na caixa de texto **Nome** e clique em **Concluir**.
8. Repita as etapas acima para a porta do ouvinte do grupo de disponibilidade (conforme especificado anteriormente no parâmetro $EndpointPort do script) e especifique um nome de regra apropriado, como **Porta do ouvinte AlwaysOn**.



<!--HONumber=Nov16_HO5-->


