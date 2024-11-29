---
layout: default
---

# PopCar
¿Quién eres?
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PopCar</title>
    <link href="lou-multi-select-57fb8d3/css/multi-select.css" media="screen" rel="stylesheet" type="text/css">
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <script type="module" src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js"></script>
    <script type="module" src="https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore.js"></script>
    <style>
        body {
            display: flex;
            flex-direction: column;
            min-height: 100vh;
            margin: 0;
        }
        main {
            flex: 1;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        #schedule-container {
            display: none; /* Ocultar inicialmente */
            margin-top: 20px;
            width: 100%;
        }
        #send-button-container {
            display: none; /* Ocultar inicialmente */
            text-align: center;
            padding: 20px;
            background-color: #f1f1f1;
        }
        button {
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
        }
        button:hover {
            background-color: #0056b3;
        }
    </style>
</head>
<body>
    <main>
        <div class="ms-container" id="ms-pre-selected-options">
            <div class="ms-selectable">
                <ul class="ms-list" tabindex="-1" id="students-list">
                    <!-- Aquí se cargarán los nombres de los alumnos -->
                </ul>
            </div>
        </div>

<div id="selected-output" style="margin-top: 20px; font-weight: bold;"></div>

<!-- Contenedor del horario -->
<div id="schedule-container">
    <table id="schedule-table" border="1" style="margin-top: 20px; width: 100%;">
        <thead>
            <tr>
                <th>Horas</th>
                <th>Lunes</th>
                <th>Martes</th>
                <th>Miércoles</th>
                <th>Jueves</th>
                <th>Viernes</th>
            </tr>
        </thead>
        <tbody>
            <!-- Aquí se cargará el horario -->
        </tbody>
    </table>
</div>
</main>

<!-- Contenedor del botón de enviar -->
<div id="send-button-container">
    <button onclick="saveCheckboxValues()">Enviar</button>
</div>

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
    let studentTeacher = null;

    async function loadStudents() {
        const studentsList = document.getElementById('students-list');
        const querySnapshot = await getDocs(collection(db, "alumnos"));
        querySnapshot.forEach((doc) => {
            const student = doc.data();
            const li = document.createElement('li');
            li.className = 'ms-elem-selectable';
            li.id = doc.id;
            li.teacher = student.profesor;
            li.innerHTML = `<span>${student.nombre}</span>`;
            studentsList.appendChild(li);
        });
//---------------------------------------------------------------------
        // Añadir evento de clic a los elementos de la lista
        $('.ms-elem-selectable').on('click', function () {
            $('.ms-elem-selectable').removeClass('ms-selected');
            $(this).addClass('ms-selected');
            $('.ms-selection .ms-list').html('<li class="ms-elem-selection ms-selected">' + $(this).html() + '</li>');

            // Obtener el valor del elemento seleccionado
            selectedValue = $(this).attr('id');
            studentTeacher = this.teacher;
            console.log("Elemento seleccionado: " + selectedValue);
            console.log("Teacher del estudiante: " + studentTeacher);

            // Mostrar el valor seleccionado en la página
            $('#selected-output').text("Elemento seleccionado: " + selectedValue);

            // Cargar el horario según el estudiante seleccionado
            loadSchedule(studentTeacher);
        });
    }

    async function loadSchedule(profesorId) {
        
        console.log("teacher: " + profesorId);
        const scheduleTable = document.getElementById('schedule-table');
        scheduleTable.querySelector('tbody').innerHTML = ""; // Limpiar tabla previa
        const docRef = doc(db, "profesor", profesorId);
        const docSnap = await getDoc(docRef);

        if (docSnap.exists()) {
            const boolArray = docSnap.data().horario || [];
            const days = ["Lunes", "Martes", "Miércoles", "Jueves", "Viernes"];
            const times = ["00:00-00:30", "00:30-01:00", "01:00-01:30", "01:30-02:00", "02:00-02:30", "02:30-03:00", "03:00-03:30", "03:30-04:00", "04:00-04:30", "04:30-05:00", "05:00-05:30", "05:30-06:00", "06:00-06:30", "06:30-07:00", "07:00-07:30", "07:30-08:00", "08:00-08:30", "08:30-09:00", "09:00-09:30", "09:30-10:00", "10:00-10:30", "10:30-11:00", "11:00-11:30", "11:30-12:00", "12:00-12:30", "12:30-13:00", "13:00-13:30", "13:30-14:00", "14:00-14:30", "14:30-15:00", "15:00-15:30", "15:30-16:00", "16:00-16:30", "16:30-17:00", "17:00-17:30", "17:30-18:00", "18:00-18:30", "18:30-19:00", "19:00-19:30", "19:30-20:00", "20:00-20:30", "20:30-21:00", "21:00-21:30", "21:30-22:00", "22:00-22:30", "22:30-23:00", "23:00-23:30", "23:30-00:00"];

            let n = 0;
            
            for (let i = 0; i < times.length; i++) {
                    
                for (let j = 0; j < days.length; j++) {
                    const cell = document.createElement('td');
                    const checkbox = document.createElement('input');
                    checkbox.type = 'checkbox';
                    checkbox.className = `${i * days.length + j}`;

                    // Habilitar solo si el boolArray lo permite
                    if (boolArray[i * days.length + j]) {
                        n = n+1;
                    } 
                }
                if (n != 0){
                    const row = document.createElement('tr');
                    const timeCell = document.createElement('td');
                    timeCell.innerHTML = times[i];
                    row.appendChild(timeCell);
        
                    for (let j = 0; j < days.length; j++) {
                        const cell = document.createElement('td');
                        const checkbox = document.createElement('input');
                        checkbox.type = 'checkbox';
                        checkbox.className = `${i * days.length + j}`;
        
                        // Habilitar solo si el boolArray lo permite
                        if (boolArray[i * days.length + j]) {
                            checkbox.disabled = false; // Activar
                        } else {
                            checkbox.disabled = true;
                            checkbox.style.display = 'none';// Desactivar
                        }
        
                        cell.appendChild(checkbox);
                        row.appendChild(cell);
                    }
                    scheduleTable.querySelector('tbody').appendChild(row);
                }
                n = 0
            }
            
            // Mostrar el horario y el botón de enviar
            document.getElementById('schedule-container').style.display = 'block';
            document.getElementById('send-button-container').style.display = 'block';
        }
    }
    
    window.saveCheckboxValues = async function() {
        if (!selectedValue) return;
    
        const boolArray = new Array(240).fill(false); // Array de disponibilidad inicializado en falso
        const checkboxes = document.querySelectorAll('input[type="checkbox"]');
        checkboxes.forEach((checkbox, index) => {
            console.log(checkbox, index, checkbox.className);
            if (checkbox.checked) {
                boolArray[checkbox.className] = true;
            }
        });
    
        try {
            const studentDocRef = doc(db, "alumnos", selectedValue);
            await setDoc(studentDocRef, { disponibilidad: boolArray }, { merge: true });
            alert("Disponibilidad enviada correctamente.");
        } catch (error) {
            console.error("Error al guardar la disponibilidad: ", error);
        }
    }

    // Llamar funciones iniciales
    loadStudents();
</script>
</body>
</html>

