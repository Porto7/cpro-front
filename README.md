# 📋 Documentação Completa do Frontend - Sistema de Checkout e E-learning

## 🏗️ Arquitetura Geral

### Stack Tecnológica
- **Framework**: React 18 com Vite
- **Roteamento**: React Router v6
- **UI Components**: Radix UI + Tailwind CSS
- **Notificações**: Sonner + Sistema customizado
- **Animações**: Framer Motion
- **Estado**: Context API + Hooks
- **Backend**: Node.js (localhost:5000)

### Estrutura de Pastas
```
src/
├── components/          # Componentes reutilizáveis
│   ├── ui/             # Componentes de UI base
│   ├── layout/         # Layouts (SellerLayout, CustomerLayout)
│   ├── auth/           # Componentes de autenticação
│   ├── checkout/       # Componentes de checkout
│   └── ...
├── pages/              # Páginas da aplicação
├── lib/                # Bibliotecas e APIs
├── hooks/              # Custom hooks
├── context/            # Contexts do React
├── config/             # Configurações
└── styles/             # Estilos globais
```

## 🌐 Configuração e URLs

### URLs Base
- **Frontend**: `http://localhost:3000`
- **Backend API**: `http://localhost:5000/api`
- **Ambiente**: Development

### Configurações (src/config/development.js)
```javascript
{
  API_BASE_URL: 'http://localhost:5000/api',
  APP_ENV: 'development',
  APP_PORT: 3000,
  BACKEND_PORT: 5000,
  SSL_ENABLED: false,
  EMAIL_API_URL: 'http://localhost:5000/api/email',
  APP_URL: 'http://localhost:3000'
}
```

## 🔐 Sistema de Autenticação

### Rotas Públicas
| Rota | Componente | Descrição |
|------|-----------|-----------|
| `/` | NewLandingPage | Landing page principal |
| `/login` | LoginPage | Login de usuários |
| `/register` | RegistrationFlow | Fluxo de registro completo |
| `/register-simple` | RegisterPage | Registro simples |
| `/forgot-password` | ForgotPasswordPage | Recuperação de senha |
| `/reset-password/:token` | ResetPasswordPage | Reset de senha com token |

### APIs de Autenticação (authAPI)

#### Login
```javascript
POST /api/auth/login
```
**Request:**
```json
{
  "email": "user@example.com",
  "password": "password123"
}
```
**Response:**
```json
{
  "success": true,
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "role": "seller|customer|admin",
    "profileCompleted": true,
    "hasFullProfile": true,
    "firstName": "Nome",
    "lastName": "Sobrenome"
  },
  "token": "jwt_token"
}
```
**Códigos de Erro:**
- `401`: Email ou senha incorretos
- `404`: Usuário não encontrado
- `500`: Erro interno do servidor

#### Registro
```javascript
POST /api/auth/register
```
**Request:**
```json
{
  "email": "user@example.com",
  "password": "password123",
  "firstName": "Nome",
  "lastName": "Sobrenome",
  "phone": "+5511999999999",
  "acceptTerms": true
}
```
**Códigos de Erro:**
- `400`: Dados inválidos fornecidos
- `409`: Email já está em uso
- `500`: Erro interno do servidor

#### Verificação de Email
```javascript
POST /api/auth/send-verification-email
```
**Request:**
```json
{
  "email": "user@example.com"
}
```

```javascript
POST /api/auth/verify-email-code
```
**Request:**
```json
{
  "email": "user@example.com",
  "code": "123456"
}
```

#### Recuperação de Senha
```javascript
POST /api/auth/forgot-password
```
**Request:**
```json
{
  "email": "user@example.com"
}
```

#### Reset de Senha
```javascript
POST /api/auth/reset-password
```
**Request:**
```json
{
  "token": "reset_token",
  "password": "new_password"
}
```

#### Verificar Usuário Atual
```javascript
GET /api/auth/me
```
**Headers:**
```
Authorization: Bearer <token>
```

#### Atualização de Perfil
```javascript
PUT /api/auth/profile
```
**Request:**
```json
{
  "firstName": "Novo Nome",
  "lastName": "Novo Sobrenome",
  "phone": "+5511999999999",
  "bio": "Biografia do usuário",
  "avatar": "url_da_imagem"
}
```

### Sistema de Proteção de Rotas
O componente `ProtectedRoute` verifica:
1. Token JWT válido no localStorage
2. Usuário autenticado via `/api/auth/me`
3. Permissões de role (para rotas admin)
4. Redirecionamento automático para login

### Cache de Usuário
- **TTL**: 30 segundos
- **Storage**: Memória local
- **Invalidação**: Automática ao fazer logout

## 🛒 Sistema de Produtos e Checkout

### Rotas de Produtos
| Rota | Componente | Tipo | Descrição |
|------|-----------|------|-----------|
| `/products` | ProductsPage | Protegida | Lista de produtos do vendedor |
| `/products/new` | ProductFormPage | Protegida | Criar novo produto |
| `/products/edit/:id` | ProductFormPage | Protegida | Editar produto |
| `/products/:id/analytics` | ProductAnalyticsPage | Protegida | Analytics do produto |
| `/product/:slug` | ProductPage | Pública | Página pública do produto |

