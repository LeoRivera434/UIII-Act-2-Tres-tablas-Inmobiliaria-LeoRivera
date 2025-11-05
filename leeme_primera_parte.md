# ============================================================
# 🏠 PROYECTO DJANGO COMPLETO: "UIII_Agencia_0330"
# CRUD DE CLIENTES CON BOOTSTRAP
# ============================================================

# 1️⃣ Crear carpeta del proyecto 
```
mkdir UIII_Agencia_0330
cd UIII_Agencia_0330
code .
```
# 2️⃣ Crear entorno virtual
```
python -m venv .venv
```
# 3️⃣ Activar entorno virtual
# En Windows:
```
.venv\Scripts\activate
# En Mac/Linux:
source .venv/bin/activate
```
# 4️⃣ Instalar Django
```
pip install django
```
# 5️⃣ Crear el proyecto principal
```
django-admin startproject backend_Agencia .
```
# 6️⃣ Crear la aplicación
```
python manage.py startapp app_Agencia
```
# ============================================================
# ESTRUCTURA FINAL DE CARPETAS
# ============================================================
```
# UIII_Agencia_0330/
# ├── .venv/
# ├── backend_Agencia/
# │   ├── __init__.py
# │   ├── asgi.py
# │   ├── settings.py
# │   ├── urls.py
# │   └── wsgi.py
# ├── app_Agencia/
# │   ├── migrations/
# │   │   └── __init__.py
# │   ├── templates/
# │   │   ├── base.html
# │   │   ├── header.html
# │   │   ├── navbar.html
# │   │   ├── footer.html
# │   │   ├── inicio.html
# │   │   └── clientes/
# │   │       ├── agregar_cliente.html
# │   │       ├── ver_clientes.html
# │   │       ├── actualizar_cliente.html
# │   │       └── borrar_cliente.html
# │   ├── __init__.py
# │   ├── admin.py
# │   ├── apps.py
# │   ├── models.py
# │   ├── tests.py
# │   ├── urls.py
# │   └── views.py
# ├── manage.py
```
# ============================================================

