# Reportes_Documentacion

 
Nombre:
Luis Fernando
Materia:
Desarrollo de aplicaciones
Tarea realizada en:
Visual Studio 2022 Y
Visual Studio Code









Código C#
 

 

 

 

 

 

 

 

 

Diseño del Form
 






Form
 
 El botón de insertar inserta una nueva fila en la tabla con el texto que este en los textbox guiándose de en qué textbox escribes y el que no deja escribir letras en el txtbox de itebis, descuento, precio unitario, cantidad, total general, y no te deja insertar números en los textbox, descripción y categoría.

El botón actualizar cambia la fila seleccionada que tenga este símbolo ➤, se puede seleccionar a otra fila, dando click izquierdo a la fila que se quiere seleccionar

El botón eliminar elimina la fila seleccionada con el símbolo ➤

El botón nuevo, elimina todo el texto que este en todas las textbox para evitar datos repetidos

El botón Generar PDF crea un pdf con la tabla actual completa


Proyecto de reportes PHP






Codigo html:
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Crear Factura</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: #f0f2f5;
            padding: 40px;
        }
        form {
            background: #ffffff;
            padding: 30px;
            max-width: 600px;
            margin: auto;
            border-radius: 12px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        input, button {
            width: 100%;
            padding: 12px;
            margin: 12px 0;
            border-radius: 5px;
            border: 1px solid #ccc;
        }
        button {
            background-color: #28a745;
            color: white;
            font-weight: bold;
            cursor: pointer;
        }
        button:hover {
            background-color: #218838;
        }
        .action-buttons {
            display: flex;
            gap: 10px;
            margin-top: 20px;
        }
        .action-buttons a {
            flex: 1;
            text-align: center;
            padding: 12px;
            background-color: #007bff;
            color: white;
            border-radius: 5px;
            text-decoration: none;
            font-weight: bold;
        }
        .action-buttons a:hover {
            background-color: #0056b3;
        }
    </style>
</head>
<body>

    <form action="crear_factura.php" method="POST">
        <h2>Formulario de Factura</h2>

        <input type="text" name="descripcion" placeholder="Descripción" required>
        <input type="text" name="categoria" placeholder="Categoría" required>
        <input type="number" name="cantidad" placeholder="Cantidad" required>
        <input type="number" step="0.01" name="precio_unitario" placeholder="Precio Unitario" required>
        <input type="number" step="0.01" name="itebis" placeholder="ITEBIS" required>
        <input type="number" step="0.01" name="descuento" placeholder="Descuento" required>
        <input type="number" step="0.01" name="total" placeholder="Total General" required>

        <button type="submit">Guardar Factura</button>

        <div class="action-buttons">
            <a href="actualizar.php">Actualizar Factura</a>
            <a href="eliminar.php">Eliminar Factura</a>
        </div>
    </form>

</body>
</html>








Archivos.PHP

Conexión.php
<?php
$host = '127.0.0.1';
$usuario = 'root';
$clave = 'ferdo1231973';
$puerto = '3305';
$baseDeDatos = 'bd_facturacionpruebas';

$conexion = new mysqli($host, $usuario, $clave, $baseDeDatos, $puerto);

if ($conexion->connect_error) {
    die("Error de conexión: " . $conexion->connect_error);
}
?>

Eliminar.php
<?php
require 'conexion.php';

if ($_SERVER['REQUEST_METHOD'] === 'POST' && isset($_POST['id'])) {
    $id = $_POST['id'];
    $stmt = $conexion->prepare("DELETE FROM facturas WHERE ID = ?");
    $stmt->bind_param("i", $id);
    if ($stmt->execute()) {
        echo "<p style='color:green; text-align:center;'>Factura eliminada correctamente.</p>";
    } else {
        echo "<p style='color:red; text-align:center;'>Error al eliminar la factura.</p>";
    }
}
?>

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Eliminar Factura</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: #f8d7da;
            padding: 40px;
        }
        form {
            background: #ffffff;
            padding: 25px;
            max-width: 500px;
            margin: auto;
            border-radius: 10px;
            box-shadow: 0 0 8px rgba(0,0,0,0.1);
        }
        select, button {
            width: 100%;
            padding: 12px;
            margin: 10px 0;
            border-radius: 5px;
            border: 1px solid #ccc;
        }
        button {
            background: #dc3545;
            color: white;
            font-weight: bold;
        }
        button:hover {
            background: #c82333;
        }
        h2 {
            text-align: center;
            color: #721c24;
        }
    </style>