### APIs de Produtos (productsAPI)

#### Listar Produtos
```javascript
GET /api/products?instructor_id={userId}
```
**Headers:**
```
Authorization: Bearer <token>
```
**Response:**
```json
{
  "success": true,
  "products": [
    {
      "id": "uuid",
      "name": "Nome do Produto",
      "slug": "produto-slug",
      "price": 99.99,
      "description": "Descrição",
      "type": "course|digital|physical",
      "status": "active|inactive",
      "createdAt": "2024-01-01T00:00:00Z"
    }
  ]
}
```

#### Criar Produto
```javascript
POST /api/products
```
**Request:**
```json
{
  "name": "Nome do Produto",
  "description": "Descrição completa",
  "price": 99.99,
  "type": "course",
  "category": "education",
  "tags": ["tag1", "tag2"]
}
```

#### Atualizar Produto
```javascript
PUT /api/products/{id}
```

#### Deletar Produto
```javascript
DELETE /api/products/{id}
```

### Sistema de Checkout

#### Rotas de Checkout
| Rota | Componente | Descrição |
|------|-----------|-----------|
| `/checkout/:slug` | CheckoutPage | Checkout básico |
| `/checkout-pro/:slug` | SophisticatedCheckoutPage | Checkout avançado |
| `/upsell/:orderId` | UpsellPage | Página de upsell |
| `/downsell/:orderId` | DownsellPage | Página de downsell |
| `/thank-you/:orderId` | ThankYouPage | Página de agradecimento |

#### Templates de Checkout Disponíveis
- **Minimal**: Design limpo e moderno, foco na conversão
- **Professional**: Layout corporativo, ideal para produtos B2B  
- **Creative**: Design colorido e dinâmico
- **Modern**: Interface moderna com elementos visuais
- **Classic**: Template tradicional e confiável
- **Premium**: Design sofisticado para produtos de alto valor

#### Configurações de Template
```javascript
{
  colors: {
    primary: "#3b82f6",
    secondary: "#6b7280", 
    accent: "#10b981",
    background: "#ffffff",
    text: "#1f2937"
  },
  fonts: {
    primary: "Inter, sans-serif",
    secondary: "system-ui, sans-serif"
  },
  layout: {
    style: "centered|full-width",
    showHeader: true,
    showFooter: false,
    showTestimonials: true,
    showGuarantee: true,
    showProgress: false
  },
  elements: {
    buttonStyle: "rounded|square",
    formStyle: "minimal|detailed",
    shadowLevel: "light|medium|heavy"
  }
}
```

#### APIs de Checkout (ordersAPI)

#### Criar Pedido
```javascript
POST /api/orders
```
**Request:**
```json
{
  "productId": "uuid",
  "customerId": "uuid",
  "paymentMethod": "pix|credit|debit|boleto",
  "customerData": {
    "name": "Cliente Nome",
    "email": "cliente@email.com",
    "phone": "+5511999999999",
    "document": "000.000.000-00",
    "address": {
      "street": "Rua Exemplo",
      "number": "123",
      "complement": "Apto 45",
      "neighborhood": "Bairro",
      "zipCode": "00000-000",
      "city": "Cidade",
      "state": "SP"
    }
  },
  "utm": {
    "source": "facebook",
    "medium": "cpc",
    "campaign": "campanha-teste",
    "content": "anuncio-a"
  },
  "affiliate": {
    "code": "AFFILIATE123",
    "commission": 10.00
  }
}
```

**Response:**
```json
{
  "success": true,
  "order": {
    "id": "uuid",
    "orderNumber": "ORD-12345",
    "status": "pending|paid|cancelled",
    "total": 99.99,
    "paymentMethod": "pix",
    "customerData": {...},
    "product": {...},
    "createdAt": "2024-01-01T00:00:00Z"
  },
  "paymentData": {
    "pixCode": "00020126...",
    "qrCode": "data:image/png;base64,...",
    "expiresAt": "2024-01-01T01:00:00Z"
  }
}
```

#### Processar Pagamento PIX
```javascript
POST /api/orders/{orderId}/payment/pix
```
**Response:**
```json
{
  "success": true,
  "pixCode": "00020126580014br.gov.bcb.pix...",
  "qrCode": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...",
  "expiresAt": "2024-01-01T01:00:00Z"
}
```

#### Processar Pagamento Cartão
```javascript
POST /api/orders/{orderId}/payment/card
```
**Request:**
```json
{
  "cardNumber": "4111111111111111",
  "expiryMonth": "12",
  "expiryYear": "2025",
  "cvv": "123",
  "holderName": "Nome no Cartão",
  "installments": 1
}
```

#### Status do Pedido
```javascript
GET /api/orders/{orderId}/status
```

#### Aplicar Cupom
```javascript
POST /api/orders/{orderId}/apply-coupon
```
**Request:**
```json
{
  "couponCode": "DESCONTO10"
}
```

