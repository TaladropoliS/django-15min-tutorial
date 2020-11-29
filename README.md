# django-15min-tutorial
Tutorial de Django en 15 minutos

## Conocimientos previos

El tutorial requiere unos conocimientos básicos de **Python y HTML**.
Del mismo modo partirá de la base de que tienes **Python y Pip** instalado en el sistema.

Puedes encontrar cursos y tutoriales sobre HTML, CSS y Python en [www.jonvadillo.com](http://jonvadillo.com)

Si no dispones de conocimientos previos en Python o te gustaría consolidarlos, tienes un disponible el libro gratuito "Apende Python desde cero a experto" en [este enlace](https://leanpub.com/aprende-python) (también puedes acceder directamente al repositorio [desde aquí](https://github.com/jvadillo/aprende-python-desde-cero-a-experto)).

## Hands on!

Tan solo necesitas seguir los pasos descritos en esta sección para lograr desarrollar tu primera aplicación web con Django. 
Recuerda que como pre-requisito tienes que tener **Python y Pip** instalado en el sistema. ¡Happy Coding!

### PASO 1: Instala Django desde Pip 
Pip es un sistema de gestión de paquetes utilizado para instalar y administrar paquetes de software en Python. La instalación de Django es directa, simplemente escribe lo siguiente:

      pip install django

A partir de este momento podrás comenzar a crear aplicaciones con Django.


### PASO 2: Crea tu primer proyecto

Crea el proyecto Django utilizando la utilidad de administración `django-admin`:

      django-admin startproject empresaDjango
    
    
La estructura de ficheros resultante es la siguiente:
    
    ```
     project/
         manage.py
         project/
     	__init__.py
     	settings.py
     	urls.py
     	wsgi.py
    
    ```
    
    
   - **__init.py**: un archivo vacío para indicar a Python de que la carpeta es un paquete. 
   - **settings.py**: contiene la configuración del proyecto creado. 
   - **urls.py**: contiene la información para generar las URLs de nuestro proyecto.
   - **manage.py**: utilidad para interactuar con el proyecto.
   
   Inicia el servidor con el comando `python manage.py runserver` dentro del directorio del proyecto.

### PASO 3: Crea tu primera aplicación
Posicionate en el directorio del proyecto `$ cd <project_folder>`

Crea la aplicación ejecutando el siguiente comando:

`python manage.py startapp DjangoUniversityApp`

Dentro del directorio de la aplicación, crear un fichero llamado `urls.py` que utilizaremos para mapear las URLs de nuestra aplicación.

La estructura de ficheros resultante será la siguiente:

```
 project/
     manage.py
     db.sqlite3
     project/
 	__init__.py
 	settings.py
 	urls.py
 	wsgi.py
     app/
 	migrations/
 	    __init__.py
 	__init__.py
 	admin.py
 	apps.py
 	models.py
 	tests.py
 	urls.py
 	views.py

```

Incluir a aplicación dentro del fichero `settings.py` del proyecto, añadiendo el nombre a la lista `INSTALLED_APPS`:
	
	 INSTALLED_APPS = [
	 	'appEmpresaDjango',
	 	# ...
	 ]

### PASO 4: Crea una nueva vista y configura su ruta de acceso

Dentro del directorio de la app, abrir `views.py` y añadir lo siguiente:

```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Listado de departamentos")
```
    
Abrir (o crear) el fichero `urls.py` y añadir el patrón para la siguiente ruta:
   ```python
from django.urls import path
from . import views
   
urlpatterns = [
    path('', views.index, name='index'),
]
   ```
Dentro del directorio del proyecto, editar `urls.py` para incluir la redirección al fichero `urls.py` de la aplicación. El resultado debe ser el siguiente (no olvides importar la librería `include` de `django.urls`):
   ```python
from django.contrib import admin
from django.urls import include, path
  
urlpatterns = [
    path('appEmpresaDjango/', include('appEmpresaDjango.urls')),
    path('admin/', admin.site.urls),
]
   ```

De este modo tenemos un `urls.py` en cada directorio de nuestras aplicaciones gestionados desde el `urls.py` del directorio del proyecto.

### PASO 5: Crea el modelo de nuestra aplicación

Editar el fichero `models.py` de la aplicación creando las clases Departamento, Habilidad y Empleado:

   ```python
from django.db import models
    
class Student(models.Model):
	# No es necesario crear un campo para la Primary Key, Django creará automáticamente un IntegerField.
	nombre = models.CharField(max_length=50)
  apellidos = models.CharField(max_length=50)
	edad = models.IntegerField()
  email = models.EmailField()
```    
Aplica los cambios realizados en el modelo mediante los siguientes comandos:
    
   ```
python manage.py makemigrations appEmpresaDjango
python manage.py migrate   
   ```

### PASO 6: Añade y consulta registros desde la API de Django

Accede al intérprete interactivo de Django escribiendo el siguiente comando:

	python manage.py shell

Una vez dentro, ya puedes comenzar a crear y consultar registros.
   ```python
	>>> from appEmpresaDjango.models import Departamento, Habilidad, Empleado
	>>> departamento = Departamento(nombre="Oficina Tecnica", telefono="945010101")
	>>> departamento.save()
	# Django le asigna un id.
	>>> departamento.id
	1
	
	# Acceder a sus atributos
	>>> departamento.nombre
	'Oficina Tecnica'
	
	>>> Departamento.objects.all()
	<QuerySet [<Departamento: Departamento object (1)>]>
	
	>>> Departamento.objects.filter(nombre__contains='Oficina').count()
	1
	
	# Mostrar los empleados de un departamento.
	>>> departamento.empleado_set.all()
	<QuerySet []>
	
	# Crear empleados.
	>>> departamento.empleado_set.create(nombre='Mikel Uriarte', fecha_nacimiento='1985-01-23')
	<Empleado: Empleado object (1)>
	
	>>>> departamento.empleado_set.create(nombre='Ane Labastida', fecha_nacimiento='1987-12-14')
	<Empleado: Empleado object (2)>
	
	empleado = departamento.empleado_set.create(nombre='Luken Abarra', fecha_nacimiento='1989-05-12')
	
	# Es posible acceder desde el hijo a su padre.
	>>> empleado.departamento
	<Departamento: Departamento object (1)>
	
	>>> empleado.departamento.nombre
	'Oficina Tecnica'
	
	# También es posible acceder desde el padre a todos sus hijos.
	>>> departamento.empleado_set.all()
	<QuerySet [<Empleado: Empleado object (1)>, <Empleado: Empleado object (2)>, <Empleado: Empleado object (3)>]>
	
	>>>> departamento.empleado_set.count()
	3
	
	>>>> quit()
   ```

Es recomendable incluir un método ```__str__()``` dentro de cada clase de nuestro modelo para que sea invocado al llamar a los métodos *print* o *str* de nuestros objetos, que por ejemplo son usados internamente desde los formularios de la aplicación de administración que ofrece Django por defecto. De esta forma tendremos la posibilidad de mostrar objetos binarios como texto e identificarlos más fácilmente.

```python
def __str__(self):
        return self.nombre
```

### PASO 7: Utiliza la aplicación de Administración de Django para visualizar y añadir registros

La aplicación de administración permite visualizar la información de nuestros modelos de forma sencilla. Crear un usuario "adminDjango" (contraseña: "adminDjango") para la aplicación de administración:
    
    ```
     python manage.py createsuperuser
    
    ```
    
Editar el fichero `admin.py` para registrar las clases de nuestro modelo y que se puedan ver en la aplicación de administración por defecto que trae Django:
    
   ```
from django.contrib import admin
from .models import Student
admin.site.register(Student)
   ```

Iniciar el servidor y entrar
    
   ```
python manage.py runserver
   ```
    
Entrar en la aplicación [http://127.0.0.1:8000/admin](http://127.0.0.1:8000/admin) y añadir algunas entradas en cada entidad para tener un juego de ensayo que luego se pueda ver desde la aplicación [http://127.0.0.1:8000/appEmpresaDjango](http://127.0.0.1:8000/appEmpresaDjango)

### PASO 8: Crea las vistas y urls para interactuar con el modelo

Creamos las vistas correspondientes a las siguientes URLs:

| URL | Descripción de la vista |
| -- | -- |
| /appEmpresaDjango  | Muestra todos los departamentos  |
| /appEmpresaDjango/departamento/<:id>  | Muestra los detalles de un departamento a partir del ID indicado  |
| /appEmpresaDjango/departamento/<:id>/empleados  | Muestra los empleados del departamento con el ID indicado  |
| /appEmpresaDjango/empleado/<:id>  | Muestra los detalles del empleado con el ID indicado  |
| /appEmpresaDjango/habilidad/<:id>  | Muestra los detalles de la habilidad con el ID indicado  |

Creamos las vistas (cada vista estará definida por una función):
```python
from django.http import HttpResponse, Http404
from django.shortcuts import get_object_or_404, get_list_or_404
from .models import Departamento, Habilidad, Empleado

#devuelve el listado de empresas
def index(request):
	departamentos = Departamento.objects.order_by('nombre')
	output = ', '.join([d.nombre for d in departamentos])
	return HttpResponse(output)

#devuelve los datos de un departamento
def detail(request, departamento_id):
	departamento = Departamento.objects.get(pk=departamento_id)
	output = ', '.join([str(departamento.id), departamento.nombre, str(departamento.telefono)])
	return HttpResponse(output)

#devuelve los empleados de un departamento
def empleados(request, departamento_id):
	departamento = Departamento.objects.get(pk=departamento_id)
	output = ', '.join([e.nombre for e in departamento.empleado_set.all()])
	return HttpResponse(output)

#devuelve los detalles de un empleado
def empleado(request, empleado_id):
	empleado = Empleado.objects.get(pk=empleado_id)
	output = ', '.join([str(empleado.id), empleado.nombre, str(empleado.fecha_nacimiento), str(empleado.antiguedad), str(empleado.departamento), str([h.nombre for h in empleado.habilidades.all()])])
	return HttpResponse(output)

#devuelve los detalles de una habilidad
def habilidad(request, habilidad_id):
	habilidad = Habilidad.objects.get(pk=habilidad_id)
	output = ', '.join([str(habilidad.id), habilidad.nombre, str([e.nombre for e in habilidad.empleado_set.all()])])
	return HttpResponse(output)
```

Creamos las URLs que redirijan las peticiones a las vistas creadas:
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
    path('departamento/<int:departamento_id>/', views.detail, name='detail'),
    path('departamento/<int:departamento_id>/empleados', views.empleados, name='empleados'),
    path('empleado/<int:empleado_id>', views.empleado, name='empleado'),
    path('habilidad/<int:habilidad_id>', views.habilidad, name='habilidad')
]
```

### PASO 9 (opcional): Mejora las vistas controlando errores 404

Utiliza el método `get_object_or_404` para controlar los casos en los que el registro de la petición no exista:

```python
from django.http import HttpResponse, Http404
from django.shortcuts import get_object_or_404, get_list_or_404
from .models import Departamento, Habilidad, Empleado


#devuelve el listado de empresas
def index(request):
	departamentos = get_list_or_404(Departamento.objects.order_by('nombre'))
	output = ', '.join([d.nombre for d in departamentos])
	return HttpResponse(output)

#devuelve los datos de un departamento
def detail(request, departamento_id):
	departamento = Departamento.objects.get(pk=departamento_id)
	# output = ', '.join([str(departamento.id), departamento.nombre, str(departamento.telefono)])
	output = f"{departamento.id}, {departamento.nombre}, {departamento.telefono}"
	return HttpResponse(output)

#devuelve los empleados de un departamento
def empleados(request, departamento_id):
	departamento = get_object_or_404(Departamento, pk=departamento_id)
	output = ', '.join([e.nombre for e in departamento.empleado_set.all()])
	return HttpResponse(output)

#devuelve los detalles de un empleado
def empleado(request, empleado_id):
    empleado = Empleado.objects.get(pk=empleado_id)
    datos_empleado = f"{empleado.id}, {empleado.nombre}, {empleado.fecha_nacimiento}, {empleado.antiguedad}, {empleado.departamento.nombre}"
    habilidades = ', '.join([h.nombre for h in empleado.habilidades.all()])
    output = f"{datos_empleado} // Habilidades: {habilidades}"
    return HttpResponse(output)

#devuelve los detalles de una habilidad
def habilidad(request, habilidad_id):
	habilidad = Habilidad.objects.get(pk=habilidad_id)
	output = ', '.join([str(habilidad.id), habilidad.nombre])
	return HttpResponse(output)
```


### PASO 10: Utiliza plantillas para mostrar la información

Actualiza las vistas creadas en `views.py`:

```python
from django.shortcuts import get_object_or_404, get_list_or_404
from django.shortcuts import render
from .models import Departamento, Habilidad, Empleado

#devuelve el listado de empresas
def index(request):
	departamentos = get_list_or_404(Departamento.objects.order_by('nombre'))
	context = {'lista_departamentos': departamentos }
	return render(request, 'index.html', context)

#devuelve los datos de un departamento
def detail(request, departamento_id):
	departamento = get_object_or_404(Departamento, pk=departamento_id)
	context = {'departamento': departamento }
	return render(request, 'detail.html', context)

#devuelve los empelados de un departamento
def empleados(request, departamento_id):
	departamento = get_object_or_404(Departamento, pk=departamento_id)
	empleados =  departamento.empleado_set.all()
	context = {'departamento': departamento, 'empleados' : empleados }
	return render(request, 'empleados.html', context)

#devuelve los detalles de un empleado
def empleado(request, empleado_id):
	empleado = get_object_or_404(Empleado, pk=empleado_id)
	habilidades =  empleado.habilidades.all()
    context = { 'empleado': empleado, 'habilidades' : habilidades }
    return render(request, 'empleado.html', context)

# Devuelve los detalles de una habilidad
def habilidad(request, habilidad_id):
    habilidad = get_object_or_404(Habilidad, pk=habilidad_id)
    empleados =  habilidad.empleado_set.all()
    context = { 'empleados': empleados, 'habilidad' : habilidad }
    return render(request, 'habilidad.html', context)
```

Crea las plantillas (en una carpeta "templates" dentro del directorio de la aplicación) que definan la estructura de las páginas HTML resultantes :

Plantilla index.html:

```python
{% extends "base.html" %}

{% block titulo %}Listado de departamentos{% endblock %}

{% block contenido %}
<h2>Listado de departamentos</h2>
{% if lista_departamentos %}
	<ul>
	{% for d in lista_departamentos %}
		<li>
		    <a href="/appEmpresaDjango/departamento/{{ d.id }}">{{ d.nombre}}</a>
		</li>
	{% endfor %}
	</ul>
{% else %}
<p>No hay departamentos creados.</p>
{% endif %}
{% endblock %}
```
Plantilla detail.html:

```python
{% extends "base.html" %}

{% block titulo %} Detalle del departamento {% endblock %}

{% block contenido %}
<h2>Datos del departamento</h2>

{% if departamento %}
    <ul>
        <li>
            {{ departamento.nombre}}
        </li>
        <li>
            {{ departamento.telefono}}
        </li>
        <li>
            <a href="/appEmpresaDjango/departamento/{{ departamento.id }}/empleados">Ver empleados</a>
        </li>
    </ul>
{% else %}
    <p>No existe este departamento.</p>
{% endif %}

{% endblock %}
```

Plantilla empleados.html:

```python
{% extends "base.html" %}

{% block titulo %} Empleados del departamento {% endblock %}

{% block contenido %}
<h2>Lista de empleados</h2>
<h3>{{ departamento.nombre }}</h3>
{% if empleados %}
	<ul>
	{% for e in empleados %}
		<li>
		    <a href="/appEmpresaDjango/empleado/{{ e.id }}">{{ e.nombre}}</a>
		</li>
	{% endfor %}
	</ul>
{% else %}
<p>No hay empleados creados.</p>
{% endif %}
{% endblock %}
```

Plantilla empleado.html:

```python
{% extends "base.html" %}

{% block titulo %} Detalles del empleado {% endblock %}

{% block contenido %}

<h2>Datos del empleado</h2>

{% if empleado %}
    <ul>
        <li>
            {{ empleado.nombre}}
        </li>
        <li>
            {{ empleado.fecha_nacimiento}}
        </li>
        <li>
            {{ empleado.antiguedad}}
        </li>
        <li>
            <a href="/appEmpresaDjango/departamento/{{ empleado.departamento.id }}">{{ empleado.departamento}}</a>
        </li>
    </ul>
	<h3>Lista de habilidades</h3>
	{% if habilidades %}
		<ol>
		{% for h in habilidades %}
			<li><a href="/appEmpresaDjango/habilidad/{{ h.id }}">{{ h.nombre}}</a></li>
		{% endfor %}
		</ol>
	{% else %}
		<p>No tiene habilidades asociadas.</p>
	{% endif %}
{% else %}
    <p>No existe este empleado.</p>
{% endif %}

{% endblock %}
```

Plantilla habilidad.html:

```python
{% extends "base.html" %}

{% block titulo %} Detalles de la habilidad {% endblock %}

{% block contenido %}

<h2>Datos de la habilidad</h2>

{% if habilidad %}
    <h3>{{ habilidad.nombre}}</h3>
	<h3>Lista de empleados</h3>
	{% if empleados %}
		<ol>
		{% for e in empleados %}
			<li><a href="/appEmpresaDjango/empleado/{{ e.id }}">{{ e.nombre}}</a></li>
		{% endfor %}
		</ol>
	{% else %}
		<p>No tiene empleados asociados.</p>
	{% endif %}
{% else %}
    <p>No existe esta habilidad.</p>
{% endif %}

{% endblock %}
```

### PASO 11: La herencia en plantillas

Django permite crear una plantilla base que contenga el “esqueleto” con todos los elementos comunes y definir bloques que puedan sobreescritos por las plantillas que la hereden. 

Crea una plantilla "base.html":
```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <link rel="stylesheet" href="style.css">
        <title>{% block titulo %}Gestor de Empleados{% endblock %}</title>
    </head>
    <body>
        <div id="content">
	     <h1>Gestor de Empleados</h1>
            {% block contenido %}{% endblock %}
        </div>
    </body>
</html>
```


Las plantillas específicas que heredan de la plantilla base, como ya pusimos en las vistas anteriores, deben llevar una única directiva "extends":

```python
#index.html:
{% extends "base.html" %}
```

### PASO 12: Actualizar las URLs

En lugar de utilizar la ruta de la URL, podemos utilizar el nombre que le hemos dado en el mapeo definido en `urls.py`

Antes:
```html
<li><a href="/appEmpresaDjango/departamento/{{ d.id }}">{{ d.nombre }}</a></li>
```
Ahora
```html
<li><a href="{% url 'detail' d.id %}">{{ d.nombre}}</a></li>
```
Cambia también el resto de URLs en las vistas correspondientes.

Entrar en la aplicación [http://127.0.0.1:8000/appEmpresaDjango/](http://127.0.0.1:8000/appEmpresaDjango/)

## Licencia

![Licencia_img](http://mirrors.creativecommons.org/presskit/buttons/80x15/png/by-nc-sa.png)

Este tutorial esta licenciado como [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es_ES) aunque no necesariamente las imágenes de su interior.