</head>
<body>

    <h2>Eliminar Factura</h2>

    <form method="POST" action="eliminar.php">
        <label>Selecciona la factura que deseas eliminar:</label>
        <select name="id" required>
            <option value="">-- Selecciona una factura --</option>
            <?php
            $result = $conexion->query("SELECT ID, DESCRIPCIÓN FROM facturas");
            while ($row = $result->fetch_assoc()) {
                echo "<option value='{$row['ID']}'>#{$row['ID']} - {$row['DESCRIPCIÓN']}</option>";
            }
            ?>
        </select>
        <button type="submit">Eliminar</button>
    </form>

</body>
</html>

Generar_pdf.php
<?php
require 'conexion.php';
require 'vendor/autoload.php';

use Dompdf\Dompdf;
use Dompdf\Options;

// Consulta la base de datos
$sql = "SELECT * FROM facturas";
$resultado = $conexion->query($sql);

// Generar contenido HTML
$html = '
    <h2 style="text-align:center;">Reporte de Facturas</h2>
    <table border="1" width="100%" style="border-collapse: collapse;">
        <thead>
            <tr style="background-color:#f2f2f2;">
                <th>ID</th>
                <th>Descripción</th>
                <th>Categoría</th>
                <th>Cantidad</th>
                <th>Precio Unitario</th>
                <th>ITEBIS</th>
                <th>Descuento</th>
                <th>Total General</th>
            </tr>
        </thead>
        <tbody>
';

while ($row = $resultado->fetch_assoc()) {
    $html .= "<tr>
                <td>{$row['ID']}</td>
                <td>{$row['DESCRIPCIÓN']}</td>
                <td>{$row['CATEGORÍA']}</td>
                <td>{$row['CANTIDAD']}</td>
                <td>{$row['PRECIO_UNITARIO']}</td>
                <td>{$row['ITEBIS']}</td>
                <td>{$row['DESCUENTO']}</td>
                <td>{$row['TOTAL_GENERAL']}</td>
              </tr>";
}

$html .= '
        </tbody>
    </table>
';

// Opciones de domPDF
$options = new Options();
$options->set('defaultFont', 'Arial');

$dompdf = new Dompdf($options);
$dompdf->loadHtml($html);
$dompdf->setPaper('A4', 'landscape');
$dompdf->render();

// Descargar PDF
$dompdf->stream("reporte_facturas.pdf", ["Attachment" => true]);
exit;
?>

Reporte.php
<?php
require 'conexion.php';
// Conexión a la base de datos usando puerto 3305 y contraseña personalizada
$conexion = new mysqli("localhost", "root", "ferdo1231973", "bd_facturacionpruebas", 3305);

// Verificar conexión
if ($conexion->connect_error) {
    die("Conexión fallida: " . $conexion->connect_error);
}

// Obtener datos del formulario
$descripcion     = $_POST['descripcion'];
$categoria       = $_POST['categoria'];
$cantidad        = (int) $_POST['cantidad'];
$precio_unitario = (float) $_POST['precio_unitario'];
$itebis_pct      = (float) $_POST['itebis'];
$descuento_pct   = (float) $_POST['descuento'];

// Cálculos
$subtotal        = $cantidad * $precio_unitario;
$monto_itebis    = $subtotal * ($itebis_pct / 100);
$monto_descuento = $subtotal * ($descuento_pct / 100);
$total_general   = $subtotal + $monto_itebis - $monto_descuento;

// Insertar en la base de datos
$sql = "INSERT INTO facturas (`DESCRIPCIÓN`, `CATEGORÍA`, `CANTIDAD`, `PRECIO_UNITARIO`, `ITEBIS`, `DESCUENTO`, `TOTAL_GENERAL`) 
        VALUES (?, ?, ?, ?, ?, ?, ?)";

