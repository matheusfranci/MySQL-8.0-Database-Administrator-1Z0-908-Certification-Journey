# Instalação do MySQL Enterprise Edition no Oracle Linux

---

## 1. Download dos Binários
- Acesse o link: [MySQL Enterprise Edition Downloads](https://www.oracle.com/mysql/technologies/mysql-enterprise-edition-downloads.html)

## 2. Copiar o Arquivo para a VM
- Será necessário copiar o arquivo baixado para a VM. Isso pode ser feito via `scp` ou através de uma interface como o MobaXterm ou WinSCP.

```bash
# Exemplo de comando SCP
C:\Users\usuario>scp C:\Users\usuario\Downloads\mysql-enterprise-9.0.1_el7_x86_64_bundle.tar usuario@***.***.*.***:/home/usuario/
# Senha do usuário
usuario@***.***.*.***'s password:
mysql-enterprise-9.0.1_el7_x86_64_bundle.tar                                          100%  954MB  68.2MB/s   00:13
