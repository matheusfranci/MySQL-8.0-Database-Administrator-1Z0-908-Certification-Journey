# Use Authentication Plug-ins no MySQL

Os *authentication plug-ins* no MySQL permitem que o sistema utilize diferentes métodos de autenticação para validar os usuários ao acessar o banco de dados. Cada plug-in implementa um mecanismo específico de autenticação, como:

- **MySQL native password**: Padrão no MySQL, utiliza um hash da senha do usuário para validação.
- **SHA-256 password**: Usa SHA-256 para hash de senha e é mais seguro que o método nativo.
- **PAM**: Integra o MySQL com sistemas de autenticação como LDAP ou Kerberos.
- **Caching SHA2 password**: Um dos métodos recomendados por oferecer segurança com cache eficiente para conexões frequentes.

Esses plug-ins permitem flexibilidade, como autenticar usuários em sistemas externos ou utilizar algoritmos mais seguros para o hash de senhas.

## Procedimento para Configurar e Utilizar Authentication Plug-ins

### 1. Verificando os Plug-ins Disponíveis

Você pode listar todos os plug-ins de autenticação disponíveis no MySQL com o comando:

```sql
SHOW PLUGINS;
```
