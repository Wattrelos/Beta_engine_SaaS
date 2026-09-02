---
adr: 8
title: Escolha e Padronização do Servidor Web e Proxy Reverso (NGINX + PHP-FPM) na Beta Engine SaaS
status: Approved
date: 2026-09-02
authors:
  - Antigravity AI
  - Josias
impacted_components:
  - infrastructure: "Web Server & Reverse Proxy Gateway"
  - runtime: "PHP 8.x (PHP-FPM via Unix Socket / FastCGI)"
  - os_platform: "Debian GNU/Linux (Trixie/Bookworm) / Ubuntu LTS"
  - webroot: "public_html/"
  - configuration:
      - "/etc/nginx/nginx.conf"
      - "/etc/nginx/sites-available/beta-engine-saas.conf"
      - "/etc/php/8.x/fpm/pool.d/www.conf"
rules:
  web_server: "NGINX (Open Source)"
  php_execution_mode: "PHP-FPM (FastCGI Process Manager)"
  concurrency_model: "Asynchronous Event-Driven (epoll / non-blocking I/O)"
  routing_strategy: "Centralized in NGINX configuration (try_files $uri $uri/ /index.php?$query_string)"
  htaccess_support: "Explicitly disabled (No dynamic per-directory overrides)"
  static_asset_handling: "Direct NGINX serving with aggressive caching (bypass PHP runtime)"
  security_hardening:
    hidden_files_blocking: "location ~ /\\.(?!well-known).* { deny all; }"
    security_headers: "HSTS, X-Content-Type-Options, X-Frame-Options, Referrer-Policy"
  multitenancy_readiness: "Dynamic subdomains, SSL termination (SNI) and reverse proxy support"
---

# ADR 008: Escolha e Padronização do Servidor Web e Proxy Reverso (NGINX + PHP-FPM) na Beta Engine SaaS

## Status
Aprovado (2026-09-02)

## Contexto

A **Beta Engine SaaS** foi concebida como uma plataforma multi-tenant moderna de alto desempenho desenvolvida em PHP 8.x, estruturada sob os princípios de Clean Architecture, Action-Domain-Responder (ADR) e Domain-Driven Design (DDD). Por se tratar de um modelo de Software as a Service (SaaS), a camada de recepção e despacho de requisições de rede (Web Server / Gateway HTTP) deve atender a rigorosos requisitos não-funcionais:

1. **Alta Concorrência e Baixo Footprint de Memória**: Capacidade de sustentar picos simultâneos de tráfego de múltiplos inquilinos (*tenants*) consumindo o mínimo de memória RAM e CPU, evitando a degradação de recursos em ambientes de VPS e Cloud.
2. **Eficiência na Entrega de Assets Estáticos**: O painel administrativo e a interface pública demandam entrega ultrarrápida de arquivos estáticos (CSS compilado, JS modulares, imagens, fontes web), liberando o motor PHP para processar exclusivamente regras de negócio dinâmicas.
3. **Ausência de Overhead de Disco por `.htaccess`**: Em servidores legados, o interpretador varre a árvore de diretórios a cada requisição HTTP em busca de regras descentralizadas (`.htaccess`), adicionando latência de I/O desnecessária a cada chamada.
4. **Prontidão para Multitenancy e Protocolos Modernos**: Necessidade de suporte nativo e performático a terminação SSL/TLS (SNI), roteamento de subdomínios dinâmicos (*wildcard* / inquilinos isolados), HTTP/2, HTTP/3 (QUIC) e conexões persistentes de longa duração (WebSockets e Server-Sent Events).
5. **Segurança de Borda e Mitigação de Ataques**: Aplicação direta no servidor web de regras de *rate limiting*, bloqueio de arquivos confidenciais (`.env`, `.git`), mitigação de ataques de negação de serviço (DoS) e injeção de cabeçalhos de segurança HTTP globais.

Diante dessas necessidades, fez-se necessária a avaliação e escolha formal do servidor web e gerenciador de processos para a infraestrutura da Beta Engine SaaS.

---

## Comparativo Técnico e Avaliação de Alternativas

