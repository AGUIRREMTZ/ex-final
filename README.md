# NSL-KDD Dataset Processing API

API REST con Django para procesamiento de datasets usando transformadores y pipelines personalizados basados en scikit-learn.

## Características

- **División de Datasets**: Split estratificado en train/validation/test
- **Preprocesamiento**: Manejo de valores nulos, imputación
- **Transformadores Personalizados**: 
  - DeleteNanRows: Elimina filas con valores nulos
  - CustomScaler: Escalado robusto de features
  - CustomOneHotEncoding: Codificación one-hot personalizada
- **Pipelines**: Pipelines completos para preprocesamiento
- **Codificación Categórica**: One-Hot y Ordinal Encoding
- **Escalado de Features**: RobustScaler para datos numéricos

## Instalación Local

### Requisitos Previos
- Python 3.9 o superior instalado en tu computadora
- pip (gestor de paquetes de Python)
- Editor de código (VS Code, PyCharm, etc.)

### Pasos de Instalación Manual

1. **Descargar el proyecto**
   - Ve a tu repositorio en GitHub
   - Haz clic en el botón verde "Code"
   - Selecciona "Download ZIP"
   - Extrae el archivo ZIP en la carpeta donde quieras trabajar

2. **Crear entorno virtual**
   - Abre tu terminal o símbolo del sistema
   - Navega hasta la carpeta del proyecto
   - En Windows: Busca "cmd" en el menú inicio, ábrelo y usa `cd` para ir a la carpeta
   - En Mac/Linux: Abre Terminal y usa `cd` para ir a la carpeta
   - Crea un entorno virtual escribiendo: `python -m venv venv`
   - Activa el entorno:
     - Windows: `venv\Scripts\activate`
     - Mac/Linux: `source venv/bin/activate`

3. **Instalar dependencias**
   - Con el entorno virtual activado, instala las dependencias: `pip install -r requirements.txt`
   - Espera a que se descarguen e instalen todos los paquetes

4. **Preparar la base de datos**
   - Ejecuta: `python manage.py migrate`
   - Esto creará las tablas necesarias en la base de datos

5. **Iniciar el servidor**
   - Ejecuta: `python manage.py runserver`
   - Abre tu navegador y ve a `http://localhost:8000/api/`
   - Deberías ver la página de inicio de la API

## Endpoints de la API