# ============================================================
# 1️⃣2️⃣ MODELOS: app_Agencia/models.py
# ============================================================
```
from django.db import models

class Cliente(models.Model):
    nombre_completo = models.CharField(max_length=150)
    telefono = models.CharField(max_length=20)
    correo = models.EmailField(unique=True)
    direccion = models.CharField(max_length=150)
    fecha_registro = models.DateField(auto_now_add=True)
    estatus = models.CharField(max_length=50, default="Activo")
    tipo_cliente = models.CharField(max_length=50, default="Regular")
    observaciones = models.TextField(blank=True, null=True)

    def __str__(self):
        return self.nombre_completo


class Casa(models.Model):
    direccion = models.CharField(max_length=150)
    ciudad = models.CharField(max_length=100)
    precio = models.DecimalField(max_digits=10, decimal_places=2)
    tipo = models.CharField(max_length=50)
    estado = models.CharField(max_length=50)
    fecha_construccion = models.DateField()
    cliente = models.ForeignKey(Cliente, on_delete=models.CASCADE, related_name="casas")
    numero_registro = models.CharField(max_length=50, unique=True)

    def __str__(self):
        return f"{self.tipo} en {self.ciudad}"


class Pago(models.Model):
    monto = models.DecimalField(max_digits=10, decimal_places=2)
    fecha_pago = models.DateField()
    metodo = models.CharField(max_length=50)
    referencia = models.CharField(max_length=100, unique=True)
    descripcion = models.TextField(blank=True, null=True)
    comprobante = models.CharField(max_length=100, blank=True, null=True)
    casas = models.ManyToManyField(Casa, related_name="pagos")
    estatus_pago = models.CharField(max_length=50, default="Completado")

    def __str__(self):
        return f"Pago {self.referencia} - ${self.monto}"
```
# ============================================================
# 1️⃣4️⃣ VISTAS: app_Agencia/views.py
# ============================================================
```
from django.shortcuts import render, redirect
from .models import Cliente

def inicio_agencia(request):
    return render(request, 'inicio.html')

def ver_clientes(request):
    clientes = Cliente.objects.all()
    return render(request, 'clientes/ver_clientes.html', {'clientes': clientes})

def agregar_cliente(request):
    if request.method == 'POST':
        Cliente.objects.create(
            nombre_completo=request.POST['nombre_completo'],
            telefono=request.POST['telefono'],
            correo=request.POST['correo'],
            direccion=request.POST['direccion']
        )
        return redirect('ver_clientes')
    return render(request, 'clientes/agregar_cliente.html')

def actualizar_cliente(request, cliente_id):
    cliente = Cliente.objects.get(id=cliente_id)
    if request.method == 'POST':
        cliente.nombre_completo = request.POST['nombre_completo']
        cliente.telefono = request.POST['telefono']
        cliente.correo = request.POST['correo']
        cliente.direccion = request.POST['direccion']
        cliente.save()
        return redirect('ver_clientes')
    return render(request, 'clientes/actualizar_cliente.html', {'cliente': cliente})

def borrar_cliente(request, cliente_id):
    cliente = Cliente.objects.get(id=cliente_id)
    if request.method == 'POST':
        cliente.delete()
        return redirect('ver_clientes')
    return render(request, 'clientes/borrar_cliente.html', {'cliente': cliente})
```
# ============================================================
# 2️⃣4️⃣ URLs: app_Agencia/urls.py
# ============================================================
```
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_agencia, name='inicio_agencia'),
    path('clientes/', views.ver_clientes, name='ver_clientes'),
    path('clientes/agregar/', views.agregar_cliente, name='agregar_cliente'),
    path('clientes/actualizar/<int:cliente_id>/', views.actualizar_cliente, name='actualizar_cliente'),
    path('clientes/borrar/<int:cliente_id>/', views.borrar_cliente, name='borrar_cliente'),
]
```
# ============================================================
# 2️⃣6️⃣ URLs PRINCIPALES: backend_Agencia/urls.py
# ============================================================
```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Agencia.urls')),
]
```
# ============================================================
# 2️⃣5️⃣ Agregar la app en settings.py
# ============================================================
```
# En backend_Agencia/settings.py:
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Agencia',
]
```
# ============================================================
# 2️⃣7️⃣ Registrar modelos: app_Agencia/admin.py
# ============================================================
```
from django.contrib import admin
from .models import Cliente, Casa, Pago

admin.site.register(Cliente)
admin.site.register(Casa)
admin.site.register(Pago)
```
# ============================================================
# 1️⃣5️⃣ Y 1️⃣6️⃣ TEMPLATES
# ============================================================

