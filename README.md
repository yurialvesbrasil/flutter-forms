# Futter - Trabalhando com formulários

Projeto monorepo com dois módulos:

- `app/` (Flutter): frontend mobile/web com gerenciamento de estado e validação de formulários.
- `backend/` (Dart + Shelf): API HTTP com rotas modulares e documentação Swagger.

## Visão Geral

- **Frontend (`app/`)**: Flutter + Modular (roteamento/DI) + Triple (estado/reações) + validações para CPF, e-mail e máscaras brasileiras. Consome um backend HTTP.
- **Backend (`backend/`)**: Shelf + Shelf Modular (rotas/middlewares) + Swagger UI + repositório de clientes (com `PrismaClient` gerado). Servidor HTTP na porta 4444.

## Tecnologias e bibliotecas

### Frontend (app)

- `flutter_modular`: injeção de dependências e roteamento modular (`AppModule`, `ChildRoute`).
- `flutter_triple`: gerenciamento de estado reativo (stores como `client_store.dart`, `details_store.dart`).
- `uno`: cliente HTTP (defina a baseURL conforme seu backend).
- `equatable`: comparação de objetos por valor (VOs e entidades).
- `cpf_cnpj_validator`, `brasil_fields`, `string_validator`: validações e formatações (CPF, máscaras, strings).
- `cupertino_icons`: ícones iOS.

Padrões:

- Módulos e rotas em `app/lib/app/app_module.dart`.
- Widgets de entrada reutilizáveis em `app/lib/app/components/`.
- Entidades/VOs imutáveis em `app/lib/app/entities` e `app/lib/app/vos` (ex.: `cpf.dart`, `email.dart`, `name.dart`).
- Stores com Triple em `app/lib/app/stores`.

### Backend (backend)

- `shelf`: servidor HTTP.
- `shelf_modular`: modularização de rotas e middlewares (`AppModule`).
- `shelf_swagger_ui`: documentação em `/doc/`.
- `orm` e `PrismaClient` (gerado em `lib/src/generated/`): acesso a dados.
- `json_annotation` + `build_runner`/`json_serializable`: (suporte a models JSON quando necessário).

Padrões:

- `lib/src/app_module.dart`: binds e rotas (inclui `ClienteResource`, `swaggerHandler` e rota raiz).
- Middleware JSON adiciona `content-type: text/json; charset=utf-8` quando necessário.
- Binário de inicialização em `bin/backend.dart` (porta 4444).

## Setup

### Pré-requisitos

- Flutter SDK (versão compatível com Dart 2.17.x).
- Dart SDK (para o backend, se não usar o do Flutter).

### Backend

1. Instalar dependências:
   ```bash
   cd backend
   dart pub get
   ```
2. Gerar códigos (se aplicável):
   ```bash
   # opcional, conforme scripts do projeto
   dart run build_runner build --delete-conflicting-outputs
   # ou via Slidy, se instalado
   slidy run generate
   ```
3. Executar servidor:
   ```bash
   dart bin/backend.dart
   # ou
   slidy run server
   ```
4. Swagger:
   - `http://localhost:4444/doc/`

### Frontend

1. Instalar dependências:
   ```bash
   cd app
   flutter pub get
   ```
2. Configurar a URL do backend no cliente HTTP (Uno):
   - Defina a `baseURL` para `http://localhost:4444` (ou a URL do seu ambiente). Procure pelo local onde `Uno` é instanciado (ex.: service/repository) e ajuste a configuração.
3. Rodar o app:
   ```bash
   # dispositivo/emulador
   flutter run
   # web
   flutter run -d chrome
   ```

## Estrutura relevante

- `app/lib/app/app_module.dart`: rotas (`/` -> `ClientPage`, `/details` -> `DetailsPage`).
- `app/lib/app/app_widget.dart`: `MaterialApp.router` com `Modular.routerDelegate`.
- `app/assets/estados-cidades.json`: dados auxiliares (UF/cidades) para formulários.
- `backend/lib/src/app_module.dart`: binds (`PrismaClient`, `ClientRepository`) e rotas.
- `backend/bin/backend.dart`: inicialização do servidor.

## Convenções e boas práticas

- VOs (`cpf.dart`, `email.dart`, `name.dart`, `state.dart`, `city.dart`) encapsulam validações e garantem consistência.
- Stores Triple para efeito/estado/erro, mantendo UI desacoplada da lógica.
- Modularização clara de rotas e injeções em ambos os lados (app/backend).

## Variáveis de ambiente e configurações

- Backend: porta padrão 4444 (ajuste em `bin/backend.dart` se necessário).
- Frontend: configurar base URL do `Uno` conforme ambiente (dev/prod).

## Scripts úteis

```bash
# Backend
cd backend && dart pub get && dart bin/backend.dart

# Frontend
cd app && flutter pub get && flutter run
```

---

Futter - Trabalhando com formulários
