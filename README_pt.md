# ☁️ Nuvem Pessoal com Nextcloud em qualquer máquina

Este projeto configura uma nuvem pessoal segura e eficiente utilizando **Nextcloud**, com suporte a **Redis** e **MariaDB** para melhor desempenho, tudo orquestrado com **Docker Compose**.

> Ideal para Raspberry Pi, mas também funciona em qualquer máquina com Linux ou Windows.

---

## 📦 Serviços incluídos

- **Nextcloud**: Servidor de arquivos com suporte a WebDAV, calendário, contatos e extensões
- **MariaDB**: Banco de dados utilizado pelo Nextcloud
- **Redis**: Cache para otimizar performance e reduzir carga no banco de dados

---

## 🚀 Como usar

### 1. Clone o repositório

```bash
git clone https://github.com/Davisralves/nextCloud_containers.git
cd nextCloud_containers
```

### 2. Configure as variáveis de ambiente

Copie o arquivo de exemplo e personalize as configurações:

**Linux/macOS:**
```bash
cp .env.example .env
```

**Windows:**
```powershell
copy .env.example .env
```

Agora edite o arquivo `.env` criado e altere as senhas padrão:

```env
# Identidade do usuário (use `id` para descobrir)
PUID=1000
PGID=1000

# Fuso horário
TZ=America/Sao_Paulo

# Porta HTTPS do Nextcloud
PORT=8443

# Banco de Dados - ALTERE ESTAS SENHAS!
MYSQL_ROOT_PASSWORD=your_root_password
DATABASE_PASSWORD=your_db_password

# Redis - ALTERE ESTA SENHA!
REDIS_PASSWORD=redis_super_secret_password
```

> ⚠️ **Importante**: Altere todas as senhas (`your_root_password`, `your_db_password`, `redis_super_secret_password`) para valores seguros antes de executar o projeto!

### 3. Execute o Docker Compose

```bash
docker-compose up -d
```

### 4. Aguarde a inicialização

Aguarde alguns minutos para que todos os serviços sejam iniciados. Você pode acompanhar os logs com:

```bash
docker-compose logs -f
```

### 5. Acesse o Nextcloud

Abra seu navegador e acesse:

```
https://localhost:8443
```

> 🔒 **Nota**: Como estamos usando HTTPS com certificado auto-assinado, seu navegador mostrará um aviso de segurança. Clique em "Avançado" e "Prosseguir para localhost".

### 6. Configure o administrador

Na primeira vez que acessar, você será direcionado para a tela de configuração inicial:

1. **Crie uma conta de administrador:**
   - Nome de usuário: `admin` (ou o nome que preferir)
   - Senha: escolha uma senha forte

2. **Configuração do banco de dados:**
   - Tipo: **MariaDB/MySQL**
   - Usuário do banco: `nextcloud`
   - Senha do banco: a mesma definida em `DATABASE_PASSWORD` no arquivo `.env`
   - Nome do banco: `nextcloud_db`
   - Host do banco: `nextcloud_db`

3. Clique em **"Finalizar configuração"**

---

## 🔧 Configurações adicionais

### Configurar Redis para cache

Após a instalação inicial, adicione as seguintes configurações no arquivo de configuração do Nextcloud:

1. Acesse o container do Nextcloud:
```bash
docker exec -it nextcloud bash
```

2. Edite o arquivo de configuração:
```bash
nano /config/www/nextcloud/config/config.php
```

3. Adicione as configurações do Redis:
```php
'memcache.local' => '\OC\Memcache\Redis',
'memcache.distributed' => '\OC\Memcache\Redis',
'memcache.locking' => '\OC\Memcache\Redis',
'redis' => array(
  'host' => 'nextcloud_redis',
  'port' => 6379,
  'password' => 'sua_senha_do_redis', // A mesma do arquivo .env
),
```

### Acessar de outros dispositivos na rede

Para acessar o Nextcloud de outros dispositivos na mesma rede:

1. Descubra o IP da máquina host:
   ```bash
   # Linux/macOS
   ip addr show
   
   # Windows
   ipconfig
   ```

2. Acesse via: `https://IP_DA_MAQUINA:8443`

3. Adicione o IP nas configurações confiáveis do Nextcloud editando o `config.php`:
   ```php
   'trusted_domains' => 
   array (
     0 => 'localhost',
     1 => 'SEU_IP_AQUI',
   ),
   ```

---

## 📱 Aplicativos móveis

- **Android**: [Nextcloud na Play Store](https://play.google.com/store/apps/details?id=com.nextcloud.client)
- **iOS**: [Nextcloud na App Store](https://apps.apple.com/app/nextcloud/id1125420102)

Configure os apps com:
- **Servidor**: `https://SEU_IP:8443`
- **Usuário**: o administrador criado na configuração inicial
- **Senha**: senha do administrador

---

## 🛠️ Comandos úteis

### Verificar status dos containers
```bash
docker-compose ps
```

### Ver logs em tempo real
```bash
docker-compose logs -f
```

### Parar os serviços
```bash
docker-compose down
```

### Fazer backup dos dados
```bash
# Linux/macOS
tar -czf nextcloud-backup-$(date +%Y%m%d).tar.gz /docker-data/nextcloud/

# Windows (PowerShell)
Compress-Archive -Path "C:\docker-data\nextcloud\*" -DestinationPath "nextcloud-backup-$(Get-Date -Format 'yyyyMMdd').zip"
```

### Atualizar os containers
```bash
docker-compose pull
docker-compose up -d
```

---

## 🔍 Solução de problemas

### Container não inicia
- Verifique se as portas não estão em uso: `netstat -tulpn | grep 8443`
- Verifique os logs: `docker-compose logs nextcloud`

### Erro de permissão nos dados
```bash
# Linux/macOS
sudo chown -R 1000:1000 /docker-data/nextcloud/
```

### Esqueci a senha do administrador
1. Acesse o container: `docker exec -it nextcloud bash`
2. Reset da senha: `php /config/www/nextcloud/occ user:resetpassword admin`

---

## 📄 Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.
