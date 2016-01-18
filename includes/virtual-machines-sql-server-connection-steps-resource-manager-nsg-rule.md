### Configurar uma regra de entrada do Grupo de Segurança de Rede para a VM

Se você quiser ser capaz de se conectar ao SQL Server pela Internet, você precisa configurar uma regra de entrada no Grupo de Segurança de Rede para a porta que sua instância do SQL Server está escutando. Por padrão, essa é a porta TCP 1433.

1. No portal, selecione **Máquinas Virtuais** e selecione sua VM do SQL Server.

2. Clique no link **Todas as Configurações**.

3. Expanda **Interfaces de Rede**.

	![interface de rede](./media/virtual-machines-sql-server-connection-steps/rm-network-interface.png)

4. Em seguida, selecione a Interface de rede para sua VM.

4. Clique no link **Grupo de Segurança de Rede**.

	![interface de rede](./media/virtual-machines-sql-server-connection-steps/rm-network-security-group.png)

6. Nas propriedades do Grupo de Segurança de Rede, expanda **Regras de Segurança de Entrada**.

5. Clique no botão **Adicionar**.

6. Forneça um **Nome** de "SQLServerPublicTraffic".

7. Altere **Protocolo** para **TCP**.

8. Especifique um **Intervalo de Porta de Destino** de 1433 (ou a porta que sua instância do SQL Server está escutando).

9. Verifique se **Ação** está definido como **Permitir**. A caixa de diálogo da regra de segurança deve ter aparência semelhante à captura de tela a seguir.

	![grupo de segurança de rede](./media/virtual-machines-sql-server-connection-steps/rm-network-security-rule.png)

9. Clique em **OK** para salvar a regra para a sua VM.

<!---HONumber=AcomDC_0107_2016-->