#### Checkout Settings por Produto
```javascript
GET /api/products/{productId}/checkout-settings
PUT /api/products/{productId}/checkout-settings
```
**Request:**
```json
{
  "template": "minimal",
  "customization": {
    "primaryColor": "#3b82f6",
    "logo": "url_do_logo",
    "termsText": "Texto dos termos personalizados"
  },
  "fields": {
    "requirePhone": true,
    "requireDocument": true,
    "requireAddress": false
  },
  "payments": {
    "enablePix": true,
    "enableCard": true,
    "enableBoleto": false,
    "installments": {
      "max": 12,
      "minValue": 50.00
    }
  },
  "upsell": {
    "enabled": true,
    "productId": "uuid",
    "delay": 5
  },
  "integrations": {
    "facebookPixel": "123456789",
    "googleAnalytics": "GA-123456-7",
    "hotjar": "123456"
  }
}
```

## 📊 Dashboard e Analytics

### Rota do Dashboard
| Rota | Componente | Tipo |
|------|-----------|------|
| `/dashboard` | DashboardPage | Protegida |

### APIs de Analytics (analyticsAPI)

#### Dashboard Stats
```javascript
GET /api/analytics/dashboard?period={period}&timezone=America/Sao_Paulo
```
**Response:**
```json
{
  "success": true,
  "data": {
    "totalRevenue": 1500.00,
    "totalOrders": 25,
    "totalProducts": 5,
    "totalCustomers": 18,
    "trends": {
      "revenue": { "value": 15.5, "trend": "up" },
      "orders": { "value": 8.3, "trend": "up" }
    }
  }
}
```

#### Métricas de Funil
```javascript
GET /api/analytics/funnel-metrics?period={period}
```

#### Performance de Produtos
```javascript
GET /api/analytics/product-performance?period={period}
```

#### Resumo de Receita
```javascript
GET /api/analytics/revenue-summary?period={period}
```

## 👥 Sistema de Clientes

### Rotas de Clientes
| Rota | Componente | Tipo |
|------|-----------|------|
| `/customer/login` | CustomerLoginPage | Pública |
| `/customer/register` | CustomerRegisterPage | Pública |
| `/customer/dashboard` | CustomerDashboardPage | Protegida |
| `/customer/courses` | CustomerCoursesPage | Protegida |
| `/customer/orders` | CustomerOrdersPage | Protegida |
| `/customer/settings` | CustomerSettingsPage | Protegida |

### Layout de Clientes
Todas as rotas protegidas de clientes usam o `CustomerLayout` que inclui:
- Sidebar de navegação
- Header com perfil do usuário
- Área de conteúdo principal

## � Área de Vendedores/Produtores

### Rotas do Sistema de Vendedores
- `/dashboard` - Dashboard principal do vendedor
- `/products/create` - Criar novo produto
- `/products/:id/edit` - Editar produto existente  
- `/products` - Lista de produtos do vendedor
- `/analytics` - Analytics e relatórios de vendas
- `/wallet` - Carteira e saldo do vendedor
- `/wallet/withdraw` - Solicitar saque
- `/orders` - Pedidos recebidos
- `/profile` - Perfil do vendedor
- `/notifications` - Notificações
- `/courses` - Cursos disponíveis para o vendedor

### APIs do Sistema de Vendedores

#### Produtos
```javascript
// Criar produto
const createProduct = async (productData) => {
  return productsAPI.create(productData);
};

// Listar produtos do vendedor
const getSellerProducts = async () => {
  return productsAPI.getOwn();
};

// Atualizar produto
const updateProduct = async (id, productData) => {
  return productsAPI.update(id, productData);
};

// Deletar produto
const deleteProduct = async (id) => {
  return productsAPI.delete(id);
};
```

#### Analytics
```javascript
// Obter estatísticas do vendedor
const getSellerStats = async () => {
  return sellerAPI.getStats();
};

// Obter relatório de vendas
const getSalesReport = async (period) => {
  return sellerAPI.getSalesReport(period);
};
```

#### Carteira
```javascript
// Obter saldo da carteira
const getWalletBalance = async () => {
  return walletAPI.getBalance();
};

// Solicitar saque
const requestWithdraw = async (amount, method) => {
  return walletAPI.requestWithdraw({ amount, method });
};

// Histórico de transações
const getTransactionHistory = async () => {
  return walletAPI.getTransactions();
};
```

### Layout de Vendedores
Todas as rotas protegidas de vendedores usam o `SellerLayout` que inclui:
- Sidebar de navegação com menu específico de vendedor
- Header com perfil e notificações
- Sistema de breadcrumbs
- Área de conteúdo principal

### Funcionalidades Específicas

#### Dashboard do Vendedor
- Visão geral de vendas
- Produtos mais vendidos
- Receita do período
- Pedidos pendentes
- Gráficos de performance

#### Gestão de Produtos
- Criação de produtos com formulário completo
- Upload de imagens múltiplas
- Configuração de preços e promoções
- Gestão de estoque
- Categorização de produtos

#### Sistema de Analytics
- Relatórios de vendas por período
- Análise de produtos mais populares
- Métricas de conversão
- Gráficos interativos
- Exportação de dados

