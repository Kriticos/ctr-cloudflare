# ctr-cloudflare

Container responsável por executar o Cloudflare Tunnel (cloudflared), permitindo a exposicao segura de servicos internos sem necessidade de abertura de portas no firewall. Utilizado para publicar aplicacoes internas (como Zabbix, Grafana e Intranet) atraves da infraestrutura da Cloudflare, com autenticacao, TLS e controle de acesso centralizados.

## Visao geral
Este projeto sobe um container com o `cloudflared` usando Docker Compose. O tunnel eh inicializado via `tunnel run` e recebe o token pelo arquivo `.env`. A rede `network-share` eh externa e permite expor servicos internos sem alterar o firewall.

## Requisitos
- Docker Engine + Docker Compose
- Rede Docker externa `network-share` criada previamente
- Token de Tunnel ativo no Cloudflare

## Estrutura
- `docker-compose.yml`: definicao do servico e rede
- `.env`: variaveis de ambiente (nao versionado)

## Configuracao
1) Crie/ajuste o arquivo `.env` com as variaveis abaixo.
2) Garanta que a rede `network-share` existe.

### Variaveis de ambiente
Exemplo de `.env`:

```env
RELEASE=cloudflare/cloudflared:latest
CONTAINER_NAME=ctr-cloudflare
TUNNEL_TOKEN=seu_token_aqui
IPV4_CLOUDFLARE=172.20.0.10
SUBNET=172.20.0.0/24
```

Descricao:
- `RELEASE`: imagem do `cloudflared` a ser utilizada
- `CONTAINER_NAME`: nome do container
- `TUNNEL_TOKEN`: token do tunnel do Cloudflare
- `IPV4_CLOUDFLARE`: IP fixo na rede externa
- `SUBNET`: subnet da rede externa

## Uso
Subir o servico:

```bash
docker compose up -d
```

Verificar logs:

```bash
docker compose logs -f ctr-cloudflare
```

Parar o servico:

```bash
docker compose down
```

## Rede
O compose usa a rede externa `network-share`. Se ela nao existir, crie:

```bash
docker network create --subnet <SUBNET> network-share
```

## Boas praticas
- Nao versionar o `.env`.
- Rotacionar o `TUNNEL_TOKEN` em caso de vazamento.
- Manter a imagem `cloudflared` atualizada.

## Solucao de problemas
- Verifique se o token esta valido no painel Cloudflare.
- Confirme se o IP e a subnet estao livres na rede.
- Cheque conectividade com `docker compose logs -f ctr-cloudflare`.