$stmt = $conexion->prepare($sql);
$stmt->bind_param("ssidddd", $descripcion, $categoria, $cantidad, $precio_unitario, $monto_itebis, $monto_descuento, $total_general);
?>

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Reporte de Factura</title>
    <style>
        body {
            font-family: 'Segoe UI', sans-serif;
            background-color: #eef1f5;
            padding: 40px;
        }
        .reporte {
            max-width: 600px;
            margin: auto;
            background: white;
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.1);
        }
        h2 {
            text-align: center;
            color: #333;
        }
        .campo {
            margin: 15px 0;
            font-size: 16px;
        }
        .campo strong {
            color: #555;
        }
        .campo span {
            float: right;
            font-weight: bold;
        }
        .volver {
            display: block;
            text-align: center;
            margin-top: 30px;
            text-decoration: none;
            color: white;
            background-color: #007BFF;
            padding: 12px;
            border-radius: 8px;
            width: 100%;
        }
        .volver:hover {
            background-color: #0056b3;
        }
    </style>
</head>
<body>

<div class="reporte">
<?php
if ($stmt->execute()) {
    $factura_id = $stmt->insert_id;

    echo "<h2>Factura Generada</h2>";
    echo "<div class='campo'><strong>ID:</strong> <span>$factura_id</span></div>";
    echo "<div class='campo'><strong>Descripción:</strong> <span>$descripcion</span></div>";
    echo "<div class='campo'><strong>Categoría:</strong> <span>$categoria</span></div>";
    echo "<div class='campo'><strong>Cantidad:</strong> <span>$cantidad</span></div>";
    echo "<div class='campo'><strong>Precio Unitario:</strong> <span>$" . number_format($precio_unitario, 2) . "</span></div>";
    echo "<div class='campo'><strong>ITBIS:</strong> <span>$" . number_format($monto_itebis, 2) . " ({$itebis_pct}%)</span></div>";
    echo "<div class='campo'><strong>Descuento:</strong> <span>$" . number_format($monto_descuento, 2) . " ({$descuento_pct}%)</span></div>";
    echo "<div class='campo'><strong>Total General:</strong> <span>$" . number_format($total_general, 2) . "</span></div>";
} else {
    echo "<p>Error al guardar la factura: " . $stmt->error . "</p>";
}

$stmt->close();
$conexion->close();
?>
    <a class="volver" href="formulario.html">← Generar otra factura</a>
    <a href="generar_pdf.php">Generar reporte</a>
</div>

</body>
</html>

Crear_factura.php
<?php
require 'conexion.php';

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $descripcion = $_POST['descripcion'];
    $categoria = $_POST['categoria'];
    $cantidad = $_POST['cantidad'];
    $precio_unitario = $_POST['precio_unitario'];
    $itebis = $_POST['itebis'];
    $descuento = $_POST['descuento'];
    $total = $_POST['total'];

    $sql = "INSERT INTO facturas (DESCRIPCIÓN, CATEGORÍA, CANTIDAD, PRECIO_UNITARIO, ITEBIS, DESCUENTO, TOTAL_GENERAL)
            VALUES (?, ?, ?, ?, ?, ?, ?)";

    $stmt = $conexion->prepare($sql);
    $stmt->bind_param("ssidddd", $descripcion, $categoria, $cantidad, $precio_unitario, $itebis, $descuento, $total);

    if ($stmt->execute()) {
        header("Location: formulario.html");
        exit;
    } else {
        echo "Error: " . $conexion->error;
    }
}
?>

Actualizer.php

<?php
require 'conexion.php';

// Inicializar variables vacías
$id = $descripcion = $categoria = $cantidad = $precio_unitario = $itebis = $descuento = $total = "";

