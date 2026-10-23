# ThinDesk - Microsserviço de Chamados

Microsserviço em Java + Spring Boot + MongoDB para abrir, acompanhar e resolver chamados técnicos.
O login roda em projeto separado; aqui a autenticação é a do Spring Security que já existia no projeto (Basic Auth para a API).

## Como rodar

```
./mvnw spring-boot:run
```

A conexão com o MongoDB fica em `src/main/resources/application.properties`.

## Usuários para teste

| Usuário | Senha | Perfil |
|---|---|---|
| usuario | usuario123 | USUARIO (abre chamados e vê só os seus) |
| tecnico | tecnico123 | TECNICO (vê todos e resolve) |
| admin | thindesk | ADMIN (mesmo acesso do técnico) |

## Endpoints

| Método | Rota | Quem acessa | O que faz |
|---|---|---|---|
| POST | /api/chamados | qualquer autenticado | Abre chamado (título + descrição). Solicitante, data e status ABERTO são automáticos |
| GET | /api/chamados/usuario/{id} | dono do chamado ou técnico/admin | Lista os chamados do usuário |
| GET | /api/chamados/{id} | dono do chamado ou técnico/admin | Detalhes e status |
| PUT | /api/chamados/{id}/resolver | técnico/admin | Registra a solução, status RESOLVIDO e data de resolução |
| GET | /api/chamados | técnico/admin | Lista todos |

Exemplo de corpo para abrir chamado:

```json
{ "titulo": "Impressora parada", "descricao": "A impressora do financeiro não imprime", "tipo": "Hardware" }
```

Exemplo para resolver:

```json
{ "solucao": "Troca do cartucho e reinício do spooler" }
```

## Regras de negócio

- título e descrição são obrigatórios (400 se faltar)
- chamado inexistente retorna 404
- chamado já RESOLVIDO não pode ser resolvido de novo (409)
- usuário comum só consulta os próprios chamados (403)
- só técnico/admin resolve chamado (403)
- sem autenticação a API retorna 401

## Postman

Importe `ThinDesk-Chamados.postman_collection.json` e rode as requisições na ordem.
Depois de abrir o chamado, copie o `id` retornado para a variável `chamadoId` da coleção.
