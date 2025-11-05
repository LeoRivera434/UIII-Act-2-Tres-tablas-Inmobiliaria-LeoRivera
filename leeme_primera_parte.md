# 1️⃣ Crear la carpeta del Proyecto
🗂️ Primero, crea la carpeta donde guardarás tu proyecto. Abre la terminal y ejecuta:
mkdir UIII_Agencia_0330

# 2️⃣ Abrir VS Code sobre la carpeta UIII_Agencia_0330
👨‍💻 Abre VS Code desde la terminal en esa misma carpeta:
cd UIII_Agencia_0330  
code .

# 3️⃣ Abrir terminal en VS Code
🔑 Para abrir la terminal, ve a Terminal > New Terminal desde el menú de VS Code. ¡Así podrás ejecutar todos los comandos!

# 4️⃣ Crear entorno virtual .venv
🛠️ Para crear el entorno virtual, ejecuta este comando en tu terminal:
python -m venv .venv

# 5️⃣ Activar el entorno virtual
🚀 Activa tu entorno virtual con:
En Windows:
.venv\Scripts\activate

En MacOS/Linux:
source .venv/bin/activate

# 6️⃣ Activar el intérprete de Python en VS Code
⚙️ Abre la paleta de comandos en VS Code (Ctrl + Shift + P), luego busca y selecciona:
Python: Select Interpreter → selecciona el de .venv

# 7️⃣ Instalar Django
📦 Con el entorno virtual activado, instala Django con:
pip install django

# 8️⃣ Crear el Proyecto Backend
🌐 Crea el proyecto Django (sin duplicar la carpeta) con:
django-admin startproject backend_Agencia .

# 9️⃣ Ejecutar el servidor en el puerto 8030
🖥️ Ejecuta el servidor con:
python manage.py runserver 8030

# 🔟 Ver en el navegador
🌍 Copia y pega el link que aparece en la terminal (por ejemplo: http://127.0.0.1:8030/) en tu navegador para ver el proyecto en acción.

# 1️⃣1️⃣ Crear la Aplicación app_Agencia
🚀 Crea la app que manejará toda la lógica de tu agencia:
python manage.py startapp app_Agencia

# 1️⃣2️⃣ Modelo models.py para "Clientes", "Casas" y "Pagos"
🔧 Código completo para app_Agencia/models.py:

from django.db import models

# ==========================================
# MODELO: CLIENTES
# ==========================================
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


# ==========================================
# MODELO: CASAS
# ==========================================
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


# ==========================================
# MODELO: PAGOS
# ==========================================
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

# 1️⃣2.5️⃣ Procedimiento para realizar las migraciones
🛠️ Ejecuta:
python manage.py makemigrations
python manage.py migrate

# 1️⃣3️⃣ Primero trabajamos con el MODELO: CLIENTES
✅ Ya tienes el modelo de Clientes en models.py.

# 1️⃣4️⃣ En views.py de app_Agencia crear las funciones con sus códigos correspondientes

from django.shortcuts import render, redirect
from .models import Cliente

def inicio_agencia(request):
    return render(request, 'inicio.html')

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

def ver_clientes(request):
    clientes = Cliente.objects.all()
    return render(request, 'clientes/ver_clientes.html', {'clientes': clientes})

# 1️⃣5️⃣ Crear la carpeta “templates” dentro de “app_Agencia”
mkdir -p app_Agencia/templates

# 1️⃣6️⃣ Crear los archivos HTML (base.html, header.html, navbar.html, footer.html, inicio.html)
📄 Código de cada uno:

# base.html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Agencia</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
    {% include 'navbar.html' %}
    <div class="container mt-4">
        {% block content %}{% endblock %}
    </div>
    {% include 'footer.html' %}
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>

# navbar.html
<nav class="navbar navbar-expand-lg navbar-dark bg-primary">
  <div class="container-fluid">
    <a class="navbar-brand" href="/">Agencia Inmobiliaria</a>
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

# footer.html
<footer class="text-center py-3 mt-5 bg-dark text-white">
    <p>© {{ now|date:"Y" }} Agencia Inmobiliaria. Todos los derechos reservados.</p>
</footer>

# inicio.html
{% extends 'base.html' %}
{% block content %}
<div class="text-center">
    <h1 class="text-primary">Bienvenido al Sistema de Agencia Inmobiliaria</h1>
    <p class="mt-3">Gestiona tus clientes, casas y pagos de forma sencilla y moderna.</p>
    <img src="https://img.freepik.com/free-vector/real-estate-agency-concept-illustration_114360-9001.jpg" class="img-fluid mt-4 rounded shadow" width="600">
</div>
{% endblock %}

# 2️⃣1️⃣ Crear la subcarpeta “clientes” dentro de app_Agencia/templates
mkdir -p app_Agencia/templates/clientes

# 2️⃣2️⃣ Crear los archivos html con su código correspondiente

# agregar_cliente.html
{% extends 'base.html' %}
{% block content %}
<h2 class="text-center text-success">Agregar Cliente</h2>
<form method="post" class="p-3 bg-white rounded shadow">
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

# ver_clientes.html
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

# actualizar_cliente.html
{% extends 'base.html' %}
{% block content %}
<h2 class="text-center text-warning">Actualizar Cliente</h2>
<form method="post" class="p-3 bg-white rounded shadow">
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

# borrar_cliente.html
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

# 2️⃣3️⃣ No utilizar forms.py
⚠️ No es necesario utilizar forms.py.

# 2️⃣4️⃣ urls.py en app_Agencia

from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_agencia, name='inicio_agencia'),
    path('clientes/', views.ver_clientes, name='ver_clientes'),
    path('clientes/agregar/', views.agregar_cliente, name='agregar_cliente'),
    path('clientes/actualizar/<int:cliente_id>/', views.actualizar_cliente, name='actualizar_cliente'),
    path('clientes/borrar/<int:cliente_id>/', views.borrar_cliente, name='borrar_cliente'),
]

# 2️⃣5️⃣ Agregar app_Agencia en settings.py
En backend_Agencia/settings.py:
INSTALLED_APPS = [
    ...,
    'app_Agencia',
]

# 2️⃣6️⃣ Configurar urls.py de backend_Agencia
En backend_Agencia/urls.py:

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Agencia.urls')),
]

# 2️⃣7️⃣ Registrar modelos en admin.py
from django.contrib import admin
from .models import Cliente, Casa, Pago

admin.site.register(Cliente)
admin.site.register(Casa)
admin.site.register(Pago)

python manage.py makemigrations
python manage.py migrate

# 2️⃣8️⃣ Utilizar colores suaves y modernos
🎨 Ya aplicado con Bootstrap.

# 2️⃣9️⃣ No validar entrada de datos
⚠️ No se validan datos.

# 3️⃣0️⃣ Proyecto totalmente funcional
✅ CRUD completo de Clientes con interfaz.

# 3️⃣1️⃣ Finalmente ejecutar el servidor en el puerto 8030
python manage.py runserver 8030
 correctamente en tu puerto 8030.