#### Gestão Financeira
- Visualização de saldo atual
- Histórico de transações
- Solicitação de saques
- Relatórios financeiros
- Configuração de dados bancários

## �🎓 Sistema de Cursos

### Rotas de Cursos
| Rota | Componente | Tipo |
|------|-----------|------|
| `/courses/:courseId` | CoursePage | Protegida |
| `/courses/:courseId/edit` | CourseEditPage | Protegida |
| `/course-management` | CourseManagementPage | Protegida |
| `/customer/courses/:courseId` | CourseViewPage | Protegida |
| `/customer/courses/:courseId/lesson/:lessonId` | CourseViewPage | Protegida |

### APIs de Cursos (coursesAPI)

#### Listar Cursos
```javascript
GET /api/courses?instructor_id={userId}
```

#### Criar Curso
```javascript
POST /api/courses
```
**Request:**
```json
{
  "title": "Título do Curso",
  "description": "Descrição completa",
  "price": 199.99,
  "duration": 120,
  "level": "beginner|intermediate|advanced",
  "modules": [
    {
      "title": "Módulo 1",
      "lessons": [
        {
          "title": "Aula 1",
          "videoUrl": "url_do_video",
          "duration": 15
        }
      ]
    }
  ]
}
```

#### Progresso do Aluno
```javascript
GET /api/courses/{courseId}/progress
POST /api/courses/{courseId}/progress
```

## 👨‍💼 Área Administrativa

### Rotas de Admin
| Rota | Componente | Tipo |
|------|-----------|------|
| `/admin/dashboard` | AdminDashboardPage | Admin |
| `/admin/users` | AdminUsersPage | Admin |
| `/admin/users/:userId` | AdminUserDetailPage | Admin |
| `/admin/products` | AdminProductsPage | Admin |
| `/admin/orders` | AdminOrdersPage | Admin |
| `/admin/settings` | AdminSettingsPage | Admin |
| `/admin/kyc-requests` | AdminKycRequestsPage | Admin |
| `/admin/disputes` | AdminDisputesPage | Admin |

### Proteção Admin
Rotas admin requerem `role: 'admin'` e são protegidas pelo componente `ProtectedRoute` com `adminOnly={true}`.

## 💳 Sistema de Pagamentos

### APIs de Pagamento (cardPaymentAPI)

#### Processar Pagamento
```javascript
POST /api/payments/process
```
**Request:**
```json
{
  "orderId": "uuid",
  "paymentMethod": "credit|debit|pix|boleto",
  "cardData": {
    "number": "4111111111111111",
    "expiry": "12/25",
    "cvv": "123",
    "holderName": "Nome"
  }
}
```

#### Status do Pagamento
```javascript
GET /api/payments/{paymentId}/status
```

## 📧 Sistema de Email

### APIs de Email (emailAPI)

#### Enviar Email
```javascript
POST /api/email/send
```
**Request:**
```json
{
  "to": "destinatario@email.com",
  "subject": "Assunto do Email",
  "template": "welcome|invoice|course_access|password_reset|verification",
  "data": {
    "name": "Nome do Cliente",
    "courseName": "Nome do Curso",
    "orderNumber": "ORD-12345",
    "amount": 99.99
  },
  "attachments": [
    {
      "filename": "certificado.pdf",
      "content": "base64_content"
    }
  ]
}
```

#### Templates de Email Disponíveis

##### Template de Boas-vindas
```javascript
"template": "welcome"
"data": {
  "name": "Nome do Usuário",
  "email": "email@exemplo.com"
}
```

##### Template de Fatura
```javascript
"template": "invoice" 
"data": {
  "customerName": "Nome do Cliente",
  "orderNumber": "ORD-12345",
  "items": [
    {
      "name": "Nome do Produto",
      "price": 99.99,
      "quantity": 1
    }
  ],
  "total": 99.99,
  "paymentMethod": "PIX"
}
```

##### Template de Acesso ao Curso
```javascript
"template": "course_access"
"data": {
  "studentName": "Nome do Aluno", 
  "courseName": "Nome do Curso",
  "accessUrl": "https://app.com/course/123",
  "instructorName": "Nome do Instrutor"
}
```

##### Template de Reset de Senha
```javascript
"template": "password_reset"
"data": {
  "name": "Nome do Usuário",
  "resetUrl": "https://app.com/reset-password/token123",
  "expiresIn": "1 hora"
}
```

##### Template de Verificação de Email
```javascript
"template": "verification"
"data": {
  "name": "Nome do Usuário",
  "verificationCode": "123456",
  "expiresIn": "10 minutos"
}
```

#### Sistema de Rate Limiting
- **Limite**: 5 emails por usuário
- **Janela de tempo**: 5 minutos (300000ms)
- **Reset automático**: A cada janela de tempo

#### APIs de Email Backend
```javascript
POST /api/email/verification
```
**Request:**
```json
{
  "email": "user@example.com",
  "name": "Nome do Usuário"
}
```

```javascript
POST /api/email/password-reset
```
**Request:**
```json
{
  "email": "user@example.com",
  "resetToken": "reset_token_123",
  "name": "Nome do Usuário"
}
```

