---
layout: default
---

# PopCar
¿Quién eres?

<div lang="es">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>PopCar</title>
        <link href="lou-multi-select-57fb8d3/css/multi-select.css" rel="stylesheet" type="text/css">
        <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
        <script type="module" src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js"></script>
        <script type="module" src="https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore.js"></script>
    </head>

    <body>
        <div class="ms-container" id="ms-pre-selected-options">
            <div class="ms-selectable">
                <ul class="ms-list" tabindex="-1" id="students-list">
                    <!-- Aquí se cargarán los nombres de los alumnos -->
                </ul>
            </div>
        </div>

        <button onclick="saveCheckboxValues()">Enviar</button>

        <script src="lou-multi-select-57fb8d3/js/jquery.multi-select.js"></script>
        <script type="module">
            // Configuración de Firebase
            import { initializeApp } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js";
            import { getFirestore, collection, getDocs, doc, getDoc, setDoc } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore.js";

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

            let selectedValue = null;

            async function loadStudents() {
                const studentsList = document.getElementById('students-list');
                const querySnapshot = await getDocs(collection(db, "alumnos"));
                querySnapshot.forEach((doc) => {
                    const student = doc.data();
                    const li = document.createElement('li');
                    li.className = 'ms-elem-selectable';
                    li.dataset.id = doc.id;
                    li.innerHTML = `<span>${student.nombre}</span>`;
                    studentsList.appendChild(li);
                });

                $('.ms-elem-selectable').on('click', function () {
                    $('.ms-elem-selectable').removeClass('ms-selected');
                    $(this).addClass('ms-selected');
                    selectedValue = $(this).data('id');
                    $('#selected-output').text(`Elemento seleccionado: ${selectedValue}`);
                });
            }

            async function saveCheckboxValues() {
                if (!selectedValue) {
                    alert("Por favor, selecciona un estudiante.");
                    return;
                }

                const checkboxes = document.querySelectorAll('input[type="checkbox"]:not([disabled])');
                const boolArray = Array.from({ length: 240 }, (_, i) => checkboxes[i]?.checked || false);

                try {
                    await setDoc(doc(db, "alumnos", selectedValue), { disponibilidad: boolArray });
                    alert("Horario cambiado correctamente");
                } catch (error) {
                    console.error("Error al guardar el horario:", error);
                    alert("Hubo un error al guardar el horario.");
                }
            }

            $(document).ready(function () {
                loadStudents();
            });
        </script>

        <div id="selected-output" style="margin-top: 20px; font-weight: bold;"></div>
    </body>
</div>
