# Swarm-Gotenberg

**Descrição breve**: Uma stack Docker Swarm para o Gotenberg, oferecendo conversão escalável de documentos e URLs para PDF com Chromium e LibreOffice. Configuração simples, segura e otimizada para produção com Traefik.

## Pré-requisitos

Antes de começar, certifique-se de ter:
- Docker instalado e configurado em modo Swarm (`docker swarm init`).
- Uma rede Docker externa chamada `cloud` (crie com `docker network create --driver overlay cloud` se ainda não existir).
- Um volume Docker externo chamado `gotenberg_data` (crie com `docker volume create gotenberg_data` se necessário).
- Traefik configurado como proxy reverso (opcional, mas necessário para as configurações de TLS e roteamento fornecidas).

## Instruções de Uso

Siga os passos abaixo para implantar e usar a stack `swarm-gotenberg`:

### 1. Clone o Repositório
Clone este repositório para sua máquina local:
```bash
git clone https://github.com/Maicon-Bartoski/swarm-gotenberg.git
cd swarm-gotenberg
```

### 2. Configure o Domínio
Edite o arquivo `docker-compose.yml` e substitua `[SEU_DOMINIO]` pelo domínio que você deseja usar:
```yaml
traefik.http.routers.gotenberg.rule=Host(`[SEU_DOMINIO]`)
```
Certifique-se de que o domínio esteja configurado no seu DNS para apontar para o nó Swarm.

### 3. (Opcional) Ajuste Variáveis de Ambiente
Revise as variáveis de ambiente no `docker-compose.yml` e ajuste conforme necessário:
- `API_PORT`: Porta da API (padrão: 3000).
- `CHROMIUM_*`: Configurações para conversão de URLs/HTML.
- `LIBREOFFICE_*`: Configurações para conversão de documentos.
- `LOG_LEVEL`: Nível de logs (ex.: `debug` para mais detalhes).

### 4. Implante a Stack
Execute o comando abaixo para implantar a stack no Docker Swarm:
```bash
docker stack deploy -c docker-compose.yml swarm-gotenberg
```

### 5. Verifique a Implantação
Confirme que o serviço está rodando:
```bash
docker service ls
```
Você verá algo como `swarm-gotenberg_gotenberg` com 1 réplica ativa.

### 6. Acesse o Gotenberg
- Teste o serviço com uma requisição simples, como converter um HTML para PDF:
  ```bash
  curl -X POST https://[SEU_DOMINIO]/forms/chromium/convert/html \
       -F "files=@seu_arquivo.html" \
       --output resultado.pdf
  ```
- Ou converta um documento do LibreOffice:
  ```bash
  curl -X POST https://[SEU_DOMINIO]/forms/libreoffice/convert \
       -F "files=@seu_arquivo.docx" \
       --output resultado.pdf
  ```

### 7. Explore a Documentação
Consulte a [documentação oficial do Gotenberg](https://gotenberg.dev/docs/introduction) para mais endpoints e opções de conversão.

## Notas Adicionais
- **Segurança**: Configure o Traefik corretamente para garantir HTTPS e proteger o acesso.
- **Escalabilidade**: Ajuste `replicas` em `deploy` para escalar o serviço conforme a demanda.
- **Persistência**: O volume `gotenberg_data` armazena dados temporários; ajuste conforme necessário.

## Sobre o Gotenberg
Esta stack utiliza a imagem oficial `gotenberg/gotenberg`. Para mais detalhes, visite [gotenberg/gotenberg](https://github.com/gotenberg/gotenberg).

## Licença
Este repositório é licenciado sob a [MIT License](LICENSE.md), aplicável apenas à stack aqui definida. O Gotenberg possui sua própria licença MIT, mantida pelo projeto original.