### 1. Overview
\`\`\`
GET /api/
\`\`\`
Retorna información sobre todos los endpoints disponibles.

### 2. Split Dataset
\`\`\`
POST /api/split-dataset/
\`\`\`

**Body:**
\`\`\`json
{
  "data": [[0, "tcp", "http", ...], ...],
  "columns": ["duration", "protocol_type", "service", ...],
  "stratify_column": "protocol_type",
  "random_state": 42,
  "shuffle": true
}
\`\`\`

**Response:**
\`\`\`json
{
  "message": "Dataset split successfully",
  "train_size": 75583,
  "validation_size": 25195,
  "test_size": 25195,
  "train_sample": [...],
  "validation_sample": [...],
  "test_sample": [...]
}
\`\`\`

### 3. Preprocess Data
\`\`\`
POST /api/preprocess/
\`\`\`

**Body:**
\`\`\`json
{
  "data": [[...], ...],
  "columns": ["col1", "col2", ...],
  "remove_nan": false,
  "impute_strategy": "median"
}
\`\`\`

### 4. Transform Categorical
\`\`\`
POST /api/transform-categorical/
\`\`\`

**Body:**
\`\`\`json
{
  "data": [[...], ...],
  "columns": ["col1", "col2", ...],
  "encoding_type": "onehot"
}
\`\`\`

### 5. Scale Features
\`\`\`
POST /api/scale-features/
\`\`\`

**Body:**
\`\`\`json
{
  "data": [[...], ...],
  "columns": ["col1", "col2", ...],
  "features_to_scale": ["src_bytes", "dst_bytes"]
}
\`\`\`

### 6. Apply Pipeline
\`\`\`
POST /api/apply-pipeline/
\`\`\`

**Body:**
\`\`\`json
{
  "data": [[...], ...],
  "columns": ["col1", "col2", ...],
  "pipeline_type": "full"
}
\`\`\`

### 7. Dataset Info
\`\`\`
POST /api/dataset-info/
\`\`\`

**Body:**
\`\`\`json
{
  "data": [[...], ...],
  "columns": ["col1", "col2", ...]
}
\`\`\`

## Despliegue en Vercel (Manual)

### Preparación del Proyecto

1. **Crear cuenta en Vercel**
   - Ve a https://vercel.com
   - Haz clic en "Sign Up"
   - Regístrate con tu cuenta de GitHub (recomendado)

2. **Subir tu proyecto a GitHub**
   - Ve a https://github.com
   - Haz clic en el botón "+" en la esquina superior derecha
   - Selecciona "New repository"
   - Dale un nombre a tu repositorio (ejemplo: "django-ml-api")
   - Selecciona "Public" o "Private" según prefieras
   - NO inicialices con README (ya tienes uno)
   - Haz clic en "Create repository"

3. **Conectar tu proyecto local con GitHub**
   - En tu terminal, dentro de la carpeta del proyecto:
   - Inicializa git: `git init`
   - Agrega todos los archivos: `git add .`
   - Haz tu primer commit: `git commit -m "Initial commit"`
   - Conecta con GitHub (usa la URL que te dio GitHub):
     \`\`\`
     git remote add origin https://github.com/TU-USUARIO/TU-REPOSITORIO.git
     git branch -M main
     git push -u origin main
     \`\`\`

### Despliegue en Vercel

1. **Importar proyecto en Vercel**
   - Inicia sesión en https://vercel.com
   - Haz clic en "Add New..." → "Project"
   - Verás una lista de tus repositorios de GitHub
   - Busca tu repositorio "django-ml-api" y haz clic en "Import"

2. **Configurar el proyecto**
   - En "Framework Preset", selecciona "Other"
   - En "Root Directory", deja el valor por defecto (.)
   - En "Build Command", deja vacío o escribe: `sh build_files.sh`
   - En "Output Directory", deja vacío
   - En "Install Command", deja: `pip install -r requirements.txt`

3. **Agregar variables de entorno**
   - Antes de hacer clic en "Deploy", expande la sección "Environment Variables"
   - Agrega las siguientes variables:
     
     **Variable 1:**
     - Name: `DJANGO_SECRET_KEY`
     - Value: (genera una clave secreta aleatoria, por ejemplo: `tu-clave-super-secreta-123456`)
     
     **Variable 2:**
     - Name: `DEBUG`
     - Value: `False`
     
     **Variable 3:**
     - Name: `ALLOWED_HOSTS`
     - Value: `.vercel.app`

4. **Desplegar**
   - Haz clic en el botón "Deploy"
   - Espera a que Vercel construya y despliegue tu proyecto (puede tomar 2-5 minutos)
   - Una vez completado, verás un mensaje de éxito con la URL de tu API
   - Haz clic en la URL para visitar tu API en producción

5. **Actualizar tu proyecto**
   - Cada vez que hagas cambios en tu código local:
     - Guarda los archivos
     - En terminal: `git add .`
     - En terminal: `git commit -m "Descripción de tus cambios"`
     - En terminal: `git push`
   - Vercel detectará automáticamente los cambios y volverá a desplegar

### Limitaciones en Vercel

⚠️ **Importante**: Vercel tiene limitaciones para Django:
- Las funciones serverless tienen un timeout de 10 segundos en el plan gratuito
- No hay persistencia de base de datos SQLite entre ejecuciones
- El tamaño máximo de cada función es 50MB
- Django no está optimizado para arquitectura serverless

### Alternativas Recomendadas para Producción

Para un despliegue más robusto de Django, considera estas plataformas:

**Railway** (Recomendado para principiantes)
1. Ve a https://railway.app
2. Regístrate con GitHub
3. Haz clic en "New Project"
4. Selecciona "Deploy from GitHub repo"
5. Elige tu repositorio
6. Railway detectará automáticamente que es Django
7. Agrega las variables de entorno en la pestaña "Variables"
8. El despliegue es automático

**Render**
1. Ve a https://render.com
2. Regístrate con GitHub
3. Haz clic en "New +" → "Web Service"
4. Conecta tu repositorio de GitHub
5. Configura:
   - Environment: Python 3
   - Build Command: `pip install -r requirements.txt && python manage.py migrate`
   - Start Command: `gunicorn ml_api.wsgi:application`
6. Agrega variables de entorno
7. Haz clic en "Create Web Service"

**PythonAnywhere** (Más tradicional)
1. Ve a https://www.pythonanywhere.com
2. Crea una cuenta gratuita
3. Ve a "Web" → "Add a new web app"
4. Selecciona "Manual configuration" → Python 3.9
5. En la pestaña "Code", configura el directorio de tu proyecto
6. Sube tus archivos usando el File Manager o Git
7. Configura el archivo WSGI según las instrucciones de PythonAnywhere
8. Recarga la aplicación

## Ejemplo de Uso con Python

\`\`\`python
import requests
import json

# URL de la API
API_URL = "http://localhost:8000/api"

# Datos de ejemplo
data = {
    "data": [
        [0, "tcp", "http", "SF", 181, 5450, "0", 0, 0, 0, 0, "1", 0, 0, 0, 0, 0, 0, 0, 0, "0", "0", 8, 8, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 9, 9, 1.0, 0.0, 0.11, 0.0, 0.0, 0.0, 0.0, 0.0],
        [0, "tcp", "http", "SF", 239, 486, "0", 0, 0, 0, 0, "1", 0, 0, 0, 0, 0, 0, 0, 0, "0", "0", 8, 8, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 19, 19, 1.0, 0.0, 0.05, 0.0, 0.0, 0.0, 0.0, 0.0]
    ],
    "columns": ["duration", "protocol_type", "service", "flag", "src_bytes", "dst_bytes", "land", "wrong_fragment", "urgent", "hot", "num_failed_logins", "logged_in", "num_compromised", "root_shell", "su_attempted", "num_root", "num_file_creations", "num_shells", "num_access_files", "num_outbound_cmds", "is_host_login", "is_guest_login", "count", "srv_count", "serror_rate", "srv_serror_rate", "rerror_rate", "srv_rerror_rate", "same_srv_rate", "diff_srv_rate", "srv_diff_host_rate", "dst_host_count", "dst_host_srv_count", "dst_host_same_srv_rate", "dst_host_diff_srv_rate", "dst_host_same_src_port_rate", "dst_host_srv_diff_host_rate", "dst_host_serror_rate", "dst_host_srv_serror_rate", "dst_host_rerror_rate", "dst_host_srv_rerror_rate"]
}

# Obtener información del dataset
response = requests.post(f"{API_URL}/dataset-info/", json=data)
print(json.dumps(response.json(), indent=2))

# Aplicar pipeline completo
response = requests.post(f"{API_URL}/apply-pipeline/", json={
    **data,
    "pipeline_type": "full"
})
print(json.dumps(response.json(), indent=2))
\`\`\`

## Estructura del Proyecto

\`\`\`
.
├── ml_api/                 # Configuración principal de Django
│   ├── __init__.py
│   ├── settings.py        # Configuración de Django
│   ├── urls.py            # URLs principales
│   └── wsgi.py            # WSGI para despliegue
├── dataset_api/           # Aplicación principal
│   ├── utils/
│   │   ├── data_loader.py    # Funciones de carga de datos
│   │   ├── transformers.py   # Transformadores personalizados
│   │   └── pipelines.py      # Pipelines de sklearn
│   ├── views.py           # Vistas de la API
│   └── urls.py            # URLs de la aplicación
├── requirements.txt       # Dependencias Python
├── vercel.json           # Configuración de Vercel
├── build_files.sh        # Script de build para Vercel
└── README.md             # Este archivo
\`\`\`

## Tecnologías Utilizadas

- **Django 4.2**: Framework web
- **Django REST Framework**: API REST
- **pandas**: Manipulación de datos
- **scikit-learn**: Machine Learning y preprocesamiento
- **numpy**: Operaciones numéricas
- **liac-arff**: Lectura de archivos ARFF

## Contribuir

Las contribuciones son bienvenidas. Para contribuir:

1. **Fork del proyecto**
   - Ve al repositorio en GitHub
   - Haz clic en el botón "Fork" en la esquina superior derecha
   - Esto creará una copia del proyecto en tu cuenta

2. **Clonar tu fork**
   - En tu fork, haz clic en "Code" y copia la URL
   - En tu terminal: `git clone URL-DE-TU-FORK`

3. **Crear una rama para tu feature**
   - `git checkout -b feature/NombreDeTuFeature`

4. **Hacer cambios y commit**
   - Realiza tus cambios en el código
   - `git add .`
   - `git commit -m 'Descripción de los cambios'`

5. **Subir cambios**
   - `git push origin feature/NombreDeTuFeature`

6. **Crear Pull Request**
   - Ve a tu fork en GitHub
   - Verás un botón "Compare & pull request"
   - Haz clic y describe tus cambios
   - Envía el Pull Request

## Licencia

Este proyecto está bajo la Licencia MIT.

## Contacto

Para preguntas o sugerencias, abre un issue en el repositorio de GitHub.
