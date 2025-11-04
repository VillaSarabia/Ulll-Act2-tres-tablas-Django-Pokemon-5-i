Perfecto — te entrego **la lista ordenada y ejecutable** con los procedimientos, comandos y el código mínimo necesario para que tu proyecto **UIII_Pokemon_0227** (Django, Python, VS Code) funcione tal como pides. Incluye comandos para Windows (PowerShell) y Linux/macOS (bash), código para `views.py`, `urls.py`, `admin.py`, y plantillas básicas. ¡Vamos al grano!

---

# 1 — Crear carpeta del proyecto

Desde tu explorador o terminal:

```
mkdir UIII_Pokemon_0227
cd UIII_Pokemon_0227
```

# 2 — Abrir VS Code en la carpeta

En terminal dentro de `UIII_Pokemon_0227`:

```
code .
```

(O abre VS Code y selecciona *File → Open Folder...* y elige `UIII_Pokemon_0227`.)

# 3 — Abrir terminal en VS Code

En VS Code: menú `Terminal → New Terminal` (o atajo `Ctrl+ñ` / `Ctrl+` `(según teclado) /`Ctrl+Shift+` `).

# 4 — Crear entorno virtual `.venv` desde terminal de VS Code

En bash (Linux/macOS):

```
python3 -m venv .venv
```

En PowerShell (Windows):

```
python -m venv .venv
```

# 5 — Activar el entorno virtual

En bash (Linux/macOS):

```
source .venv/bin/activate
```

En PowerShell (Windows):

```
.venv\Scripts\Activate.ps1
```

(o si no permite políticas de ejecución, `.\.venv\Scripts\activate` en cmd)

# 6 — Activar intérprete de Python en VS Code

1. Abre la paleta `Ctrl+Shift+P`.
2. Escribe `Python: Select Interpreter`.
3. Selecciona el intérprete que apunte a `.../UIII_Pokemon_0227/.venv/...`.

# 7 — Instalar Django

Con entorno activado:

```
pip install django
```

(Comprueba versión: `django-admin --version`)

# 8 — Crear proyecto `backend_Pokemon` sin duplicar carpeta

En la carpeta raíz `UIII_Pokemon_0227`, para evitar anidar otra carpeta con el mismo nombre, usa el punto al final:

```
django-admin startproject backend_Pokemon .
```

> Esto crea la carpeta del proyecto (manage.py y backend_Pokemon/) **en la carpeta actual**, sin crear una subcarpeta extra.

# 9 — Ejecutar servidor en el puerto 8027

Desde la raíz (donde está `manage.py`) y con venv activo:

```
python manage.py runserver 8027
```

(O si quieres que escuche en todas interfaces: `python manage.py runserver 0.0.0.0:8027`)

# 10 — Copiar y pegar el link en el navegador

Abre:
`http://127.0.0.1:8027/`
ó si usas 0.0.0.0: `http://<tu-ip-local>:8027/`

# 11 — Crear aplicación `app_Pokemon`

Con venv activo y desde la raíz:

```
python manage.py startapp app_Pokemon
```

# 12 — Modelos (`models.py`)

Ya incluiste el código; lo colocas en `app_Pokemon/models.py`. (Copiar exactamente el bloque que enviaste.)

> Nota: tus modelos cumplen requisitos: `Cliente` (7 campos), `Pedido` (FK a Cliente + campos), `Producto` (FK a Pedido + campos).

# 12.5 — Procedimiento para realizar migraciones

1. Crear migraciones:

```
python manage.py makemigrations
```

2. Aplicarlas:

```
python manage.py migrate
```

# 13 — Primero trabajamos con el MODELO: CLIENTE

(Asumiremos CRUD para `Cliente` ahora.)

# 14 — `views.py` de `app_Pokemon`: funciones y código (CRUD Cliente)

Coloca esto en `app_Pokemon/views.py` (suponiendo que no usarás `forms.py` ni validación):

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Cliente

# 1. Página de inicio del sistema
def inicio_Pokemon(request):
    return render(request, 'inicio.html')

# 2. Agregar cliente (muestra formulario y procesa envío)
def agregar_cliente(request):
    if request.method == 'POST':
        nombre = request.POST.get('nombre_cliente')
        email = request.POST.get('email')
        direccion = request.POST.get('direccion')
        telefono = request.POST.get('telefono')
        tipo_entrenador = request.POST.get('tipo_entrenador')
        puntos = request.POST.get('puntos_lealtad') or 0

        Cliente.objects.create(
            nombre_cliente=nombre,
            email=email,
            direccion=direccion,
            telefono=telefono,
            puntos_lealtad=int(puntos),
            tipo_entrenador=tipo_entrenador
        )
        return redirect('ver_cliente')
    return render(request, 'clientes/agregar_cliente.html')

