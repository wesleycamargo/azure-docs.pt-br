
#### Para instalar a Atualização 1 do Portal de Gerenciamento do Azure

1. No Portal de Gerenciamento, vá até a página **Dispositivos** e selecione seu dispositivo.
 
2. Navegue até **Dispositivos** > **Configurar**.

3. Em **Interfaces de Rede**, localize a interface de rede que tenha um gateway atribuído. Essa interface de rede será diferente de DATA 0.

4. Limpe a configuração do gateway. Observe que, como as configurações de gateway são necessárias em uma interface de rede habilitada para a nuvem, você precisará desabilitar o acesso à nuvem dessa interface para apagar a configuração.

5. Repita a etapa 4 para qualquer outra interface de rede que tenha um gateway atribuído (exceto DATA 0).

6. Salve a configuração modificada.

7. Agora você pode [usar o Portal de Gerenciamento para instalar a Atualização 1](#use-the-management-portal-to-install-update-1).

<!---HONumber=August15_HO6-->