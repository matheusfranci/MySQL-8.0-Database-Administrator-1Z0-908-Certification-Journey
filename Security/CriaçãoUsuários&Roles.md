# Criação de usuários e Roles
```sql
-- Criar o usuário
CREATE USER 'admin_user'@'localhost' IDENTIFIED BY 'mypassword';

-- Criar a role
CREATE ROLE 'admin_role';

-- Atribuir permissões à role
GRANT SELECT, INSERT, UPDATE, DELETE ON mydb.* TO 'admin_role';

-- Atribuir a role ao usuário
GRANT 'admin_role' TO 'admin_user'@'localhost';

-- Definir a role como padrão
SET DEFAULT ROLE 'admin_role' TO 'admin_user'@'localhost';

-- Recarregar os privilégios
FLUSH PRIVILEGES;
```