# 3. Ver clientes (listado)
def ver_cliente(request):
    clientes = Cliente.objects.all().order_by('nombre_cliente')
    return render(request, 'clientes/ver_cliente.html', {'clientes': clientes})

# 4. Mostrar formulario de actualización (editar)
def actualizar_cliente(request, cliente_id):
    cliente = get_object_or_404(Cliente, pk=cliente_id)
    return render(request, 'clientes/actualizar_cliente.html', {'cliente': cliente})

# 5. Procesar actualización (submit)
def realizar_actualizacion_cliente(request, cliente_id):
    cliente = get_object_or_404(Cliente, pk=cliente_id)
    if request.method == 'POST':
        cliente.nombre_cliente = request.POST.get('nombre_cliente')
        cliente.email = request.POST.get('email')
        cliente.direccion = request.POST.get('direccion')
        cliente.telefono = request.POST.get('telefono')
        cliente.tipo_entrenador = request.POST.get('tipo_entrenador')
        cliente.puntos_lealtad = int(request.POST.get('puntos_lealtad') or 0)
        cliente.save()
        return redirect('ver_cliente')
    return redirect('actualizar_cliente', cliente_id=cliente_id)

# 6. Borrar cliente (confirmación y borrado)
def borrar_cliente(request, cliente_id):
    cliente = get_object_or_404(Cliente, pk=cliente_id)
    if request.method == 'POST':
        cliente.delete()
        return redirect('ver_cliente')
    return render(request, 'clientes/borrar_cliente.html', {'cliente': cliente})
```

# 15 — Crear carpeta `templates` dentro de `app_Pokemon`

Estructura sugerida:

```
app_Pokemon/
  templates/
    base.html
    header.html
    navbar.html
    footer.html
    inicio.html
    clientes/
      agregar_cliente.html
      ver_cliente.html
      actualizar_cliente.html
      borrar_cliente.html
    categoria/   <-- carpeta vacía ahora, para futuras vistas
```

# 16 — Archivos HTML básicos

A continuación, ejemplos mínimos (no completo diseño) — ponlos en los archivos indicados.

### `base.html` (raíz `templates/`)

Incluye Bootstrap desde CDN:

```html
<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>{% block title %}Sistema Pokemon{% endblock %}</title>
  <!-- Bootstrap CSS -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
  {% include 'header.html' %}
  {% include 'navbar.html' %}
  <main class="container mt-4">
    {% block content %}{% endblock %}
  </main>
  {% include 'footer.html' %}

  <!-- Bootstrap JS -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

### `header.html`

```html
<header class="bg-white shadow-sm py-2 mb-3">
  <div class="container d-flex align-items-center">
    <h1 class="h4 mb-0">UIII Pokémon - Sistema</h1>
  </div>
</header>
```

### `navbar.html` (estructura con iconos de Bootstrap Icons)

```html
<nav class="navbar navbar-expand-lg navbar-light bg-white border-bottom">
  <div class="container">
    <a class="navbar-brand" href="{% url 'inicio' %}">🐾 Sistema de Administración Pokémon</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navMenu">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navMenu">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item"><a class="nav-link" href="{% url 'inicio' %}">Inicio</a></li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" data-bs-toggle="dropdown" href="#">Clientes</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="{% url 'agregar_cliente' %}">Agregar Cliente</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_cliente' %}">Ver Clientes</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" data-bs-toggle="dropdown" href="#">Pedidos</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar Pedidos</a></li>
            <li><a class="dropdown-item" href="#">Ver Pedidos</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" data-bs-toggle="dropdown" href="#">Productos</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar Productos</a></li>
            <li><a class="dropdown-item" href="#">Ver Productos</a></li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

> Nota: pediste iconos en opciones principales pero no en submenú; aquí use un emoji en el título para simplicidad. Puedes cambiar por Bootstrap Icons si quieres (requiere <link> adicional).

### `footer.html`

```html
<footer class="bg-white border-top mt-4 py-3 fixed-bottom">
  <div class="container text-center small">
    &copy; {{ now.year }} Sistema Pokémon — Creado por Ing. Yahel Villa, Cbtis 128
  </div>