| Critério de Avaliação | NGINX + PHP-FPM (Opção Escolhida) | Apache 2.4 (com `mpm_event` + PHP-FPM) | Caddy Server | FrankenPHP / RoadRunner |
|---|---|---|---|---|
| **Modelo de Concorrência** | **Assíncrono / Orientado a Eventos (`epoll`)**: Lida com dezenas de milhares de conexões simultâneas com uso mínimo e estável de RAM. | **Híbrido (Event/Threads)**: Melhorou em relação ao `mpm_prefork`, mas ainda aloca overhead adicional por thread e descritor de conexão. | **Assíncrono (Go Goroutines)**: Excelente modelo concorrente em Go, com consumo moderado de memória. | **App Runner / Worker Mode**: Mantém o PHP residente em memória (estado compartilhado). |
| **Consumo de Memória (RAM)** | **Ultraleve (< 20MB a 50MB)**: O processo mestre e workers do NGINX operam com pegada de memória desprezível. | **Moderado/Alto (150MB a 500MB+)**: A infraestrutura de módulos do Apache carrega overhead significativo. | **Baixo/Moderado (60MB a 120MB)**: Gerenciamento automático de TLS e runtime Go embutido. | **Alto por Worker**: Cada worker PHP residente consome de 30MB a 80MB de RAM continuamente. |
| **Entrega de Assets Estáticos** | **Máxima Eficiência**: Uso nativo da chamada de sistema `sendfile()`, bypass completo do interpretador PHP. | **Média/Boa**: Boa entrega estática, porém sujeita à checagem de diretórios e módulos ativos. | **Excelente**: Servidor HTTP nativo eficiente com compactação automática (Gzip/Zstandard). | **Boa**: Focado prioritariamente na execução de workers dinâmicos. |
| **Configuração e I/O de Disco** | **Centralizada e em Memória**: Regras lidas no boot/reload (`nginx.conf`). Zero I/O de disco por requisição. | **Descentralizada (`.htaccess`)**: Overhead crônico de I/O a cada requisição para buscar arquivos locais de regras. | **Centralizada (Caddyfile)**: Simples e elegante, sem busca em disco. | **Centralizada (Config YAML/CLI)**: Sem arquivos descentralizados. |
| **Maturidade e Adoção Global** | **Padrão de Mercado Indiscutível**: Mais de 30% da web global; suporte unânime em Debian/Ubuntu, Docker, Kubernetes e Cloud. | **Legado Consolidado**: Amplamente suportado, mas perdendo espaço em novas arquiteturas de microsserviços e SaaS. | **Emergente**: Crescendo, porém com menor base de pacotes oficiais corporativos e menor suporte comunitário legado. | **Especializado**: Excelente para APIs de ultrabaixa latência, mas exige refatoração para evitar vazamento de estado (*memory leaks*). |
| **Custo de Licenciamento** | **100% Livre (Open Source - Licença BSD 2-Clause)**. | **100% Livre (Apache 2.0)**. | **Livre (Apache 2.0)**. | **Livre (MIT / Open Source)**. |

---

## Decisão Arquitetural

Decidiu-se pela **adoção do NGINX (Open Source) operando em conjunto com o PHP-FPM (FastCGI Process Manager)** via Unix Domain Socket como o servidor web, proxy reverso e gateway HTTP padrão para a **Beta Engine SaaS**.

### Pilares da Escolha:

1. **Arquitetura Orientada a Eventos e Eficiência sob Concorrência:**
   * O NGINX opera com um modelo assíncrono baseado em *Event Loops* não-bloqueantes (`epoll` no Linux). Ao contrário do Apache histórico (que alocava um processo ou thread pesada por conexão aberta), o NGINX consome uma quantidade previsível e ínfima de memória RAM mesmo sob dezenas de milhares de conexões simultâneas ou em estado *idle* (*keep-alive*). Isso reduz drasticamente os custos operacionais de instâncias VPS e Cloud.

2. **Segregação Estrita de Camadas (Estático vs. Dinâmico):**
   * O NGINX atua como primeira linha de defesa e orquestração. Requisições para arquivos estáticos (CSS compilado, JS, imagens, fontes e mídias da pasta `public_html/`) são entregues diretamente pelo NGINX com suporte a `sendfile`, `tcp_nopush` e cabeçalhos de expiração agressivos (*Cache-Control: immutable*). O interpretador PHP-FPM só é acionado quando a rota de fato exige execução dinâmica de código backend.

3. **Roteamento Centralizado de Alto Desempenho (Sem `.htaccess`):**
   * Como a Beta Engine SaaS é um projeto desenvolvido a partir do zero, foi abolida a dependência do arquivo `.htaccess`. Todo o roteamento é consolidado no bloco do NGINX através da diretiva `try_files $uri $uri/ /index.php?$query_string;`. Isso elimina leituras contínuas de disco e previne brechas de segurança causadas por sobreposições acidentais de permissões em pastas.

4. **Prontidão para Multitenancy, SSL Termination e Protocolos Modernos:**
   * O NGINX é referência em terminação SSL/TLS com Server Name Indication (SNI), permitindo servir múltiplos certificados para múltiplos subdomínios de clientes (*wildcard* ou domínios customizados dos inquilinos). Suporta nativamente multiplexação HTTP/2, HTTP/3 (QUIC), balanceamento de carga e proxy reverso transparente para WebSockets e microsserviços auxiliares.

5. **Hardening de Segurança e Blindagem de Borda:**
   * Implementação simplificada de bloqueio de acesso a arquivos de configuração (`.env`, `.git`, `.yaml`), limitação de taxa de requisições por IP (*rate limiting* para mitigação de força bruta em rotas de autenticação) e injeção padronizada de cabeçalhos de segurança (`X-Frame-Options`, `X-Content-Type-Options`, `Referrer-Policy`, `Content-Security-Policy`).

6. **Licenciamento 100% Livre e Compatibilidade com Debian/Ubuntu:**
   * O NGINX Open Source é regido por licença BSD simplificada, garantindo total liberdade comercial sem custos ocultos de licenciamento.

---

## Diretrizes e Bloco de Configuração de Referência

