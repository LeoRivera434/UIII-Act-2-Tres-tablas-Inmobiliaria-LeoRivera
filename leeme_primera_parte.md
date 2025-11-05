1. Procedimiento para crear carpeta del Proyecto:

Primero, crea la carpeta para tu proyecto:

Abre tu terminal y navega hasta el directorio donde quieras crear el proyecto.

Ejecuta el siguiente comando:

mkdir UIII_Agencia_0330

2. Procedimiento para abrir VS Code sobre la carpeta UIII_Agencia_0330:

En la terminal, navega a la carpeta del proyecto:

cd UIII_Agencia_0330


Luego, ejecuta:

code .


Esto abrirá VS Code en la carpeta UIII_Agencia_0330.

3. Procedimiento para abrir terminal en VS Code:

En VS Code, abre el terminal desde el menú superior: Terminal > New Terminal.

4. Procedimiento para crear entorno virtual .venv:

En la terminal de VS Code, ejecuta el siguiente comando para crear un entorno virtual:

python -m venv .venv

5. Procedimiento para activar el entorno virtual:

En la terminal, activa el entorno virtual:

En Windows:

.venv\Scripts\activate


En MacOS/Linux:

source .venv/bin/activate

6. Procedimiento para activar el intérprete de Python:

Abre la paleta de comandos de VS Code (Ctrl + Shift + P).

Busca "Python: Select Interpreter" y selecciona el intérprete dentro de la carpeta .venv.

7. Procedimiento para instalar Django:

Con el entorno virtual activado, instala Django con:

pip install django

8. Procedimiento para crear el proyecto backend_Agencia sin duplicar carpeta:

En la terminal de VS Code, ejecuta:

django-admin startproject backend_Agencia .

9. Procedimiento para ejecutar el servidor en el puerto 8030:

Ejecuta el servidor con:

python manage.py runserver 8030

10. Procedimiento para copiar y pegar el link en el navegador:

Copia el link que aparece en la terminal (http://127.0.0.1:8030/
) y pégalo en tu navegador.

11. Procedimiento para crear la aplicación app_Agencia:

En la terminal, ejecuta:

python manage.py startapp app_Agencia

12. Modelo models.py (ya proporcionado):

Este código es el que se va a agregar dentro del archivo models.py de la aplicación app_Agencia, lo cual ya has definido correctamente.

Procedimientos restantes:
12.5 Procedimiento para realizar las migraciones:

Primero ejecuta makemigrations para crear las migraciones:

python manage.py makemigrations


Luego, ejecuta migrate para aplicar las migraciones:

python manage.py migrate

13. Trabajar con el modelo CLIENTES:

Ya tienes el modelo de clientes, así que puedes comenzar con las vistas.

14. Crear funciones en views.py:

En el archivo views.py de app_Agencia, crea las siguientes funciones:

from django.shortcuts import render, redirect
from .models import Cliente
from .forms import ClienteForm  # si decidieras usar formularios más adelante

def inicio_agencia(request):
    return render(request, 'inicio.html')

def agregar_cliente(request):
    if request.method == 'POST':
        nombre = request.POST['nombre_completo']
        telefono = request.POST['telefono']
        correo = request.POST['correo']
        direccion = request.POST['direccion']
        Cliente.objects.create(nombre_completo=nombre, telefono=telefono, correo=correo, direccion=direccion)
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

15-17. Estructura de la carpeta templates:

Crea las carpetas y archivos dentro de app_Agencia/templates/:

mkdir -p app_Agencia/templates/clientes


Dentro de templates, crea los siguientes archivos:

base.html (con Bootstrap incluido para CSS y JS):

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Agencia</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <header>{% include 'header.html' %}</header>
    <nav>{% include 'navbar.html' %}</nav>
    <div class="container mt-4">{% block content %}{% endblock %}</div>
    <footer>{% include 'footer.html' %}</footer>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>


header.html:

<h1>Bienvenido a la Agencia</h1>


navbar.html:

<ul class="nav">
    <li class="nav-item"><a href="/" class="nav-link">Inicio</a></li>
    <li class="nav-item"><a href="/clientes" class="nav-link">Clientes</a></li>
    <li class="nav-item"><a href="/casas" class="nav-link">Casas</a></li>
    <li class="nav-item"><a href="/pagos" class="nav-link">Pagos</a></li>
</ul>


footer.html:

<footer class="fixed-bottom text-center py-3">
    <p>&copy; 2025. Creado por Leonardo Rivera Cbtis 128</p>
</footer>

18. Subcarpeta clientes dentro de templates:

Ya la creaste antes, solo asegúrate de colocar los siguientes archivos:

agregar_cliente.html

ver_clientes.html

actualizar_cliente.html

borrar_cliente.html

19. Configurar URLs:

En el archivo urls.py de app_Agencia:

from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_agencia, name='inicio_agencia'),
    path('clientes/', views.ver_clientes, name='ver_clientes'),
    path('clientes/agregar/', views.agregar_cliente, name='agregar_cliente'),
    path('clientes/actualizar/<int:cliente_id>/', views.actualizar_cliente, name='actualizar_cliente'),
    path('clientes/borrar/<int:cliente_id>/', views.borrar_cliente, name='borrar_cliente'),
]


Asegúrate de agregar app_Agencia en el settings.py bajo INSTALLED_APPS y configura urls.py de backend_Agencia para enlazar.