</footer>
```

> Para que `{{ now.year }}` funcione, puedes enviar `{'now': timezone.now()}` desde la vista `inicio_Pokemon`, o usar la template tag `now`:

```django
{% load tz %}
&copy; {% now "Y" %}
```

### `inicio.html`

```html
{% extends 'base.html' %}
{% block title %}Inicio - Sistema Pokémon{% endblock %}
{% block content %}
  <div class="row">
    <div class="col-md-8">
      <h2>Bienvenido al Sistema de Administración Pokémon</h2>
      <p>Información del sistema y descripción.</p>
    </div>
    <div class="col-md-4">
      <img src="https://upload.wikimedia.org/wikipedia/commons/..." class="img-fluid" alt="Cinepolis (imagen ejemplo)">
    </div>
  </div>
{% endblock %}
```

(Usa una imagen tomada desde la web — respeta licencias.)

# 21 — Crear subcarpeta `categoria` dentro de `templates`

```
app_Pokemon/templates/categoria/
```

(Dejar para futuras vistas.)

# 22 — Archivos HTML para clientes (en `templates/clientes/`)

Sugerencias mínimas:

`agregar_cliente.html` (form simple POST):

```html
{% extends 'base.html' %}
{% block content %}
<h3>Agregar Cliente</h3>
<form method="post">{% csrf_token %}
  <div class="mb-2">
    <label>Nombre</label><input name="nombre_cliente" class="form-control">
  </div>
  <div class="mb-2">
    <label>Email</label><input name="email" type="email" class="form-control">
  </div>
  <div class="mb-2">
    <label>Dirección</label><input name="direccion" class="form-control">
  </div>
  <div class="mb-2">
    <label>Teléfono</label><input name="telefono" class="form-control">
  </div>
  <div class="mb-2">
    <label>Tipo Entrenador</label><input name="tipo_entrenador" class="form-control">
  </div>
  <div class="mb-2">
    <label>Puntos</label><input name="puntos_lealtad" type="number" class="form-control" value="0">
  </div>
  <button class="btn btn-primary" type="submit">Guardar</button>
</form>
{% endblock %}
```

`ver_cliente.html` (tabla con botones ver/editar/borrar):

```html
{% extends 'base.html' %}
{% block content %}
<h3>Clientes</h3>
<table class="table table-striped">
  <thead><tr>
    <th>Nombre</th><th>Email</th><th>Teléfono</th><th>Tipo</th><th>Acciones</th>
  </tr></thead>
  <tbody>
    {% for c in clientes %}
    <tr>
      <td>{{ c.nombre_cliente }}</td>
      <td>{{ c.email }}</td>
      <td>{{ c.telefono }}</td>
      <td>{{ c.tipo_entrenador }}</td>
      <td>
        <a class="btn btn-sm btn-info" href="{% url 'actualizar_cliente' c.id %}">Editar</a>
        <a class="btn btn-sm btn-danger" href="{% url 'borrar_cliente' c.id %}">Borrar</a>
      </td>
    </tr>
    {% empty %}
    <tr><td colspan="5">No hay clientes.</td></tr>
    {% endfor %}
  </tbody>
</table>
{% endblock %}
```

`actualizar_cliente.html`:

```html
{% extends 'base.html' %}
{% block content %}
<h3>Actualizar Cliente</h3>
<form method="post" action="{% url 'realizar_actualizacion_cliente' cliente.id %}">{% csrf_token %}
  <input name="nombre_cliente" value="{{ cliente.nombre_cliente }}" class="form-control mb-2">
  <input name="email" value="{{ cliente.email }}" class="form-control mb-2">
  <input name="direccion" value="{{ cliente.direccion }}" class="form-control mb-2">
  <input name="telefono" value="{{ cliente.telefono }}" class="form-control mb-2">
  <input name="tipo_entrenador" value="{{ cliente.tipo_entrenador }}" class="form-control mb-2">
  <input name="puntos_lealtad" value="{{ cliente.puntos_lealtad }}" class="form-control mb-2" type="number">
  <button class="btn btn-primary" type="submit">Actualizar</button>
</form>
{% endblock %}
```

`borrar_cliente.html`:

```html
{% extends 'base.html' %}
{% block content %}
<h3>¿Eliminar cliente {{ cliente.nombre_cliente }}?</h3>
<form method="post">{% csrf_token %}
  <button type="submit" class="btn btn-danger">Confirmar borrar</button>
  <a class="btn btn-secondary" href="{% url 'ver_cliente' %}">Cancelar</a>
