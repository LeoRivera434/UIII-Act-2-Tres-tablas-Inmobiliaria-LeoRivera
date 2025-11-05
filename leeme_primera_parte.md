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
⚙️ Abre la paleta de comandos en VS Code (presiona Ctrl + Shift + P), luego busca y selecciona: Python: Select Interpreter. ¡Elige el de .venv!
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

# 1️⃣2️⃣ Modelo models.py para "Clientes"
🔧 Aquí está el código que debes poner en el archivo models.py de tu aplicación app_Agencia para definir los modelos de Clientes, Casas y Pagos:
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
    # Relación 1 a muchos: un cliente puede tener varias casas
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
    # Relación muchos a muchos: una casa puede tener varios pagos y un pago puede aplicarse a varias casas
    estatus_pago = models.CharField(max_length=50, default="Completado")

    def __str__(self):
        return f"Pago {self.referencia} - ${self.monto}"

# 1️⃣2.5️⃣ Procedimiento para realizar las migraciones (makemigrations y migrate)
🛠️ Ejecuta estos dos comandos en tu terminal para que Django cree la base de datos:
Crear migraciones:
python manage.py makemigrations

Aplicar migraciones:
python manage.py migrate

# 1️⃣3️⃣ Primero trabajamos con el MODELO: CLIENTES
✅ Ya tienes el modelo de Clientes en models.py. Ahora toca trabajar con las vistas.
# 1️⃣4️⃣ En views de app_Agencia crear las funciones con sus códigos correspondientes
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

# 1️⃣5️⃣ Crear la carpeta “templates” dentro de “app_Agencia”
🗂️ Dentro de app_Agencia, crea las carpetas necesarias para las plantillas:
mkdir -p app_Agencia/templates

# 1️⃣6️⃣ Crear los archivos HTML (base.html, header.html, navbar.html, footer.html, inicio.html)
📄 Dentro de app_Agencia/templates, crea los archivos base.html, header.html, navbar.html, footer.html e inicio.html como se describió antes.
# 1️⃣7️⃣ En el archivo base.html agregar Bootstrap para CSS y JS
✨ Ya se ha agregado el CDN de Bootstrap tanto para CSS como para JS dentro de base.html.
# 1️⃣8️⃣ En el archivo navbar.html incluir las opciones
📝 Agrega las opciones dentro del navbar.html (como se muestra arriba). Utiliza iconos con las clases de Bootstrap Icons.
# 1️⃣9️⃣ En el archivo footer.html incluir derechos de autor
🔏 Dentro de footer.html, mantén la información con los derechos de autor y la fecha del sistema.
# 2️⃣0️⃣ En el archivo inicio.html colocar información del sistema más una imagen
🖼️ Agrega en inicio.html una descripción del sistema y una imagen de agencia tomada desde la web.
# 2️⃣1️⃣ Crear la subcarpeta “clientes” dentro de app_Agencia\templates
🗂️ Crea la subcarpeta clientes dentro de app_Agencia/templates:
mkdir -p app_Agencia/templates/clientes

# 2️⃣2️⃣ Crear los archivos html con su código correspondiente
📄 Los archivos para agregar_cliente.html, ver_clientes.html, actualizar_cliente.html, borrar_cliente.html se colocan en app_Agencia/templates/clientes.
agregar_cliente.html
{% extends 'base.html' %}

{% block content %}
<h2>Agregar Cliente</h2>
<form method="post">
    {% csrf_token %}
    <label for="nombre_completo">Nombre Completo:</label>
    <input type="text" name="nombre_completo" required>
    <label for="telefono">Teléfono:</label>
    <input type="text" name="telefono" required>
    <label for="correo">Correo:</label>
    <input type="email" name="correo" required>
    <label for="direccion">Dirección:</label>
    <input type="text" name="direccion" required>
    <button type="submit">Agregar</button>
</form>
{% endblock %}

# 2️⃣3️⃣ No utilizar forms.py
⚠️ No es necesario utilizar forms.py en este proyecto. Todo se maneja con el modelo directamente.
# 2️⃣4️⃣ Procedimiento para crear el archivo urls.py en app_Agencia
🔗 En app_Agencia/urls.py, configura las rutas para las vistas del CRUD de Clientes.
# 2️⃣5️⃣ Procedimiento para agregar app_Agencia en settings.py de backend_Agencia
🧑‍💻 En el archivo settings.py de backend_Agencia, agrega tu aplicación en la lista INSTALLED_APPS.
# 2️⃣6️⃣ Realizar las configuraciones correspondientes a urls.py de backend_Agencia
🌍 En el archivo urls.py de backend_Agencia, conecta las URLs de app_Agencia.
# 2️⃣7️⃣ Procedimiento para registrar los modelos en admin.py y volver a realizar las migraciones
💻 Registra tus modelos en admin.py para que se muestren en el panel de administración de Django. Luego, realiza las migraciones.
# 2️⃣8️⃣ Utilizar colores suaves, atractivos y modernos
🎨 El diseño debe tener colores suaves, atractivos y modernos, y el código de las páginas web debe ser sencillo.
# 2️⃣9️⃣ No validar entrada de datos
⚠️ En esta versión del proyecto, no se validan los datos ingresados por el usuario.
# 3️⃣0️⃣ Proyecto totalmente funcional
✅ Con todos los pasos anteriores completados, tendrás un proyecto totalmente funcional.
# 3️⃣1️⃣ Finalmente ejecutar el servidor en el puerto 8030
🎉 ¡Ahora ejecuta el servidor con:
python manage.py runserver 8030

¡Y listo! El proyecto estará corriendo correctamente en tu puerto 8030.
