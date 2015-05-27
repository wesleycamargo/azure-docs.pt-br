
Em seguida, você precisa alterar a maneira que as notificações por push são registradas para que o usuário seja autenticado antes da tentativa do registro.

1. No **QSAppDelegate.m**, remova toda a implementação de **didFinishLaunchingWithOptions**.

2. Abra o **qstodolistviewcontroller.m** e adicione o seguinte código ao final do método **viewDidLoad**:

```
// Register for remote notifications
[[UIApplication sharedApplication] registerForRemoteNotificationTypes:
UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
```

<!--HONumber=54-->