// Si se selecciona una factura, cargar sus datos
if (isset($_GET['id'])) {
    $id = $_GET['id'];
    $sql = "SELECT * FROM facturas WHERE ID = ?";
    $stmt = $conexion->prepare($sql);
    $stmt->bind_param("i", $id);
    $stmt->execute();
    $resultado = $stmt->get_result();
    if ($resultado->num_rows === 1) {
        $factura = $resultado->fetch_assoc();
        $descripcion = $factura['DESCRIPCIÓN'];
        $categoria = $factura['CATEGORÍA'];
        $cantidad = $factura['CANTIDAD'];
        $precio_unitario = $factura['PRECIO_UNITARIO'];
        $itebis = $factura['ITEBIS'];
        $descuento = $factura['DESCUENTO'];
        $total = $factura['TOTAL_GENERAL'];
    }
}

// Si se envió el formulario para actualizar
if ($_SERVER["REQUEST_METHOD"] === "POST" && isset($_POST['id'])) {
    $id = $_POST['id'];
    $descripcion = $_POST['descripcion'];
    $categoria = $_POST['categoria'];
    $cantidad = $_POST['cantidad'];
    $precio_unitario = $_POST['precio_unitario'];
    $itebis = $_POST['itebis'];
    $descuento = $_POST['descuento'];
    $total = $_POST['total'];

    $sql = "UPDATE facturas SET DESCRIPCIÓN=?, CATEGORÍA=?, CANTIDAD=?, PRECIO_UNITARIO=?, ITEBIS=?, DESCUENTO=?, TOTAL_GENERAL=? WHERE ID=?";
    $stmt = $conexion->prepare($sql);
    $stmt->bind_param("ssiddddi", $descripcion, $categoria, $cantidad, $precio_unitario, $itebis, $descuento, $total, $id);

    if ($stmt->execute()) {
        echo "<p style='color:green; text-align:center;'>Factura actualizada correctamente.</p>";
    } else {
        echo "<p style='color:red; text-align:center;'>Error al actualizar: " . $conexion->error . "</p>";
    }
}
?>

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Actualizar Factura</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: #e9ecef;
            padding: 40px;
        }
        form {
            background: #fff;
            padding: 25px;
            max-width: 600px;
            margin: auto;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        input, select, button {
            width: 100%;
            padding: 12px;
            margin: 10px 0;
            border-radius: 5px;
            border: 1px solid #ccc;
        }
        button {
            background: #ffc107;
            color: black;
            font-weight: bold;
            cursor: pointer;
            transition: background 0.3s ease;
        }
        button:hover {
            background: #e0a800;
        }
        h2 {
            text-align: center;
        }
    </style>
</head>
<body>

<h2>Actualizar Factura</h2>

<form method="GET" action="actualizar.php">
    <label>Seleccionar Factura:</label>
    <select name="id" onchange="this.form.submit()">
        <option value="">-- Selecciona una factura --</option>
        <?php
        $result = $conexion->query("SELECT ID, DESCRIPCIÓN FROM facturas");
        while ($row = $result->fetch_assoc()):
            $selected = ($row['ID'] == $id) ? 'selected' : '';
            echo "<option value='{$row['ID']}' $selected>#{$row['ID']} - {$row['DESCRIPCIÓN']}</option>";
        endwhile;
        ?>
    </select>
</form>

<?php if ($id): ?>
<form method="POST" action="actualizar.php">
    <input type="hidden" name="id" value="<?= $id ?>">
    <input type="text" name="descripcion" value="<?= htmlspecialchars($descripcion) ?>" placeholder="Descripción" required>
    <input type="text" name="categoria" value="<?= htmlspecialchars($categoria) ?>" placeholder="Categoría" required>
    <input type="number" name="cantidad" value="<?= $cantidad ?>" placeholder="Cantidad" required>
    <input type="number" step="0.01" name="precio_unitario" value="<?= $precio_unitario ?>" placeholder="Precio Unitario" required>
    <input type="number" step="0.01" name="itebis" value="<?= $itebis ?>" placeholder="ITEBIS" required>
    <input type="number" step="0.01" name="descuento" value="<?= $descuento ?>" placeholder="Descuento" required>
    <input type="number" step="0.01" name="total" value="<?= $total ?>" placeholder="Total General" required>
    <button type="submit">Actualizar Factura</button>
</form>
<?php endif; ?>

</body>
</html>

Formulario y archivos con XAMPP
 
 
 
 
