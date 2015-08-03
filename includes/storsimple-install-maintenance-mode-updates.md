
#### Para instalar atualizações do modo de Manutenção por meio do Windows PowerShell para StorSimple

1. Se você ainda não fez isso, acesse o console serial do dispositivo e selecione a opção 1, **Efetuar login com acesso total**. 

2. Digite a senha. A senha padrão é **Senha1**.

3. No prompt de comando, digite:

     `Get-HcsUpdateAvailability`
    
4. Você será notificado se houver atualizações disponíveis e se as atualizações são interruptivas ou não interruptivas. Para aplicar atualizações sem interrupção, você precisa colocar o dispositivo no modo de Manutenção. Confira [Entrar no modo de manutenção](#enter-maintenance-mode) para obter instruções.

5. Quando o dispositivo estiver em modo de Manutenção, no prompt de comando, digite: `Start-HcsUpdate`

6. Será solicitada a sua confirmação. Depois de confirmar as atualizações, elas serão instalados no controlador que está sendo atualmente acessado por você. Depois que as atualizações forem instaladas, o controlador será reiniciado.

7. Monitore o status das atualizações. Digite:

	`Get-HcsUpdateStatus`
	
	Se `RunInProgress` for `True`, a atualização ainda está em andamento. Se `RunInProgress` for `False`, isso indica que a atualização foi concluída.

7. Quando a atualização estiver instalada no controlador atual e este tiver sido reiniciado, conecte-se ao outro controlador e execute as etapas de 1 a 6.

8. Depois que ambos os controladores estiverem atualizados, saia do modo de Manutenção. Confira [Sair do modo de manutenção](#exit-maintenance-mode) para obter instruções.

<!---HONumber=July15_HO4-->