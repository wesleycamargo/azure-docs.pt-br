1. Navegue de volta ao Gerenciador de Cluster de Failover.  Expanda **funções** e realce seu grupo de disponibilidade.  Na guia **recursos** , clique com o botão direito do mouse no nome do ouvinte e clique em Propriedades.
2. Selecione a guia **Dependências** . Se houver vários recursos listados, verifique se os endereços IP têm dependências OR, e não AND.  Clique em **OK**.
3. Clique com o botão direito do mouse no nome do ouvinte e clique em **Trazer online**.
4. Quando o ouvinte estiver online, na guia **Recursos**, clique com o botão direito do mouse no grupo de disponibilidade e clique em **propriedades**.
   
    ![Configurar o recurso de grupo de disponibilidade](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)
5. Crie uma dependência no recurso de nome de ouvinte (não o nome de recursos de endereço IP). Clique em **OK**.
   
    ![Adicionar dependência no nome do ouvinte](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)
6. Inicie o **SQL Server Management Studio** e conecte-se à réplica principal.
7. Navegue até **Alta Disponibilidade do AlwaysOn** | **Grupos de Disponibilidade** | **<AvailabilityGroupName>** | **Ouvintes do Grupo de Disponibilidade**. 
8. Agora você deve ver o nome do ouvinte que você criou no Gerenciador de Cluster de Failover. Clique com o botão direito do mouse no nome do ouvinte e clique em **Propriedades**.
9. Na caixa **Porta**, especifique o número da porta para o ouvinte do grupo de disponibilidade usando o $EndpointPort usado anteriormente (neste tutorial, 1433 era o padrão) e clique em **OK**.



<!--HONumber=Nov16_HO3-->