```javascript
POST /api/email/course-access
```
**Request:**
```json
{
  "email": "student@example.com",
  "studentName": "Nome do Aluno",
  "courseName": "Nome do Curso",
  "courseId": "uuid",
  "instructorName": "Nome do Instrutor"
}
```

```javascript
POST /api/email/purchase-confirmation
```
**Request:**
```json
{
  "email": "customer@example.com",
  "orderData": {
    "orderNumber": "ORD-12345",
    "customerName": "Nome do Cliente",
    "items": [...],
    "total": 99.99,
    "paymentMethod": "PIX"
  }
}
```

### Configuração SMTP
O sistema usa Gmail SMTP com as seguintes configurações:
```javascript
{
  host: "smtp.gmail.com",
  port: 587,
  secure: false,
  auth: {
    user: process.env.EMAIL_USER,
    pass: process.env.EMAIL_PASS
  }
}
```

### Email Simulation Mode
Para desenvolvimento, pode-se ativar o modo simulação:
```javascript
EMAIL_SIMULATION_MODE: true
```
Neste modo, os emails são logados no console ao invés de serem enviados.

## 🔔 Sistema de Notificações

### APIs de Notificações (notificationsAPI)

#### Listar Notificações
```javascript
GET /api/notifications
```
**Response:**
```json
{
  "success": true,
  "notifications": [
    {
      "id": "uuid",
      "title": "Nova venda realizada",
      "message": "Produto vendido para cliente@email.com",
      "type": "sale|system|warning",
      "read": false,
      "createdAt": "2024-01-01T00:00:00Z"
    }
  ]
}
```

#### Marcar como Lida
```javascript
PUT /api/notifications/{id}/read
```

### Sistema de Notificações em Tempo Real
- Implementado com polling a cada 30 segundos
- Cache local para evitar requests excessivos
- Indicadores visuais no layout (badge no sino)

## 🪝 Sistema de Webhooks

### Rotas de Webhooks
| Rota | Componente |
|------|-----------|
| `/webhooks` | WebhooksPage |
| `/webhooks-management` | WebhooksManagementPage |

### APIs de Webhooks (webhooksAPI)

#### Listar Webhooks
```javascript
GET /api/webhooks
```

#### Criar Webhook
```javascript
POST /api/webhooks
```
**Request:**
```json
{
  "url": "https://exemplo.com/webhook",
  "events": ["order.created", "order.paid", "course.completed"],
  "secret": "webhook_secret"
}
```

#### Eventos Disponíveis
- `order.created`: Pedido criado
- `order.paid`: Pagamento confirmado
- `order.cancelled`: Pedido cancelado
- `course.completed`: Curso concluído
- `user.registered`: Usuário registrado

## 💰 Sistema Financeiro

### Rotas Financeiras
| Rota | Componente |
|------|-----------|
| `/wallet` | WalletPage |
| `/transactions` | TransactionsPage |
| `/user-plans` | UserPlansSettingsPage |

### APIs Financeiras

#### Carteira (Wallet)
```javascript
GET /api/wallet/balance
POST /api/wallet/withdraw
```

#### Transações
```javascript
GET /api/transactions?page={page}&limit={limit}
```

#### Planos de Usuário (plansAPI)
```javascript
GET /api/plans
POST /api/plans/subscribe
```

## 📊 Sistema de Análise e Relatórios

### APIs de Relatórios (reportsAPI)

#### Relatório de Vendas
```javascript
GET /api/reports/sales?startDate={date}&endDate={date}
```

#### Relatório de Performance
```javascript
GET /api/reports/performance?period={period}
```

#### Exportar Relatório
```javascript
GET /api/reports/export?type={type}&format=pdf|csv
```

## 🎯 Sistema de Marketing e Tracking

### UTM Tracking
```javascript
GET /api/analytics/utm-tracking
POST /api/analytics/utm-campaign
```

### Abandoned Cart Recovery
Sistema de recuperação de carrinho abandonado:

#### Iniciar Tracking de Checkout
```javascript
// startCheckoutTracking(cartData)
{
  "productId": "uuid",
  "productSlug": "produto-teste",
  "productName": "Nome do Produto",
  "productPrice": 99.99,
  "utmParams": {
    "source": "facebook",
    "medium": "cpc",
    "campaign": "campanha-teste"
  }
}
```

#### APIs de Carrinho Abandonado
```javascript
GET /api/abandoned-carts
POST /api/abandoned-carts/recover/{cartId}
DELETE /api/abandoned-carts/{cartId}
```

### Facebook API Integration
```javascript
POST /api/facebook/config/pixel
```
**Request:**
```json
{
  "pixelId": "123456789",
  "accessToken": "facebook_access_token",
  "testEventCode": "TEST12345"
}
```

#### Track Events
```javascript
POST /api/facebook/track
```
**Request:**
```json
{
  "event": "Purchase|ViewContent|AddToCart",
  "eventData": {
    "value": 99.99,
    "currency": "BRL",
    "content_ids": ["product_123"],
    "content_type": "product"
  },
  "userData": {
    "email": "cliente@email.com",
    "phone": "+5511999999999"
  }
}
```

