<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PopCar</title>
    <link href="lou-multi-select-57fb8d3/css/multi-select.css" media="screen" rel="stylesheet" type="text/css">
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database.js"></script>
</head>
<body>
    <div class="ms-container" id="ms-pre-selected-options">
        <div class="ms-selectable">
            <ul class="ms-list" tabindex="-1">
                <li class="ms-elem-selectable" id="elem1">
                    <span>elem 1</span>
                </li>
                <li class="ms-elem-selectable" id="elem2">
                    <span>elem 2</span>
                </li>
                <li class="ms-elem-selectable" id="elem3">
                    <span>elem 3</span>
                </li>
            </ul>
        </div>
        <div class="ms-selection">
            <ul class="ms-list" tabindex="-1">
                <!-- Aquí se mostrará el elemento seleccionado -->
            </ul>
        </div>
    </div>
    <div id="selected-output" style="margin-top: 20px; font-weight: bold;"></div>

    <script>
        // Configuración de Firebase
        const firebaseConfig = {
            apiKey: "TU_API_KEY",
            authDomain: "TU_AUTH_DOMAIN",
            databaseURL: "TU_DATABASE_URL",
            projectId: "TU_PROJECT_ID",
            storageBucket: "TU_STORAGE_BUCKET",
            messagingSenderId: "TU_MESSAGING_SENDER_ID",
            appId: "TU_APP_ID"
        };

        // Inicializar Firebase
        const app = firebase.initializeApp(firebaseConfig);
        const database = firebase.database();

        $(document).ready(function() {
            $('.ms-elem-selectable').on('click', function() {
                $('.ms-elem-selectable').removeClass('ms-selected');
                $(this).addClass('ms-selected');
                $('.ms-selection .ms-list').html('<li class="ms-elem-selection ms-selected">' + $(this).html() + '</li>');
                
                // Obtener el valor del elemento seleccionado
                var selectedValue = $(this).text();
                console.log("Elemento seleccionado: " + selectedValue);
                
                // Mostrar el valor seleccionado en la página
                $('#selected-output').text("Elemento seleccionado: " + selectedValue);
                
                // Guardar el valor seleccionado en Firebase
                database.ref('selecciones/').push({
                    valor: selectedValue
                });
            });
        });
    </script>
</body>
</html>
