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

-- Após a criação de uma role é necessária a atiavação da mesma
SET ROLE 'admin_role';

-- Recarregar os privilégios
FLUSH PRIVILEGES;

-- Revogar permissão de SELECT
REVOKE SELECT ON nome_do_banco.nome_da_tabela FROM 'novo_usuario'@'localhost';

-- Revogar um papel de um usuário
REVOKE 'leitor' FROM 'novo_usuario'@'localhost';

-- Visualizar Permissões
SHOW GRANTS FOR 'novo_usuario'@'localhost';

-- Excluir usuário
DROP USER 'novo_usuario'@'localhost';
```