Abaixo encontra-se a arquitetura de configuração recomendada para o VirtualHost da aplicação (`/etc/nginx/sites-available/beta-engine-saas.conf`):

```nginx
# ==============================================================================
# Beta Engine SaaS - NGINX VirtualHost Configuration (Reference Template)
# ==============================================================================

# Rate Limiting Zone para proteção de rotas de login/checkout
limit_req_zone $binary_remote_addr zone=auth_limit:10m rate=10r/m;

server {
    listen 80;
    listen [::]:80;
    server_name .saas-app.local; # Aceita domínio principal e subdomínios (multitenant)

    # Redirecionamento forçado para HTTPS em ambiente de produção
    # return 301 https://$host$request_uri;

    root /var/www/html/Beta_engine_SaaS/public_html;
    index index.php index.html;

    # Charset padrão e tamanho máximo de upload
    charset utf-8;
    client_max_body_size 64M;

    # Cabeçalhos Globais de Segurança (Hardening)
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;

    # Roteador Principal (Front Controller Pattern para Slim 4 / ADR)
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    # Desativa logs para favicon e robots.txt
    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    # Entrega direta de Assets Estáticos com Cache Agressivo (Offload do PHP)
    location ~* \.(css|js|jpg|jpeg|png|gif|ico|svg|webp|avif|woff|woff2|ttf|otf|eot|mp4|webm)$ {
        expires 30d;
        add_header Cache-Control "public, no-transform, immutable";
        access_log off;
        try_files $uri =404;
    }

    # Bloqueio estrito de arquivos e diretórios ocultos (.env, .git, .htaccess)
    location ~ /\.(?!well-known).* {
        deny all;
        access_log off;
        log_not_found off;
    }

    # Processamento de Requisições Dinâmicas via PHP-FPM
    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/run/php/php8.3-fpm.sock; # Ajustar conforme versão ativa do PHP
        fastcgi_index index.php;
        include fastcgi_params;

        # Parâmetros otimizados do FastCGI
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        fastcgi_param DOCUMENT_ROOT $realpath_root;
        fastcgi_param HTTP_PROXY ""; # Mitigação de vulnerabilidade HTTPOXY
        
        # Buffers e Timeouts FastCGI
        fastcgi_buffer_size 128k;
        fastcgi_buffers 256 16k;
        fastcgi_busy_buffers_size 256k;
        fastcgi_temp_file_write_size 256k;
        fastcgi_read_timeout 60s;
        fastcgi_connect_timeout 10s;
        fastcgi_send_timeout 60s;
        fastcgi_intercept_errors off;
    }
}
```

---

## Consequências

### Positivas (Prós)
* **Máximo Desempenho e TTFB (Time to First Byte) Reduzido**: A resposta para assets estáticos é instantânea e o encaminhamento para o PHP-FPM via socket Unix local elimina latências desnecessárias.
* **Escalabilidade com Custo Mínimo**: Capacidade de processar grandes volumes de requisições concorrentes consumindo uma fração da memória RAM exigida por servidores tradicionais.
* **Desacoplamento e Arquitetura Limpa**: O código backend em PHP fica focado estritamente na lógica de negócio e na camada HTTP (PSR-7/PSR-15), sem regras de infraestrutura espalhadas pelo sistema de arquivos.
* **Facilidade para Ambientes Multi-Tenant e Microsserviços**: Facilidade para configurar roteamento de subdomínios dinâmicos, certificados SSL automáticos (Let's Encrypt / Certbot) e balanceamento de carga entre múltiplos servidores backend quando houver necessidade de expansão horizontal.

### Negativas / Mitigações (Contras)
* **Incompatibilidade com `.htaccess` e Configurações Descentralizadas**:
  * *Impacto*: Desenvolvedores acostumados com o ecossistema Apache não poderão utilizar diretivas de arquivo local para reescrita de URLs ou configuração de cabeçalhos.
  * *Mitigação*: Centralização de todas as regras de roteamento na aplicação (Slim 4) e documentação dos blocos de configuração do NGINX na pasta `docs/architecture/` do repositório.
* **Necessidade de Acesso Root/Sudo para Recarregar Configurações**:
  * *Impacto*: Alterações no arquivo `nginx.conf` ou *VirtualHosts* exigem permissão administrativa para executar `nginx -t` e `systemctl reload nginx`.
  * *Mitigação*: Uso de scripts padronizados de provisionamento e integração na esteira de CI/CD (Forgejo Actions).

---

## Artefatos e Componentes Impactados
* **Documentação Arquitetural**: [`docs/architecture/adr/0008-web_server.md`](file:///var/www/html/Beta_engine_SaaS/docs/architecture/adr/0008-web_server.md)
* **Estrutura de Webroot**: [`public_html/`](file:///var/www/html/Beta_engine_SaaS/public_html/)
* **Ponto de Entrada Front Controller**: [`public_html/index.php`](file:///var/www/html/Beta_engine_SaaS/public_html/index.php)
* **Configuração de Infraestrutura**: `/etc/nginx/sites-available/beta-engine-saas.conf`
* **Pool do Process Manager**: `/etc/php/8.x/fpm/pool.d/www.conf`