</form>
{% endblock %}
```

# 23 — No utilizar `forms.py`

Se implementó tal como pediste (formularios HTML y `request.POST` en vistas).

# 24 — `urls.py` en `app_Pokemon`

Crea `app_Pokemon/urls.py` con este contenido:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_Pokemon, name='inicio'),
    path('clientes/agregar/', views.agregar_cliente, name='agregar_cliente'),
    path('clientes/', views.ver_cliente, name='ver_cliente'),
    path('clientes/actualizar/<int:cliente_id>/', views.actualizar_cliente, name='actualizar_cliente'),
    path('clientes/actualizar/realizar/<int:cliente_id>/', views.realizar_actualizacion_cliente, name='realizar_actualizacion_cliente'),
    path('clientes/borrar/<int:cliente_id>/', views.borrar_cliente, name='borrar_cliente'),
]
```

# 25 — Agregar `app_Pokemon` en `settings.py` de `backend_Pokemon`

En `backend_Pokemon/settings.py` añade en `INSTALLED_APPS`:

```python
INSTALLED_APPS = [
    # ... otras apps
    'app_Pokemon',
    # opcional: 'django.contrib.staticfiles' ya debe estar
]
```

# 26 — Configurar `urls.py` de `backend_Pokemon` para enlazar con `app_Pokemon`

En `backend_Pokemon/urls.py` (archivo del proyecto):

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Pokemon.urls')),
]
```

# 27 — Registrar modelos en `admin.py` y volver a migrar

En `app_Pokemon/admin.py`:

```python
from django.contrib import admin
from .models import Cliente, Pedido, Producto

@admin.register(Cliente)
class ClienteAdmin(admin.ModelAdmin):
    list_display = ('nombre_cliente','email','telefono','tipo_entrenador','puntos_lealtad')

# Registrar pero no es obligatorio mostrar todos
admin.site.register(Pedido)
admin.site.register(Producto)
```

Luego:

```
python manage.py makemigrations app_Pokemon
python manage.py migrate
```

(Esto asegura que Django tenga las tablas.)

# 27 (nota) — Por ahora trabajar solo con `Cliente`

Dejar `Pedido` y `Producto` para después; ya están en `models.py` pero el CRUD y plantillas se enfocan en `Cliente`.

# 28 — Estética: colores suaves y código sencillo

Usar Bootstrap, `bg-light` y `card` para secciones hace la UI moderna y suave. Con los templates y clases ya propuestas tendrás apariencia moderna y simple.

# 29 — Al inicio crear la estructura completa de carpetas y archivos

Estructura sugerida completa:

```
UIII_Pokemon_0227/
  .venv/
  manage.py
  backend_Pokemon/
    __init__.py
    settings.py
    urls.py
    wsgi.py
    asgi.py
  app_Pokemon/
    migrations/
    __init__.py
    admin.py
    apps.py
    models.py
    views.py
    urls.py
    templates/
      base.html
      header.html
      navbar.html
      footer.html
      inicio.html
      clientes/
        agregar_cliente.html
        ver_cliente.html
        actualizar_cliente.html
        borrar_cliente.html
      categoria/
    static/   (opcional para CSS/JS/imagenes)
```

# 30 — Proyecto totalmente funcional

Con lo anterior: modelos, vistas, urls, templates y admin, el CRUD de `Cliente` funcionará. Recuerda crear un superusuario para acceder al admin:

```
python manage.py createsuperuser
```

# 31 — Finalmente ejecutar servidor en el puerto 8027

Repetido intencionalmente para finalizar:

```
python manage.py runserver 8027
```

---

## Consejos y recordatorios rápidos

* Si `django-admin startproject backend_Pokemon .` da error porque ya hay archivos, crea el proyecto desde una carpeta vacía o elimina archivos conflictivos.
* Para que `footer` no tape contenido cuando está `fixed-bottom`, añade espaciado al final del `main` (`pb-5` o similar).
* No validamos entradas (tal como pediste). Si en el futuro quieres validaciones mínimas, lo hacemos con `if not campo: ...` en vistas.
* Para usar imágenes remotas en `inicio.html` respeta licencias; usa imágenes con permiso o dominio público.

---

Si quieres, **ahora** puedo:

* Generar los archivos con contenido completo (puedo volcar el código listo para pegar).
* Crear las plantillas `clientes/` completas y el `base.html` con más estilo.
* Probar (simular) comandos y darte un checklist de verificación (qué ver en la consola cuando todo esté bien).

Dime cuál de esas tareas quieres que haga **ahora mismo** y te genero los archivos/código listos para pegar en tu VS Code.

