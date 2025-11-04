
# 1) Estructura de carpetas y archivos
```
UIII_Paleteria_0588/                  <- carpeta raíz del proyecto
├── .venv/                            <- entorno virtual
├── backend_paleteria/                <- carpeta del proyecto Django (settings, wsgi, etc.)
│   ├── backend_paleteria/
│   │   ├── __init__.py
│   │   ├── settings.py
│   │   ├── urls.py
│   │   └── wsgi.py
│   ├── manage.py
│   └── db.sqlite3                     <- después de migrar (opcional)
├── app_Paleteria/                     <- la app principal
│   ├── migrations/
│   │   └── __init__.py
│   ├── templates/
│   │   ├── base.html
│   │   ├── header.html
│   │   ├── navbar.html
│   │   ├── footer.html
│   │   └── inicio.html
│   │   └── proveedor/                 <- subcarpeta para vistas de proveedor
│   │       ├── agregar_proveedor.html
│   │       ├── ver_proveedor.html
│   │       ├── actualizar_proveedor.html
│   │       └── borrar_proveedor.html
│   ├── static/                        <- (opcional) css, img, js
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── views.py
│   ├── urls.py                        <- urls específicas de la app
│   └── tests.py
└── README.md                          <- opcional: instrucciones del proyecto
```

---

# 2) Procedimientos paso a paso (comandos y código)

> Supondré Windows (PowerShell / CMD) y VS Code, pero incluyo comandos para Linux/macOS cuando difiere.

### 1. Crear la carpeta del proyecto

Abrir el Explorador y crear la carpeta o usar terminal:

```bash
mkdir UIII_Paleteria_0588
cd UIII_Paleteria_0588
```

### 2. Abrir VS Code en la carpeta `UIII_Paleteria_0588`

En terminal dentro de la carpeta:

```bash
code .
```

O desde VS Code: `File → Open Folder...` y selecciona `UIII_Paleteria_0588`.

### 3. Abrir terminal en VS Code

Dentro de VS Code: `Ctrl + ` (tecla de tilde) o `View → Terminal`.

### 4. Crear carpeta entorno virtual `.venv` desde terminal de VS Code

Python debe estar instalado. Crear venv:

```bash
python -m venv .venv
```

(o `py -m venv .venv` si usas `py` en Windows)

### 5. Activar el entorno virtual

* **Windows PowerShell:**

```powershell
.\.venv\Scripts\Activate.ps1
```

(si hay restricción de ejecución: `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass`)

* **Windows CMD:**

```cmd
.\.venv\Scripts\activate
```

* **Linux / macOS:**

```bash
source .venv/bin/activate
```

Al activarlo verás `(.venv)` al inicio de la línea.

### 6. Activar intérprete de Python en VS Code

En VS Code: `Ctrl+Shift+P` → escribe `Python: Select Interpreter` → selecciona la ruta que apunte a `UIII_Paleteria_0588/.venv/...`.

### 7. Instalar Django

Con el entorno activo:

```bash
pip install django
```

Puedes fijar versión si quieres: `pip install "django==4.2"` (opcional).

### 8. Crear proyecto `backend_paleteria` sin duplicar carpeta

**Importante**: para evitar crear un subdirectorio con el mismo nombre dentro de otro, entra en la carpeta raíz (`UIII_Paleteria_0588`) y ejecuta:

```bash
django-admin startproject backend_paleteria .
```

> Nota: el `.` al final indica crear el proyecto en la carpeta actual (no crear `backend_paleteria/backend_paleteria` extra).

Si ya ejecutaste `startproject backend_paleteria` sin el punto y te creó una carpeta extra, borra/fusiona y vuelve a ejecutar con `.`.

### 9. Ejecutar servidor en el puerto 8036

Desde la raíz (donde está `manage.py`):

```bash
python manage.py runserver 8036
```

(esto ejecuta en `http://127.0.0.1:8036/`)

### 10. Copiar y pegar el link en el navegador

Abre el navegador y pega:

```
http://127.0.0.1:8036/
```

o

```
http://localhost:8036/
```

### 11. Crear aplicación `app_Paleteria`

Desde la raíz del proyecto:

```bash
python manage.py startapp app_Paleteria
```

Esto crea la carpeta `app_Paleteria` con archivos básicos.

