## Modos do MySQL Enterprise Firewall

### 1. OFF
- **Descrição**: Desabilita o firewall para o usuário especificado.
- **Comportamento**: Quando o firewall está em modo "OFF", nenhuma proteção do firewall é aplicada para o usuário. Todas as consultas SQL que o usuário envia ao servidor são permitidas, sem verificações de segurança.
- **Uso**: Este modo pode ser útil se você quiser desativar temporariamente o firewall para um usuário, talvez para depuração ou testes.

### 2. RECORDING
- **Descrição**: Coloca o firewall em modo de "gravação" para o usuário.
- **Comportamento**: Todas as consultas SQL que o usuário executar são registradas e armazenadas no perfil de segurança do usuário. Essas consultas gravadas servirão como base para definir quais consultas serão permitidas no futuro.
- **Uso**: O modo "RECORDING" é usado quando você está criando o perfil de consultas seguras de um usuário. Ele não bloqueia consultas, apenas registra todas as que o usuário executa.

### 3. PROTECTING
- **Descrição**: Ativa a proteção do firewall para o usuário.
- **Comportamento**: O firewall compara cada consulta SQL executada pelo usuário com as que foram gravadas durante o modo "RECORDING". Se a consulta for diferente das gravadas, ela será bloqueada. Somente consultas que foram previamente gravadas são permitidas.
- **Uso**: Este é o modo ativo de proteção. Após gravar um conjunto de consultas seguras, você pode habilitar o modo "PROTECTING" para garantir que o usuário só possa executar essas consultas registradas.

### 4. RESET
- **Descrição**: Limpa o perfil de firewall do usuário.
- **Comportamento**: Quando esse modo é ativado, todas as consultas previamente gravadas para o usuário são removidas do perfil de firewall. Isso efetivamente reinicia o perfil de segurança do usuário.
- **Uso**: Use este modo se você quiser começar do zero o processo de gravação de consultas seguras para um usuário, removendo todas as consultas previamente gravadas.

***Sintaxe***
Para alterar o modo do firewall para um usuário específico, use a seguinte sintaxe:
```SQL
CALL mysql.sp_set_firewall_mode('user@host', 'MODE');
```

***Ativar o modo RECORDING para o usuário 'app_user':***
```SQL
CALL mysql.sp_set_firewall_mode('app_user@localhost', 'RECORDING');
```

***Ativar o modo PROTECTING para o usuário 'app_user':***
```SQL
CALL mysql.sp_set_firewall_mode('app_user@localhost', 'PROTECTING');
```

***Desativar o firewall para o usuário 'app_user':***
```SQL
CALL mysql.sp_set_firewall_mode('app_user@localhost', 'OFF');
```

***Resetar o perfil do firewall para o usuário 'app_user':***
```SQL
CALL mysql.sp_set_firewall_mode('app_user@localhost', 'RESET');
```