#### Eventos de Tracking Disponíveis
- `PageView`: Visualização de página
- `ViewContent`: Visualização de produto
- `AddToCart`: Adicionar ao carrinho
- `InitiateCheckout`: Iniciar checkout
- `AddPaymentInfo`: Adicionar informações de pagamento  
- `Purchase`: Compra concluída
- `Lead`: Geração de lead
- `CompleteRegistration`: Registro completo

### Google Analytics 4
```javascript
POST /api/analytics/ga4/track
```
**Request:**
```json
{
  "measurement_id": "GA_MEASUREMENT_ID",
  "client_id": "client_id",
  "events": [
    {
      "name": "purchase",
      "parameters": {
        "transaction_id": "12345",
        "value": 99.99,
        "currency": "BRL",
        "items": [
          {
            "item_id": "produto_123",
            "item_name": "Nome do Produto",
            "category": "Cursos",
            "quantity": 1,
            "price": 99.99
          }
        ]
      }
    }
  ]
}
```

## 🔧 Sistema de Configurações

### APIs de Configurações (settingsAPI)

#### Configurações Gerais
```javascript
GET /api/settings
PUT /api/settings
```
**Request:**
```json
{
  "companyName": "Minha Empresa",
  "logo": "url_do_logo",
  "theme": "light|dark",
  "emailNotifications": true,
  "smsNotifications": false
}
```

#### Configurações de Checkout
```javascript
GET /api/settings/checkout
PUT /api/settings/checkout
```

## 🔐 Sistema KYC

### APIs KYC (kycAPI)

#### Status KYC
```javascript
GET /api/kyc/status
```

#### Enviar Documentos
```javascript
POST /api/kyc/documents
```
**Request (FormData):**
```
document_type: "cpf|rg|cnpj"
document_front: File
document_back: File
selfie: File
```

#### Histórico KYC
```javascript
GET /api/kyc/history
```

## 🗂️ Sistema de Arquivos

### Upload de Vídeos (videoAPI)
```javascript
GET /api/upload/video?maxSize={size}
POST /api/upload/video
```

### Upload de Imagens
```javascript
POST /api/upload/image
```

## 📱 Componentes de Layout

### SellerLayout
Usado para todas as páginas de vendedores/produtores:
- Sidebar com navegação categorizada
- Topbar com notificações e perfil
- Sistema de notificações em tempo real
- Menu responsivo

**Categorias do Menu:**
- **Produtos & Cursos**: Produtos, Cursos, Analytics
- **Vendas & Marketing**: Pedidos, Recovery, UTM, Funil
- **Financeiro**: Carteira, Transações, Planos
- **Automação & Integrações**: Webhooks, APIs, Pixels
- **Configurações**: Perfil, Sistema, KYC

### CustomerLayout
Usado para área de clientes:
- Navegação específica para clientes
- Acesso a cursos e pedidos
- Perfil e configurações de cliente

## 🔄 Sistema de Estados

### Context Providers
- `DataMigrationProvider`: Migração de dados
- `EnhancedToastProvider`: Sistema de notificações
- `CheckoutFlowContext`: Fluxo de checkout
- `CheckoutThemeContext`: Temas de checkout

### Custom Hooks
- `useDashboardStats`: Estatísticas do dashboard
- `useLoading`: Estados de loading
- `usePlans`: Gerenciamento de planos
- `useOutsideClick`: Detecção de cliques externos
- `usePreventTextSelection`: Prevenção de seleção indesejada

## 🚀 Scripts de Desenvolvimento

```json
{
  "dev": "vite --config vite.config.dev.js",
  "dev:simple": "vite --config vite.config.simple.js",
  "build": "vite build --config vite.config.dev.js",
  "build:prod": "vite build --config vite.config.prod.js",
  "server": "cd backend-node && npm run dev",
  "dev:full": "concurrently \"npm run server\" \"npm run dev\""
}
```

## 🔒 Sistema de Segurança

### Autenticação JWT
- Tokens armazenados em localStorage
- Refresh automático de tokens
- Validação de sessão
- Cache de usuário (30s TTL)

### Proteção de Rotas
- `ProtectedRoute`: Rotas autenticadas
- `adminOnly`: Rotas exclusivas para admin
- Redirecionamento automático para login

### Headers de Segurança
```javascript
{
  "Authorization": "Bearer <token>",
  "Content-Type": "application/json",
  "X-Requested-With": "XMLHttpRequest"
}
```

## 📋 Códigos de Status HTTP

### Sucessos (2xx)
- `200`: OK - Sucesso geral
- `201`: Created - Recurso criado
- `204`: No Content - Sucesso sem conteúdo

### Erros de Cliente (4xx)
- `400`: Bad Request - Dados inválidos
- `401`: Unauthorized - Não autenticado
- `403`: Forbidden - Sem permissão
- `404`: Not Found - Recurso não encontrado
- `422`: Unprocessable Entity - Erro de validação

### Erros de Servidor (5xx)
- `500`: Internal Server Error - Erro interno
- `503`: Service Unavailable - Serviço indisponível