---

### 12. Aquí está el `models.py` (ya lo mandaste) — lo pego con un pequeño ajuste: `fecha_elaboracion` como Char se mantiene según tu código.

`app_Paleteria/models.py`

```python
from django.db import models

class Proveedor(models.Model):
    id_proveedor = models.AutoField(primary_key=True)
    nombre_empresa = models.CharField(max_length=100, unique=True)
    contacto = models.CharField(max_length=100, unique=True)
    telefono = models.CharField(max_length=50, unique=True)
    email = models.CharField(max_length=100, unique=True)
    direccion = models.CharField(max_length=100, unique=True)
    fecha_registro = models.DateField()
    ciudad = models.CharField(max_length=100)

    def __str__(self):
        return self.nombre_empresa

class Producto(models.Model):
    id_producto = models.AutoField(primary_key=True)
    nombre = models.CharField(max_length=100, unique=True)
    sabor = models.CharField(max_length=100, unique=True)
    precio = models.CharField(max_length=100)
    stock = models.CharField(max_length=100)
    tipo = models.CharField(max_length=100)
    fecha_elaboracion = models.CharField(max_length=100)
    id_proveedor = models.ForeignKey(Proveedor, on_delete=models.CASCADE, db_column='id_proveedor', related_name='productos')

    def __str__(self):
        return f"{self.nombre} ({self.sabor})"

class Cliente(models.Model):
    id_cliente = models.AutoField(primary_key=True)
    nombre = models.CharField(max_length=100, unique=True)
    apellido = models.CharField(max_length=100, unique=True)
    telefono = models.CharField(max_length=50, unique=True)
    email = models.CharField(max_length=100, unique=True)
    direccion = models.CharField(max_length=100, unique=True)
    fecha_registro = models.DateField()
    productos = models.ManyToManyField(Producto, related_name='clientes')

    def __str__(self):
        return f"{self.nombre} {self.apellido}"
```

> Mantendré tu decisión de no validar entradas ni usar `forms.py`.

---

### 12.5 Procedimiento para hacer migraciones

Primero registra la app en `settings.py` (punto 25 abajo), luego:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

### 13. Empezamos trabajando solo con el MODELO: PROVEEDOR

### 14. `views.py` de `app_Paleteria` — funciones para CRUD (esqueleto)

`app_Paleteria/views.py`

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Proveedor
from django.urls import reverse

def inicio_paleteria(request):
    return render(request, 'inicio.html')

def agregar_proveedor(request):
    if request.method == 'POST':
        # No usamos forms.py: obtenemos valores directamente
        nombre_empresa = request.POST.get('nombre_empresa')
        contacto = request.POST.get('contacto')
        telefono = request.POST.get('telefono')
        email = request.POST.get('email')
        direccion = request.POST.get('direccion')
        fecha_registro = request.POST.get('fecha_registro')
        ciudad = request.POST.get('ciudad')
        Proveedor.objects.create(
            nombre_empresa=nombre_empresa,
            contacto=contacto,
            telefono=telefono,
            email=email,
            direccion=direccion,
            fecha_registro=fecha_registro,
            ciudad=ciudad
        )
        return redirect('ver_proveedor')
    return render(request, 'proveedor/agregar_proveedor.html')

def ver_proveedor(request):
    proveedores = Proveedor.objects.all()
    return render(request, 'proveedor/ver_proveedor.html', {'proveedores': proveedores})

def actualizar_proveedor(request, id_proveedor):
    proveedor = get_object_or_404(Proveedor, id_proveedor=id_proveedor)
    if request.method == 'POST':
        proveedor.nombre_empresa = request.POST.get('nombre_empresa')
        proveedor.contacto = request.POST.get('contacto')
        proveedor.telefono = request.POST.get('telefono')
        proveedor.email = request.POST.get('email')
        proveedor.direccion = request.POST.get('direccion')
        proveedor.fecha_registro = request.POST.get('fecha_registro')
        proveedor.ciudad = request.POST.get('ciudad')
        proveedor.save()
        return redirect('ver_proveedor')
    return render(request, 'proveedor/actualizar_proveedor.html', {'proveedor': proveedor})

