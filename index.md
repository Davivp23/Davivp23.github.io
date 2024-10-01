---
layout: default
---

# PopCar
¿Quién eres?

<head>
    <link href="lou-multi-select-57fb8d3/css/multi-select.css" media="screen" rel="stylesheet" type="text/css">
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <script type="module" src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js"></script>
    <script type="module" src="https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore.js"></script>
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
    <script src="lou-multi-select-57fb8d3/js/jquery.multi-select.js" type="text/javascript"></script>
    <script type="module">
        // Configuración de Firebase
        import { initializeApp } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js";
        import { getFirestore, doc, setDoc } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore.js";

        const firebaseConfig = {
            apiKey: "AIzaSyCBJWfRiKmrVLKXLJ_cY9XQlg0D7U56ZqE",
            authDomain: "popcarautohorario.firebaseapp.com",
            projectId: "popcarautohorario",
            storageBucket: "popcarautohorario.appspot.com",
            messagingSenderId: "1046371810802",
            appId: "1:1046371810802:web:8b9944cd5001359ac23f6b",
            measurementId: "G-WK8NCRW5J6",
            databaseURL: "https://popcarautohorario-default-rtdb.europe-west1.firebasedatabase.app/"
        };

        // Inicializar Firebase
        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);

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

                // Guardar el valor seleccionado en Firestore
                setDoc(doc(db, "selectedElement", "current"), {
                    value: selectedValue
                });
            });
        });
    </script>
    <div id="selected-output" style="margin-top: 20px; font-weight: bold;"></div>
</body>
