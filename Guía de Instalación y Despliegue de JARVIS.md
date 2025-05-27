# Guía de Instalación y Despliegue de JARVIS

Esta guía detalla el proceso de instalación, configuración y despliegue de la plataforma JARVIS, un asistente virtual inteligente con múltiples integraciones.

## Requisitos del Sistema

### Requisitos de Software
- Python 3.8 o superior
- Node.js 14.0 o superior
- npm 6.0 o superior
- SQLite (desarrollo) o PostgreSQL (producción)
- Git

### Requisitos de Hardware Recomendados
- CPU: 2 núcleos o más
- RAM: 4GB mínimo (8GB recomendado)
- Almacenamiento: 1GB disponible

## Estructura del Proyecto

El proyecto JARVIS está organizado en dos componentes principales:

```
jarvis_project/
├── backend/               # Servidor Flask y API
│   ├── migrations/        # Migraciones de base de datos
│   ├── src/               # Código fuente del backend
│   │   ├── auth/          # Autenticación y OAuth
│   │   ├── integrations/  # Integraciones con servicios externos
│   │   ├── models/        # Modelos de datos
│   │   ├── routes/        # Rutas API
│   │   └── static/        # Archivos estáticos (producción)
│   ├── tests/             # Pruebas unitarias y de integración
│   ├── .env.example       # Plantilla de variables de entorno
│   ├── manage.py          # Script de gestión
│   └── requirements.txt   # Dependencias Python
├── frontend/              # Aplicación React
│   ├── public/            # Archivos públicos
│   ├── src/               # Código fuente del frontend
│   │   ├── components/    # Componentes React
│   │   ├── pages/         # Páginas de la aplicación
│   │   ├── services/      # Servicios y API
│   │   └── styles/        # Estilos CSS/SCSS
│   ├── package.json       # Dependencias y scripts
│   └── tailwind.config.js # Configuración de Tailwind CSS
└── scripts/               # Scripts de utilidad y despliegue
    └── deploy.sh          # Script de despliegue automatizado
```

## Instalación

### 1. Clonar el Repositorio

```bash
git clone https://github.com/tu-organizacion/jarvis.git
cd jarvis
```

### 2. Configurar el Backend

#### Crear y Activar Entorno Virtual

```bash
cd backend
python -m venv venv
source venv/bin/activate  # En Windows: venv\Scripts\activate
```

#### Instalar Dependencias

```bash
pip install -r requirements.txt
```

#### Configurar Variables de Entorno

```bash
cp .env.example .env
```

Edite el archivo `.env` con sus configuraciones:

```
# Configuración de la aplicación
FLASK_APP=src.main:create_app
FLASK_ENV=development
SECRET_KEY=your-secret-key-here

# Configuración de la base de datos
DATABASE_URL=sqlite:///jarvis.db

# Configuración JWT
JWT_SECRET_KEY=your-jwt-secret-key
JWT_ACCESS_TOKEN_EXPIRES=3600
JWT_REFRESH_TOKEN_EXPIRES=2592000

# Claves de cifrado
ENCRYPTION_KEY=your-32-byte-encryption-key

# Configuración de integraciones
GOOGLE_CLIENT_ID=your-google-client-id
GOOGLE_CLIENT_SECRET=your-google-client-secret

MICROSOFT_CLIENT_ID=your-microsoft-client-id
MICROSOFT_CLIENT_SECRET=your-microsoft-client-secret

SLACK_CLIENT_ID=your-slack-client-id
SLACK_CLIENT_SECRET=your-slack-client-secret

CLICKUP_CLIENT_ID=your-clickup-client-id
CLICKUP_CLIENT_SECRET=your-clickup-client-secret

OPENAI_API_KEY=your-openai-api-key
```

#### Inicializar la Base de Datos

```bash
python manage.py db upgrade
```

### 3. Configurar el Frontend

```bash
cd ../frontend
npm install
```

## Ejecución en Entorno de Desarrollo

### Iniciar el Backend