def borrar_proveedor(request, id_proveedor):
    proveedor = get_object_or_404(Proveedor, id_proveedor=id_proveedor)
    if request.method == 'POST':
        proveedor.delete()
        return redirect('ver_proveedor')
    return render(request, 'proveedor/borrar_proveedor.html', {'proveedor': proveedor})
```

### 15. Crear la carpeta `templates` dentro de `app_Paleteria`

Ruta: `app_Paleteria/templates/` como está en la estructura.

### 16. Archivos HTML en `templates` (`base.html`, `header.html`, `navbar.html`, `footer.html`, `inicio.html`)

Voy a dar plantillas básicas. Asegúrate de crear la subcarpeta `proveedor` y los archivos de CRUD ahí (ver punto 22).

`app_Paleteria/templates/base.html`

```html
<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>{% block title %}Sistema Paletería{% endblock %}</title>
  <!-- Bootstrap CSS (CDN) -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
  {% include 'header.html' %}
  {% include 'navbar.html' %}
  <main class="container my-4">
    {% block content %}{% endblock %}
  </main>
  {% include 'footer.html' %}
  <!-- Bootstrap JS (CDN) -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

`header.html` (simple)

```html
<header class="py-3 border-bottom">
  <div class="container">
    <h1 class="h4">Sistema de Administración Paletería</h1>
  </div>
</header>
```

`navbar.html` (menú principal con submenus; solo iconos en opciones principales)

```html
<nav class="navbar navbar-expand-lg navbar-light bg-white border-bottom">
  <div class="container">
    <a class="navbar-brand" href="{% url 'inicio_paleteria' %}">🍦 Paletería</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navMenu">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navMenu">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">Proveedor</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="{% url 'agregar_proveedor' %}">Agregar Proveedor</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_proveedor' %}">Ver Proveedor</a></li>
          </ul>
        </li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#">Producto</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar producto</a></li>
            <li><a class="dropdown-item" href="#">Ver producto</a></li>
          </ul>
        </li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#">Cliente</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar cliente</a></li>
            <li><a class="dropdown-item" href="#">Ver cliente</a></li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

`footer.html`

```html
<footer class="bg-white border-top fixed-bottom">
  <div class="container py-2 text-center small">
    © {{ now.year }} - Creado por Alumno Estefania Gonzalez, Cbtis 128
  </div>
</footer>
```

> Para `{{ now.year }}` necesitarás agregar `django.template.context_processors.request` o usar `now` template tag: `{% load tz %}` then `{% now "Y" %}`. Puedes también poner la fecha manual.

`inicio.html`

```html
{% extends 'base.html' %}
{% block title %}Inicio - Paletería{% endblock %}
{% block content %}
  <div class="text-center">
    <h2>Bienvenido al sistema de administración Paletería</h2>
    <p>Información del sistema y una imagen representativa</p>
    <img src="https://images.unsplash.com/photo-1582719478180-1c3f646d9b4d" alt="paletas" class="img-fluid rounded" style="max-width:400px;">
  </div>
{% endblock %}
```

---

### 21. Crear subcarpeta `proveedor` dentro de `templates` (ya en la estructura)

### 22. Archivos HTML de proveedor (ejemplos básicos)

`proveedor/agregar_proveedor.html`

```html
{% extends 'base.html' %}
{% block content %}
<h3>Agregar Proveedor</h3>
<form method="post">
  {% csrf_token %}
  <div class="mb-3">
    <label>Nombre empresa</label>
    <input class="form-control" name="nombre_empresa" required>
  </div>
  <!-- repetir campos -->
  <button class="btn btn-primary" type="submit">Guardar</button>
</form>
{% endblock %}
```

`proveedor/ver_proveedor.html` (tabla con botones ver/editar/borrar)

```html
{% extends 'base.html' %}
{% block content %}
<h3>Proveedores</h3>
<table class="table table-striped">
  <thead><tr><th>Empresa</th><th>Contacto</th><th>Teléfono</th><th>Acciones</th></tr></thead>
  <tbody>
    {% for p in proveedores %}
    <tr>
      <td>{{ p.nombre_empresa }}</td>
      <td>{{ p.contacto }}</td>
      <td>{{ p.telefono }}</td>
      <td>
        <a class="btn btn-info btn-sm" href="{% url 'actualizar_proveedor' p.id_proveedor %}">Editar</a>
        <a class="btn btn-danger btn-sm" href="{% url 'borrar_proveedor' p.id_proveedor %}">Borrar</a>
      </td>
    </tr>
    {% empty %}
    <tr><td colspan="4">No hay proveedores</td></tr>
    {% endfor %}
  </tbody>
