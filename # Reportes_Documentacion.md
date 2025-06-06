Proyecto 1: Aplicación de Facturación en C# (Windows Forms)
Conexión a la Base de Datos MySQL
Para conectar la aplicación con la base de datos MySQL, usé el conector oficial de MySQL para .NET (MySql.Data). La cadena de conexión contiene todos los parámetros necesarios:

csharp
string connectionString = "Server=localhost;Database=BD_FacturacionPruebas;Uid=root;Pwd=ferdo1231973;Port=3305;";
Explicación de cada parámetro:

Server: Dirección del servidor (en este caso, localhost)

Database: Nombre de la base de datos

Uid: Usuario de MySQL

Pwd: Contraseña del usuario

Port: Puerto donde escucha MySQL (3305 en este caso)

Uso de Report Viewer para Generar PDFs
Para generar los reportes en PDF, implementé iTextSharp, una biblioteca popular para trabajar con PDFs en .NET. El proceso es:

Crear un documento PDF:

csharp
Document doc = new Document(PageSize.A4, 10f, 10f, 20f, 20f);
Configurar el escritor del PDF:

csharp
PdfWriter.GetInstance(doc, new FileStream(saveFile.FileName, FileMode.Create));
Agregar contenido:

Título centrado

Tabla con los datos de la base de datos

Cerrar el documento:

csharp
doc.Close();
Funcionalidades Principales
Validación de Campos:

Campos numéricos solo aceptan números (enteros o decimales)

Campos de texto no aceptan números

Placeholders que desaparecen al hacer focus

CRUD Completo:

Insertar: Agrega nuevos registros a la base de datos

Actualizar: Modifica registros existentes

Eliminar: Borra registros seleccionados

Nuevo: Limpia el formulario

Exportación a PDF:

Genera un archivo PDF con todos los registros visibles en el DataGridView

Permite seleccionar la ubicación para guardar el archivo

Proyecto 2: Sistema de Facturación en PHP
Conexión a la Base de Datos
El archivo conexion.php establece la conexión con MySQL:

php
$conexion = new mysqli($host, $usuario, $clave, $baseDeDatos, $puerto);
Instalación y Uso de DomPDF
Para generar PDFs en PHP, usé DomPDF. Así lo instalé:

Instalar Composer (si no está instalado):

Descargar de getcomposer.org

Ejecutar el instalador

Instalar DomPDF:

En la carpeta del proyecto ejecutar:

bash
composer require dompdf/dompdf
Implementación en el código:

php
require 'vendor/autoload.php';
use Dompdf\Dompdf;
use Dompdf\Options;

// Configuración
$options = new Options();
$options->set('defaultFont', 'Arial');

// Crear instancia
$dompdf = new Dompdf($options);

// Generar HTML
$html = '<h1>Mi Reporte</h1>...';

// Cargar HTML y renderizar PDF
$dompdf->loadHtml($html);
$dompdf->setPaper('A4', 'landscape');
$dompdf->render();

// Descargar PDF
$dompdf->stream("reporte.pdf", ["Attachment" => true]);
Estructura del Proyecto PHP
formulario.html:

Interfaz principal para crear facturas

Validación básica con HTML5 (required, type=number)

crear_factura.php:

Procesa el formulario y guarda en la base de datos

Realiza cálculos automáticos (subtotal, ITEBIS, descuentos)

actualizar.php:

Permite seleccionar una factura existente y modificar sus datos

Muestra el formulario con los datos actuales

eliminar.php:

Interfaz para seleccionar y eliminar facturas

Confirmación antes de eliminar

generar_pdf.php:

Genera un reporte en PDF con todas las facturas

Diseño tabular con estilo básico

Características Especiales
Cálculos Automáticos:

El sistema calcula automáticamente:

Subtotal (cantidad × precio unitario)

Monto de ITEBIS (porcentaje aplicado al subtotal)

Descuentos (porcentaje aplicado al subtotal)

Total general (subtotal + ITEBIS - descuento)

Validación de Datos:

Todos los campos son obligatorios

Campos numéricos validan que sean valores positivos

Protección básica contra inyección SQL con prepared statements

Experiencia de Usuario:

Mensajes de confirmación/error claros

Diseño responsive y amigable

Flujo intuitivo entre pantallas

Pues basicamente para todo esto se requiere

Proyecto C# (Windows Forms) - Visual Studio
text
Reportes_Facturacion/
├── Reportes.sln                  // Solución de Visual Studio
├── Reportes/
│   ├── Form1.cs                  // Formulario principal (Diseño y código)
│   ├── Form1.Designer.cs         // Diseño del formulario (generado automáticamente)
│   ├── Program.cs                // Punto de entrada de la aplicación
│   ├── App.config                // Configuración de la aplicación
│   ├── Packages.config           // Paquetes NuGet (incluye MySql.Data)
│   ├── bin/                      // Binarios compilados
│   └── obj/                      // Objetos de compilación
Dependencias necesarias (instalar via NuGet):

MySql.Data (para conexión a MySQL)

iTextSharp (para generación de PDF)

Proyecto PHP - Visual Studio Code
text
Facturacion_PHP/
├── conexion.php                  // Conexión a la base de datos
├── formulario.html               // Formulario principal
├── crear_factura.php             // Procesa nuevas facturas
├── actualizar.php                // Actualiza facturas existentes
├── eliminar.php                  // Elimina facturas
├── generar_pdf.php               // Genera reporte en PDF
├── reporte.php                   // Muestra detalles de factura
├── vendor/                       // Dependencias (incluye dompdf)
│   └── autoload.php              // Autocargador de Composer
├── composer.json                 // Configuración de Composer
└── composer.lock                 // Versiones exactas de dependencias
Archivos adicionales necesarios:

composer.json (debe existir para instalar dompdf):

json
{
    "require": {
        "dompdf/dompdf": "^2.0"
    }
}
Base de datos SQL (script de creación):

sql
CREATE DATABASE BD_FacturacionPruebas;
USE BD_FacturacionPruebas;

CREATE TABLE facturas (
    ID INT AUTO_INCREMENT PRIMARY KEY,
    DESCRIPCIÓN VARCHAR(255),
    CATEGORÍA VARCHAR(100),
    CANTIDAD INT,
    PRECIO_UNITARIO DECIMAL(10,2),
    ITEBIS DECIMAL(10,2),
    DESCUENTO DECIMAL(10,2),
    TOTAL_GENERAL DECIMAL(10,2)
);
Pasos para configurar el proyecto PHP
Instalar XAMPP/WAMP con:

Apache

MySQL (configurar en puerto 3305 si es necesario)

PHP

Ejecutar en la terminal (dentro de la carpeta del proyecto):

bash
composer install
Importar el script SQL a MySQL

Modificar en conexion.php:

Usuario

Contraseña

Puerto (si no es el default 3306)

Requisitos comunes para ambos proyectos
MySQL instalado (versión 5.7+)

Base de datos creada con la estructura indicada

Usuario con permisos de lectura/escritura


 
 
 