```bash
cd backend
source venv/bin/activate  # En Windows: venv\Scripts\activate
python manage.py run
```

El servidor estará disponible en `http://localhost:5000`.

### Iniciar el Frontend

```bash
cd frontend
npm start
```

La aplicación estará disponible en `http://localhost:3000`.

## Despliegue en Producción

### Usando el Script de Despliegue Automatizado

El proyecto incluye un script de despliegue que automatiza el proceso:

```bash
chmod +x scripts/deploy.sh
./scripts/deploy.sh prod
```

### Despliegue Manual

#### 1. Configurar el Backend para Producción

```bash
cd backend
source venv/bin/activate

# Configurar variables de entorno para producción
export FLASK_ENV=production
export FLASK_DEBUG=0

# Actualizar la base de datos
python manage.py db upgrade

# Instalar Gunicorn (si no está instalado)
pip install gunicorn

# Iniciar el servidor
gunicorn -w 4 -b 0.0.0.0:5000 'src.main:create_app()'
```

#### 2. Construir el Frontend para Producción

```bash
cd frontend
npm run build

# Copiar archivos estáticos al backend (opcional)
mkdir -p ../backend/src/static
cp -r build/* ../backend/src/static/
```

## Configuración de Integraciones

### Google Drive

1. Cree un proyecto en [Google Cloud Console](https://console.cloud.google.com/)
2. Habilite la API de Google Drive
3. Configure las credenciales OAuth:
   - Tipo: Aplicación Web
   - URI de redirección: `https://su-dominio.com/api/oauth/callback/google_drive`
4. Copie el Client ID y Client Secret a su archivo `.env`

### Slack

1. Cree una aplicación en [Slack API](https://api.slack.com/apps)
2. Configure los permisos OAuth:
   - Scopes: `chat:write`, `channels:read`, `channels:history`
   - URL de redirección: `https://su-dominio.com/api/oauth/callback/slack`
3. Copie el Client ID y Client Secret a su archivo `.env`

### ClickUp

1. Cree una aplicación en [ClickUp API](https://clickup.com/api)
2. Configure OAuth:
   - URL de redirección: `https://su-dominio.com/api/oauth/callback/clickup`
3. Copie el Client ID y Client Secret a su archivo `.env`

### ChatGPT (OpenAI)

1. Obtenga una API key en [OpenAI Platform](https://platform.openai.com/)
2. Copie la API key a su archivo `.env`

## Pruebas

### Ejecutar Pruebas Unitarias

```bash
cd backend
source venv/bin/activate
python -m unittest discover tests
```

### Ejecutar Pruebas de Integración

```bash
cd backend
source venv/bin/activate
python -m unittest tests/test_api.py
```

## Solución de Problemas

### Problemas de Conexión con Integraciones

Si experimenta problemas con las integraciones:

1. Verifique que las credenciales en `.env` sean correctas
2. Asegúrese de que las URLs de redirección estén configuradas correctamente
3. Revise los logs del servidor para mensajes de error específicos

### Errores de Base de Datos

Si encuentra errores relacionados con la base de datos:

1. Verifique que la URL de la base de datos sea correcta
2. Asegúrese de que las migraciones estén actualizadas:
   ```bash
   python manage.py db upgrade
   ```
3. Si es necesario, reinicie la base de datos:
   ```bash
   python manage.py db downgrade base
   python manage.py db upgrade
   ```

## Seguridad

- Nunca comparta sus claves de API o secretos
- Mantenga su archivo `.env` fuera del control de versiones
- Actualice regularmente las dependencias para parchar vulnerabilidades
- En producción, use HTTPS para todas las comunicaciones
- Implemente límites de tasa para prevenir abusos

## Recursos Adicionales

- [Documentación de Flask](https://flask.palletsprojects.com/)
- [Documentación de React](https://reactjs.org/docs/getting-started.html)
- [Documentación de SQLAlchemy](https://docs.sqlalchemy.org/)
- [Documentación de Tailwind CSS](https://tailwindcss.com/docs)
