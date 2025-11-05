🌟 Guía Completa para Crear un Proyecto de Agencia con Django 🌟

Estructura del Proyecto 📂
La estructura inicial de tu proyecto será algo como esto:
Curso_Django/
└── UIII_Agencia_0330/
    ├── .venv/                       # Entorno virtual
    ├── backend_Agencia/             # Configuración del proyecto
    │   ├── __init__.py
    │   ├── settings.py
    │   ├── urls.py
    │   └── wsgi.py
    ├── app_Agencia/                 # La aplicación de la Agencia
    │   ├── __init__.py
    │   ├── models.py                # Modelos de la agencia (Clientes, Casas, Pagos)
    │   ├── admin.py                 # Configuración para el Admin de Django
    │   ├── forms.py                 # Formularios si decides usarlos
    │   ├── views.py                 # Vistas de la aplicación
    │   ├── urls.py                  # Rutas de la aplicación
    │   ├── templates/               # Plantillas HTML
    │   │   ├── base.html            # Estructura base de las páginas
    │   │   ├── header.html          # Cabecera
    │   │   ├── navbar.html          # Barra de navegación
    │   │   ├── footer.html          # Pie de página
    │   │   ├── inicio.html          # Página de inicio
    │   │   └── clientes/            # Plantillas específicas de clientes
    │   │       ├── agregar_cliente.html
    │   │       ├── ver_clientes.html
    │   │       ├── actualizar_cliente.html
    │   │       └── borrar_cliente.html
    │   └── static/                  # Archivos estáticos (CSS, imágenes)
    │       └── css/
    │           └── styles.css       # Estilos personalizados
    ├── media/                       # Imágenes subidas (creado automáticamente)
    ├── manage.py                    # Archivo principal de gestión
    └── requirements.txt             # Dependencias del proyecto


📝 Pasos para Crear tu Proyecto de Agencia con Django 📝
1️⃣ Crear la Carpeta del Proyecto 🗂️
Primero, crea la carpeta para tu proyecto de Agencia:
mkdir UIII_Agencia_0330


2️⃣ Abrir VS Code sobre la Carpeta UIII_Agencia_0330 💻
Abre VS Code en esa carpeta:
cd UIII_Agencia_0330
code .


3️⃣ Abrir Terminal en VS Code 🖥️
En VS Code, abre la terminal desde el menú: Terminal > New Terminal.

4️⃣ Crear Entorno Virtual .venv 🌱
Crea el entorno virtual ejecutando:
python -m venv .venv


5️⃣ Activar el Entorno Virtual 🚀
Activa el entorno virtual con:


Windows:
.venv\Scripts\activate



MacOS/Linux:
source .venv/bin/activate




6️⃣ Activar el Intérprete de Python en VS Code 🔧
Abre la paleta de comandos (Ctrl + Shift + P) y selecciona Python: Select Interpreter. Luego, elige el intérprete dentro de la carpeta .venv.

7️⃣ Instalar Django 📦
Con el entorno virtual activado, instala Django:
pip install django


8️⃣ Crear Proyecto Backend (backend_Agencia) ⚙️
En la terminal, crea el proyecto Django sin duplicar la carpeta:
django-admin startproject backend_Agencia .


9️⃣ Ejecutar el Servidor en el Puerto 8030 🌍
Ejecuta el servidor en el puerto 8030:
python manage.py runserver 8030


🔟 Ver Proyecto en el Navegador 🌐
Copia y pega el siguiente enlace en tu navegador:
http://127.0.0.1:8030/

1️⃣1️⃣ Crear Aplicación app_Agencia 📲
Crea la aplicación app_Agencia:
python manage.py startapp app_Agencia


1️⃣2️⃣ Configurar Modelos en models.py 📝
En app_Agencia/models.py, agrega los modelos de Clientes, Casas, y Pagos. Este es tu código para los modelos:
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


1️⃣2.5️⃣ Realizar Migraciones 🛠️
Para aplicar los cambios en la base de datos:


Crea las migraciones:


python manage.py makemigrations



Aplica las migraciones:


python manage.py migrate


1️⃣3️⃣ Trabajar con el Modelo CLIENTES 👥
Ya tienes el modelo de Clientes. Ahora es el momento de crear las vistas para manejar los datos.

1️⃣4️⃣ Crear Funciones en views.py 🎨
En views.py de app_Agencia, crea las siguientes funciones:
from django.shortcuts import render, redirect
from .models import Cliente

# Vista de inicio
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


1️⃣5️⃣ Crear Plantillas HTML 🖥️
Dentro de app_Agencia/templates/, crea las plantillas:


base.html (estructura base con Bootstrap)


header.html, navbar.html, footer.html


Plantillas de clientes en app_Agencia/templates/clientes/:


agregar_cliente.html


ver_clientes.html


actualizar_cliente.html


borrar_cliente.html





1️⃣6️⃣ Configurar URLs en urls.py de app_Agencia 🔗
En urls.py de app_Agencia, agrega las rutas:
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_agencia, name='inicio_agencia'),
    path('clientes/', views.ver_clientes, name='ver_clientes'),
    path('clientes/agregar/', views.agregar_cliente, name='agregar_cliente'),
    path('clientes/actualizar/<int:cliente_id>/', views.actualizar_cliente, name='actualizar_cliente'),
    path('clientes/borrar/<int:cliente_id>/', views.borrar_cliente, name='borrar_cliente'),
]


1️⃣7️⃣ Registrar la Aplicación en settings.py 🛠️
En settings.py de backend_Agencia, agrega app_Agencia a INSTALLED_APPS:
INSTALLED_APPS = [
    # Otras apps
    'app_Agencia',
]


1️⃣8️⃣ Configurar URLs en urls.py de backend_Agencia 🌍
En urls.py de backend_Agencia, conecta las rutas de app_Agencia:
from django.urls import path, include

urlpatterns = [
    path('', include('app_Agencia.urls')),
]


1️⃣9️⃣ ¡Listo! Ejecutar el Servidor en el Puerto 8030 🚀
Finalmente, ejecuta el servidor:
python manage.py runserver 8030


¡Y listo! 🎉 Ahora tu proyecto de Agencia en Django está completamente funcional. Si necesitas más detalles o tienes dudas en alguna parte, ¡no dudes en preguntar! 😄