</table>
{% endblock %}
```

`proveedor/actualizar_proveedor.html` y `borrar_proveedor.html` siguen patrón similar (form para POST / confirmación).

---

### 23. No utilizar `forms.py` — ya respetado en vistas.

### 24. `urls.py` en `app_Paleteria`

`app_Paleteria/urls.py`

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_paleteria, name='inicio_paleteria'),
    path('proveedor/agregar/', views.agregar_proveedor, name='agregar_proveedor'),
    path('proveedor/ver/', views.ver_proveedor, name='ver_proveedor'),
    path('proveedor/actualizar/<int:id_proveedor>/', views.actualizar_proveedor, name='actualizar_proveedor'),
    path('proveedor/borrar/<int:id_proveedor>/', views.borrar_proveedor, name='borrar_proveedor'),
]
```

### 25. Agregar `app_Paleteria` en `settings.py` de `backend_paleteria`

En `backend_paleteria/settings.py`, dentro de `INSTALLED_APPS`:

```python
INSTALLED_APPS = [
    # apps por defecto ...
    'app_Paleteria',
]
```

### 26. Configurar `urls.py` de `backend_paleteria` para enlazar con `app_Paleteria`

`backend_paleteria/urls.py`

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Paleteria.urls')),  # ruta raíz va a la app
]
```

### 27. Registrar modelos en `admin.py` y volver a migrar

`app_Paleteria/admin.py`

```python
from django.contrib import admin
from .models import Proveedor, Producto, Cliente

admin.site.register(Proveedor)
admin.site.register(Producto)
admin.site.register(Cliente)
```

Luego:

```bash
python manage.py makemigrations
python manage.py migrate
```

Crear superusuario si quieres acceder a admin:

```bash
python manage.py createsuperuser
```

> Recuerde: por el momento trabajamos solo con “proveedor”; productos y clientes quedan pendientes.

---

### 28. Estética: usar colores suaves y diseño simple

Usa Bootstrap y clases (ej.: `bg-light`, `text-muted`, `btn-outline-primary`) en tus plantillas para tonos suaves. Mantén HTML sencillo y limpio.

### 29. Crear la estructura completa de carpetas y archivos al inicio

Ya está definida arriba; crea carpetas con `mkdir` y archivos con el editor o `touch`:

```bash
mkdir -p app_Paleteria/templates/proveedor
mkdir -p app_Paleteria/static/css
# crear archivos:
code app_Paleteria/models.py app_Paleteria/views.py app_Paleteria/urls.py app_Paleteria/templates/base.html
```

### 30. Proyecto totalmente funcional (resumen de pasos finales)

1. Activar `.venv`.
2. `pip install django`.
3. `django-admin startproject backend_paleteria .`
4. `python manage.py startapp app_Paleteria`
5. Añadir `app_Paleteria` a `INSTALLED_APPS`.
6. Colocar `models.py`, `views.py`, `urls.py`, templates.
7. `python manage.py makemigrations` → `python manage.py migrate`
8. `python manage.py runserver 8036`

### 31. Ejecutar servidor en el puerto 8036

(ya antes) — repetir:

```bash
python manage.py runserver 8036
```

---

## Tips rápidos / errores comunes

* Si `runserver` falla por puerto ocupado, usa `python manage.py runserver 0.0.0.0:8036`.
* Si `startproject` te crea una carpeta extra, borra la carpeta interna y vuelve a ejecutar con `.`.
* Asegúrate de activar el entorno virtual antes de instalar paquetes.
* Si tienes errores por migraciones, borra `migrations` (solo si es seguro) y ejecuta `makemigrations` otra vez.

---

Si quieres, ahora **te doy**:

* los archivos HTML completos para `actualizar_proveedor.html` y `borrar_proveedor.html`,
* o el código completo de `views.py` listo para copiar (ya te di esqueleto),
* o el `settings.py` mínimo que necesitas (con `STATIC` y `TEMPLATES`) — dime cuál prefieres y lo pego directo para que copies y pegues.