## 🛠️ Ferramentas de Desenvolvimento

### Dependências Principais
- `react`: Framework principal
- `react-router-dom`: Roteamento
- `@radix-ui/*`: Componentes UI
- `tailwindcss`: Estilização
- `framer-motion`: Animações
- `sonner`: Notificações
- `lucide-react`: Ícones

### Dependências de Build
- `vite`: Build tool
- `@vitejs/plugin-react`: Plugin React
- `autoprefixer`: CSS prefixing
- `postcss`: Processamento CSS

## 📝 Padrões de Código

### Convenções de Nomes
- **Componentes**: PascalCase (`UserProfile.jsx`)
- **Hooks**: camelCase com prefixo `use` (`useAuth.js`)
- **APIs**: camelCase com sufixo `API` (`authAPI`)
- **Páginas**: PascalCase com sufixo `Page` (`DashboardPage.jsx`)

### Estrutura de Componentes
```jsx
import React, { useState, useEffect } from 'react';

const ComponentName = ({ prop1, prop2 }) => {
  const [state, setState] = useState(null);
  
  useEffect(() => {
    // Effect logic
  }, []);

  return (
    <div>
      {/* Component JSX */}
    </div>
  );
};

export default ComponentName;
```

### Estrutura de APIs
```javascript
export const apiName = {
  async method(params) {
    try {
      const response = await fetch(url, config);
      const data = await response.json();
      
      if (!response.ok) {
        throw new Error(data.message);
      }
      
      return data;
    } catch (error) {
      console.error('API Error:', error);
      throw error;
    }
  }
};
```

## 🎨 Sistema de Temas

### Configuração Tailwind
- Tema principal: Indigo/Blue
- Dark mode support
- Responsive design
- Custom components

### Cores Principais
```css
--primary: indigo-600
--secondary: slate-600
--success: green-600
--warning: yellow-600
--error: red-600
```

## 📱 Responsividade

### Breakpoints
- `sm`: 640px
- `md`: 768px
- `lg`: 1024px
- `xl`: 1280px
- `2xl`: 1536px

### Mobile First
Todos os componentes são desenvolvidos com mobile-first approach.

## 🧪 Sistema de Testes

### Rotas de Teste
- `/test`: Página de teste básica
- `/test-checkout`: Sistema de checkout de teste
- `/test-urls`: Gerador de URLs de teste

### Utilitários de Admin
- `/create-admin`: Criação simples de admin
- `/create-admin-full`: Criação completa de admin
- `/make-admin`: Utilitário para tornar usuário admin

## 👥 Sistema de Membros (Members Area)

### Rotas de Membros
| Rota | Componente | Tipo |
|------|-----------|------|
| `/members/:sellerId` | MembersAreaPage | Pública |
| `/members/:sellerId/preview` | MembersAreaPage | Pública (Preview) |
| `/members-management` | MembersManagementPage | Protegida |
| `/members-theme` | MembersThemeCustomizer | Protegida |

### Sistema de Acesso de Membros

#### Gerar Acesso de Membro
```javascript
// generateMemberAccess(purchaseData)
{
  "customerEmail": "cliente@email.com",
  "customerName": "Nome do Cliente", 
  "orderId": "uuid",
  "sellerId": "uuid",
  "products": [
    {
      "id": "uuid",
      "name": "Nome do Produto",
      "type": "course"
    }
  ]
}
```

#### Estrutura de Dados do Membro
```json
{
  "id": "member_orderId_timestamp",
  "email": "cliente@email.com",
  "name": "Nome do Cliente",
  "sellerId": "uuid",
  "orderId": "uuid", 
  "temporaryPassword": "123456",
  "passwordExpiresAt": "timestamp + 24h",
  "purchasedProducts": [
    {
      "id": "uuid",
      "name": "Nome do Produto",
      "type": "course",
      "accessGranted": true,
      "purchaseDate": "2024-01-01T00:00:00Z",
      "progress": 0,
      "lastAccessed": null
    }
  ],
  "createdAt": "2024-01-01T00:00:00Z",
  "status": "active",
  "firstAccess": false
}
```

#### APIs de Membros
```javascript
GET /api/members/:sellerId
POST /api/members/authenticate
PUT /api/members/:memberId/progress
GET /api/members/:memberId/content
```

### Customização de Tema
```javascript
PUT /api/members/:sellerId/theme
```
**Request:**
```json
{
  "primaryColor": "#3b82f6",
  "secondaryColor": "#6b7280",
  "logo": "url_do_logo",
  "backgroundImage": "url_da_imagem",
  "customCSS": "body { font-family: 'Inter' }",
  "layout": {
    "headerStyle": "minimal|full",
    "sidebarEnabled": true,
    "footerEnabled": false
  }
}
```

## 🔐 Sistema de Two-Factor Authentication (2FA)

### APIs 2FA (twoFactorAPI)

#### Ativar 2FA
```javascript
POST /api/2fa/setup
```
**Response:**
```json
{
  "qrCode": "data:image/png;base64,...",
  "secret": "secret_key",
  "backupCodes": ["12345678", "87654321", ...]
}
```

