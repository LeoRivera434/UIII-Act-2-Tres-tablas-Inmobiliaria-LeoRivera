🌟 # Guía paso a paso para tu Proyecto "Agencia" con Django
1️⃣ Crear la carpeta del Proyecto:

🗂️ Primero, crea la carpeta donde guardarás tu proyecto. Abre la terminal y ejecuta:

mkdir UIII_Agencia_0330

2️⃣ Abrir VS Code sobre la carpeta UIII_Agencia_0330:

👨‍💻 Abre VS Code desde la terminal en esa misma carpeta:

cd UIII_Agencia_0330
code .

3️⃣ Abrir terminal en VS Code:

🔑 Para abrir la terminal, ve a Terminal > New Terminal desde el menú de VS Code. ¡Así podrás ejecutar todos los comandos!

4️⃣ Crear entorno virtual .venv:

🛠️ Para crear el entorno virtual, ejecuta este comando en tu terminal:

python -m venv .venv

5️⃣ Activar el entorno virtual:

🚀 Activa tu entorno virtual con:

En Windows:

.venv\Scripts\activate


En MacOS/Linux:

source .venv/bin/activate

6️⃣ Activar el intérprete de Python en VS Code:

⚙️ Abre la paleta de comandos en VS Code (presiona Ctrl + Shift + P), luego busca y selecciona: Python: Select Interpreter. ¡Elige el de .venv!

7️⃣ Instalar Django:

📦 Con el entorno virtual activado, instala Django con:

pip install django

8️⃣ Crear el Proyecto Backend:

🌐 Crea el proyecto Django (sin duplicar la carpeta) con:

django-admin startproject backend_Agencia .

9️⃣ Ejecutar el servidor en el puerto 8030:

🖥️ Ejecuta el servidor con:

python manage.py runserver 8030

🔟 Ver en el navegador:

🌍 Copia y pega el link que aparece en la terminal (por ejemplo: http://127.0.0.1:8030/
) en tu navegador para ver el proyecto en acción.

1️⃣1️⃣ Crear la Aplicación app_Agencia:

🚀 Crea la app que manejará toda la lógica de tu agencia:

python manage.py startapp app_Agencia

1️⃣2️⃣ Modelo models.py para "Clientes" (Ya lo tienes):

🔧 Aquí está el código que debes poner en el archivo models.py de tu aplicación app_Agencia para definir los modelos de Clientes, Casas y Pagos:

# Código de los modelos (lo has hecho perfecto 😎)

1️⃣2.5️⃣ Realizar migraciones (makemigrations y migrate):

🛠️ Ejecuta estos dos comandos en tu terminal para que Django cree la base de datos:

Crear migraciones:

python manage.py makemigrations


Aplicar migraciones:

python manage.py migrate

1️⃣3️⃣ Trabajar con el modelo CLIENTES:

✅ Ya tienes el modelo de Clientes en models.py. Ahora toca trabajar con las vistas.

1️⃣4️⃣ Crear funciones en views.py:

📄 En el archivo views.py de app_Agencia, crea las siguientes funciones para manejar operaciones CRUD en Clientes:

from django.shortcuts import render, redirect
from .models import Cliente

# Función de inicio
def inicio_agencia(request):
    return render(request, 'inicio.html')

# Agregar un cliente
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

# Actualizar un cliente
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

# Borrar un cliente
def borrar_cliente(request, cliente_id):
    cliente = Cliente.objects.get(id=cliente_id)
    if request.method == 'POST':
        cliente.delete()
        return redirect('ver_clientes')
    return render(request, 'clientes/borrar_cliente.html', {'cliente': cliente})

# Ver todos los clientes
def ver_clientes(request):
    clientes = Cliente.objects.all()
    return render(request, 'clientes/ver_clientes.html', {'clientes': clientes})

1️⃣5️⃣ Crear la estructura de carpetas y archivos HTML:

🗂️ Dentro de app_Agencia/templates/, crea las siguientes carpetas y archivos:

mkdir -p app_Agencia/templates/clientes


Los archivos principales que debes crear son:

base.html (con Bootstrap):

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


navbar.html (menú de navegación):

<ul class="nav">
    <li class="nav-item"><a href="/" class="nav-link">Inicio</a></li>
    <li class="nav-item"><a href="/clientes" class="nav-link">Clientes</a></li>
    <li class="nav-item"><a href="/casas" class="nav-link">Casas</a></li>
    <li class="nav-item"><a href="/pagos" class="nav-link">Pagos</a></li>
</ul>


footer.html (footer fijo al final):

<footer class="fixed-bottom text-center py-3">
    <p>&copy; 2025. Creado por Leonardo Rivera Cbtis 128</p>
</footer>

1️⃣6️⃣ Crear la subcarpeta clientes y los archivos HTML:

🖥️ Dentro de app_Agencia/templates/clientes/, crea estos archivos:

agregar_cliente.html

ver_clientes.html (con tabla de clientes)

actualizar_cliente.html

borrar_cliente.html

1️⃣7️⃣ Configurar urls.py de app_Agencia:

🔗 En app_Agencia/urls.py, configura las rutas:

from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_agencia, name='inicio_agencia'),
    path('clientes/', views.ver_clientes, name='ver_clientes'),
    path('clientes/agregar/', views.agregar_cliente, name='agregar_cliente'),
    path('clientes/actualizar/<int:cliente_id>/', views.actualizar_cliente, name='actualizar_cliente'),
    path('clientes/borrar/<int:cliente_id>/', views.borrar_cliente, name='borrar_cliente'),
]

1️⃣8️⃣ Agregar app_Agencia en settings.py:

🧑‍💻 En el archivo settings.py de backend_Agencia, agrega tu aplicación en la lista INSTALLED_APPS:

INSTALLED_APPS = [
    # Otras apps
    'app_Agencia',
]

1️⃣9️⃣ Configurar urls.py de backend_Agencia:

🌍 En el archivo urls.py de backend_Agencia, conecta las URLs de app_Agencia:

from django.urls import path, include

urlpatterns = [
    path('', include('app_Agencia.urls')),
]

2️⃣0️⃣ ¡Finalizar y Ejecutar!

🎉 ¡Ahora que todo está listo, ejecuta tu servidor en el puerto 8030 con:

python manage.py runserver 8030


¡Listo! ¡Tienes un proyecto de agencia totalmente funcional! 🎉 Si tienes alguna duda o quieres más detalles