# ==========================
# base.html
# ==========================
```
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Agencia Inmobiliaria</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
    {% include 'header.html' %}
    {% include 'navbar.html' %}
    <main class="container mt-4">
        {% block content %}{% endblock %}
    </main>
    {% include 'footer.html' %}
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```
# ==========================
# header.html
# ==========================
```
<header class="bg-primary text-white text-center py-3">
    <h1 class="fw-bold">Sistema Agencia Inmobiliaria</h1>
</header>
```
# ==========================
# navbar.html
# ==========================
```
<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
  <div class="container-fluid">
    <a class="navbar-brand" href="/">🏠 Agencia</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNav">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item"><a class="nav-link" href="/">Inicio</a></li>
        <li class="nav-item"><a class="nav-link" href="/clientes/">Ver Clientes</a></li>
        <li class="nav-item"><a class="nav-link" href="/clientes/agregar/">Agregar Cliente</a></li>
      </ul>
    </div>
  </div>
</nav>
```
# ==========================
# footer.html
# ==========================
```
<footer class="bg-primary text-white text-center py-3 mt-5">
    <p>© {{ now|date:"Y" }} Agencia Inmobiliaria. Todos los derechos reservados.</p>
</footer>
```
# ==========================
# inicio.html
# ==========================
```
{% extends 'base.html' %}
{% block content %}
<div class="text-center">
    <h2 class="text-primary">Bienvenido al Sistema de Gestión de Agencia</h2>
    <p class="mt-3">Administra tus clientes, casas y pagos fácilmente.</p>
    <img src="https://img.freepik.com/free-vector/real-estate-agency-concept-illustration_114360-9001.jpg" 
         alt="Agencia Inmobiliaria" class="img-fluid rounded shadow mt-3" width="600">
</div>
{% endblock %}
```
# ==========================
# clientes/agregar_cliente.html
# ==========================
```
{% extends 'base.html' %}
{% block content %}
<h2 class="text-center text-success mb-3">Agregar Cliente</h2>
<form method="post" class="bg-white p-4 rounded shadow">
    {% csrf_token %}
    <div class="mb-3">
        <label>Nombre Completo:</label>
        <input type="text" name="nombre_completo" class="form-control" required>
    </div>
    <div class="mb-3">
        <label>Teléfono:</label>
        <input type="text" name="telefono" class="form-control" required>
    </div>
    <div class="mb-3">
        <label>Correo:</label>
        <input type="email" name="correo" class="form-control" required>
    </div>
    <div class="mb-3">
        <label>Dirección:</label>
        <input type="text" name="direccion" class="form-control" required>
    </div>
    <button type="submit" class="btn btn-primary">Agregar</button>
</form>
{% endblock %}
```
# ==========================
# clientes/ver_clientes.html
# ==========================
```
{% extends 'base.html' %}
{% block content %}
<h2 class="text-center text-info">Lista de Clientes</h2>
<table class="table table-striped table-bordered mt-3">
    <thead class="table-primary">
        <tr>
            <th>ID</th>
            <th>Nombre</th>
            <th>Teléfono</th>
            <th>Correo</th>
            <th>Dirección</th>
            <th>Acciones</th>
        </tr>
    </thead>
    <tbody>
        {% for cliente in clientes %}
        <tr>
            <td>{{ cliente.id }}</td>
            <td>{{ cliente.nombre_completo }}</td>
            <td>{{ cliente.telefono }}</td>
            <td>{{ cliente.correo }}</td>
            <td>{{ cliente.direccion }}</td>
            <td>
                <a href="/clientes/actualizar/{{ cliente.id }}/" class="btn btn-sm btn-warning">Editar</a>
                <a href="/clientes/borrar/{{ cliente.id }}/" class="btn btn-sm btn-danger">Borrar</a>
            </td>
        </tr>
        {% endfor %}
    </tbody>
</table>
{% endblock %}
```
# ==========================
# clientes/actualizar_cliente.html
# ==========================
```
{% extends 'base.html' %}
{% block content %}
<h2 class="text-center text-warning mb-3">Actualizar Cliente</h2>
<form method="post" class="bg-white p-4 rounded shadow">
    {% csrf_token %}
    <div class="mb-3">
        <label>Nombre Completo:</label>
        <input type="text" name="nombre_completo" class="form-control" value="{{ cliente.nombre_completo }}">
    </div>
    <div class="mb-3">
        <label>Teléfono:</label>
        <input type="text" name="telefono" class="form-control" value="{{ cliente.telefono }}">
    </div>
    <div class="mb-3">
        <label>Correo:</label>
        <input type="email" name="correo" class="form-control" value="{{ cliente.correo }}">
    </div>
    <div class="mb-3">
        <label>Dirección:</label>
        <input type="text" name="direccion" class="form-control" value="{{ cliente.direccion }}">
    </div>
    <button type="submit" class="btn btn-success">Guardar Cambios</button>
</form>
{% endblock %}
```
# ==========================
# clientes/borrar_cliente.html
# ==========================
```
{% extends 'base.html' %}
{% block content %}
<h2 class="text-center text-danger">Eliminar Cliente</h2>
<p class="text-center">¿Seguro que deseas eliminar a <strong>{{ cliente.nombre_completo }}</strong>?</p>
<form method="post" class="text-center">
    {% csrf_token %}
    <button type="submit" class="btn btn-danger">Sí, eliminar</button>
    <a href="/clientes/" class="btn btn-secondary">Cancelar</a>
</form>
{% endblock %}
```
# ============================================================
# 1️⃣2.5️⃣ Migraciones
```
python manage.py makemigrations
python manage.py migrate
```

# ============================================================
# 3️⃣1️⃣ Ejecutar el servidor en puerto 8030
```
python manage.py runserver 8030
```
# Acceder en el navegador: http://127.0.0.1:8030/
# ============================================================

✅ ¡Proyecto Django Agencia funcional, completo, con CRUD de clientes y diseño Bootstrap moderno!