#### Verificar 2FA
```javascript
POST /api/2fa/verify
```
**Request:**
```json
{
  "token": "123456"
}
```

#### Desativar 2FA
```javascript
DELETE /api/2fa/disable
```

## 🗂️ Sistema de Logs e Auditoria

### APIs de Logs (logsAPI)

#### Logs de Sistema
```javascript
GET /api/logs/system?page={page}&limit={limit}&level={level}
```

#### Logs de Usuário
```javascript
GET /api/logs/user/{userId}
```

#### Logs de Transações
```javascript
GET /api/logs/transactions?startDate={date}&endDate={date}
```

### Tipos de Logs
- **Sistema**: `info`, `warn`, `error`, `debug`
- **Usuário**: `login`, `logout`, `profile_update`, `password_change`
- **Transação**: `payment_created`, `payment_completed`, `payment_failed`
- **Produto**: `product_created`, `product_updated`, `product_deleted`
- **Curso**: `course_enrolled`, `lesson_completed`, `course_completed`

## � Sistema de Backup e Restauração

### APIs de Backup (backupAPI)

#### Criar Backup
```javascript
POST /api/backup/create
```
**Request:**
```json
{
  "type": "full|users|products|orders",
  "compression": true,
  "encryption": true
}
```

#### Listar Backups
```javascript
GET /api/backup/list
```

#### Restaurar Backup
```javascript
POST /api/backup/restore/{backupId}
```

#### Download de Backup
```javascript
GET /api/backup/download/{backupId}
```

## ⚙️ Configurações do Sistema

### Variáveis de Ambiente
```bash
# Backend API
API_BASE_URL=http://localhost:5000/api
BACKEND_PORT=5000

# Frontend
APP_PORT=3000
APP_URL=http://localhost:3000

# Email SMTP
EMAIL_USER=your-gmail@gmail.com
EMAIL_PASS=your-app-password

### Configurações de Build
```javascript
// vite.config.dev.js
{
  server: {
    port: 3000,
    host: true
  },
  define: {
    'process.env.NODE_ENV': '"development"'
  },
  build: {
    outDir: 'dist',
    sourcemap: true
  }
}
```

## 🚀 Deploy e Produção

### Scripts de Build
```bash
# Desenvolvimento
npm run dev

# Build para produção  
npm run build:prod

# Servir produção
npm run preview

# Executar com backend
npm run dev:full
```

### Docker Configuration
```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build:prod
EXPOSE 3000
CMD ["npm", "run", "preview"]
```

### Nginx Configuration
```nginx
server {
    listen 80;
    server_name your-domain.com;
    
    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
    
    location /api {
        proxy_pass http://localhost:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

## 🔍 Troubleshooting

### Problemas Comuns

#### Erro de CORS
```javascript
// Verificar configuração em config/development.js
CORS_ORIGIN: 'http://localhost:3000'
```

#### Erro de Autenticação
```bash
# Limpar cache do navegador
localStorage.removeItem('authToken');
localStorage.removeItem('currentUser');
```

#### Erro de Build
```bash
# Limpar node_modules e reinstalar
rm -rf node_modules
npm install
```

#### Erro de Email
```bash
# Verificar configuração SMTP
EMAIL_USER=your-gmail@gmail.com
EMAIL_PASS=your-app-password  # App Password, não senha normal
```

### Logs de Debug
Para ativar logs detalhados:
```javascript
// config/development.js
DEBUG: true,
LOG_LEVEL: 'debug'
```

## 📊 Performance e Monitoramento

### Métricas de Performance
- **TTL do Cache de Usuário**: 30 segundos
- **Rate Limit de Email**: 5 emails por 5 minutos
- **Timeout de Requests**: 30 segundos
- **Compressão**: Gzip habilitado

### Monitoring Endpoints
```javascript
GET /api/health          # Health check
GET /api/metrics         # Métricas do sistema  
GET /api/performance     # Performance stats
```

---

## �📞 Contato e Suporte

Esta documentação cobre toda a estrutura do frontend, suas APIs, rotas, componentes e integrações com o backend Node.js. 

### Estrutura de Arquivos Importantes
- `src/App.jsx` - Configuração principal de rotas
- `src/lib/api.js` - Todas as APIs do sistema (3000+ linhas)
- `src/components/layout/SellerLayout.jsx` - Layout principal de vendedores
- `src/config/development.js` - Configurações de desenvolvimento
- `package.json` - Dependências e scripts

### URLs de Teste
- **Frontend**: http://localhost:3000
- **Backend**: http://localhost:5000
- **Teste Básico**: http://localhost:3000/test
- **Criar Admin**: http://localhost:3000/create-admin

Para dúvidas específicas ou implementações adicionais, consulte o código-fonte nos respectivos arquivos mencionados.

**Última atualização**: Agosto 2025  
**Versão do Sistema**: 0.0.0  
**Ambiente**: Development (localhost:3000 → localhost:5000)  
**Total de APIs**: 15+ módulos (authAPI, productsAPI, ordersAPI, etc.)  
**Total de Rotas**: 50+ rotas protegidas e públicas
