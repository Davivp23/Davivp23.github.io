---
layout: default
title: Página del profesor
---

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link href="lou-multi-select-57fb8d3/css/multi-select.css" media="screen" rel="stylesheet" type="text/css">
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <title>Página del profesor</title>
    <style>
         /* Tamaño de fuente base para escritorio */
        html {
            font-size: 16px;
        }
        /* Ajustar el tamaño de fuente para pantallas pequeñas (móviles) */
        @media (max-width: 600px) {
            html {
                font-size: 8px;
            }
        }
        body {
            display: flex;
            flex-direction: column;
            min-height: 10vh;
            margin: 0;
        }
        main {
            flex: 1;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        #auth-container {
            display: block;
            text-align: center;
            padding: 2vh;
            background-color: #fff;
        }
        #auth-button-container {
            display: flex;
            text-align: center;
            padding: 2vh;
            background-color: #fff;
        }
        #add-button-container {
            display: none;
            text-align: center;
            padding: 2vh;
            background-color: #fff;
        }       
        #alumnos-container {
            display: none;
            text-align: center;
            padding: 2vh;
            background-color: #fff;
        }      
        #schedule-container {
            display: none;
            text-align: center;
            padding: 2vh;
            background-color: #fff;
        }        
        #send-button-container {
            display: none;
            text-align: center;
            padding: 2vh;
            background-color: #fff;
        }
        button {
            padding: 1vh 2vw;
            font-size: 1rem;
            cursor: pointer;
            background-color: #567482;
            color: white;
            border: none;
            border-radius: 5px;
        }
        button:hover {
            background-color: #819198;
        }
    </style>
</head>
<body>
    <!-- Contenedor de autenticación -->
    <div id="auth-container">
        <h1>Acceso al Horario</h1>
        <p>Por favor, introduce la contraseña para acceder.</p>
        <input type="password" id="password" placeholder="Contraseña">
        <p id="error-message" style="color: red; display: none;">Contraseña incorrecta</p>
    </div>
    <!-- Contenedor de botón de autenticación -->
    <div id="auth-button-container">
        <button id="login-btn">Ingresar</button>
    </div>
    <!-- Contenedor principal (oculto hasta autenticación) -->    
    <div id="add-button-container">
        <button id="add-student-btn">Añadir Alumno</button>
    </div>
    <div class="ms-container" id="ms-pre-selected-options" style="display: none;">
        <div class="ms-selectable">
            <ul class="ms-list" tabindex="-1" id="students-list">
                <!-- Aquí se cargarán los nombres de los alumnos -->
            </ul>
        </div>
    </div>
    <div id="schedule-container">
        <h1>Horario</h1>
        <table>
            <tr>
                <th>Hora</th>
                <th>Lunes</th>
                <th>Martes</th>
                <th>Miércoles</th>
                <th>Jueves</th>
                <th>Viernes</th>
                <th>Todos</th>
            </tr>
            <tbody id="schedule"></tbody>
        </table>
    </div>
    <div id="send-button-container">
        <button onclick="sendData()">Enviar</button>
    </div>

<script type="module">
    // Importaciones de Firebase
    import { initializeApp } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js";
    import { getFirestore, doc, getDoc, setDoc, getDocs, collection } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore.js";

    // Configuración de Firebase
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
        });
    }

    loadStudents()

    // Constantes y variables
    const profesores = "jose"; // Cambia esto según sea necesario
    const PASSWORD = "12345"; // Contraseña predefinida (puedes cambiarla)

    // Referencias del DOM
    const authContainer = document.getElementById("auth-container");
    const authButtonContainer = document.getElementById("auth-button-container");
    const addButtonContainer = document.getElementById("add-button-container");
    const msContainer = document.getElementById("ms-pre-selected-options");
    const scheduleContainer = document.getElementById("schedule-container");
    const sendButtonContainer = document.getElementById("send-button-container");
    const loginBtn = document.getElementById("login-btn");
    const passwordInput = document.getElementById("password");
    const errorMessage = document.getElementById("error-message");

    // Función de autenticación
    loginBtn.addEventListener("click", () => {
        const enteredPassword = passwordInput.value;
        if (enteredPassword === PASSWORD) {
            authButtonContainer.style.display = "none";
            authContainer.style.display = "none"; // Ocultar autenticación
            addButtonContainer.style.display = "block";
            msContainer.style.display = "block";
            scheduleContainer.style.display = "block"; // Mostrar horario
            sendButtonContainer.style.display = "block";
            loadSchedule(); // Cargar datos del horario
        } else {
            errorMessage.style.display = "block"; // Mostrar error si la contraseña es incorrecta
        }
    });

    // Función para cargar el horario desde Firebase
    async function loadSchedule() {
        const docRef = doc(db, "profesor", profesores);
        const docSnap = await getDoc(docRef);

        if (docSnap.exists()) {
            const schedule = docSnap.data().horario;
            const days = ['mon', 'tue', 'wed', 'thu', 'fri'];

            for (let hour = 0; hour < 24; hour++) {
                for (let half = 0; half < 2; half++) {
                    days.forEach((day, index) => {
                        const id = `${day}${hour}${half}`;
                        const scheduleIndex = hour * 2 * 5 + half * 5 + index;
                        document.getElementById(id).checked = schedule[scheduleIndex];
                    });
                }
            }
        } else {
            console.log("No such document!");
        }
    }

    // Función para enviar datos al servidor
    window.sendData = async function() {
        const schedule = [];
        const days = ['mon', 'tue', 'wed', 'thu', 'fri'];

        for (let hour = 0; hour < 24; hour++) {
            for (let half = 0; half < 2; half++) {
                days.forEach(day => {
                    const id = `${day}${hour}${half}`;
                    schedule.push(document.getElementById(id).checked);
                });
            }
        }

        try {
            await setDoc(doc(db, "profesor", profesores), { horario: schedule });
            alert("Horario cambiado correctamente");
        } catch (error) {
            console.error("Error cambiando el horario: ", error);
            alert("Hubo un error al cambiar el horario");
        }
    };

    // Añadir alumno a Firebase
    document.getElementById('add-student-btn').addEventListener('click', async () => {
        const studentName = prompt("Introduce el nombre del alumno:");
        if (studentName) {
            const studentData = {
                nombre: studentName,
                disponibilidad: Array(240).fill(false), // Array de 240 valores false
                profesor: profesores
            };

            try {
                await setDoc(doc(db, "alumnos", studentName), studentData);
                alert("Alumno añadido correctamente");
            } catch (error) {
                console.error("Error añadiendo el alumno: ", error);
                alert("Hubo un error al añadir el alumno");
            }
        }
    });

    // Generar tabla dinámica
    const days = ['mon', 'tue', 'wed', 'thu', 'fri'];
    const tbody = document.getElementById('schedule');

    for (let hour = 0; hour < 24; hour++) {
        for (let half = 0; half < 2; half++) {
            const row = document.createElement('tr');
            const timeCell = document.createElement('td');
            const startHour = hour.toString().padStart(2, '0');
            const startMinute = (half * 30).toString().padStart(2, '0');
            const endHour = (half === 0) ? startHour : (hour + 1).toString().padStart(2, '0');
            const endMinute = (half === 0) ? '30' : '00';
            timeCell.textContent = `${startHour}:${startMinute} - ${endHour}:${endMinute}`;
            row.appendChild(timeCell);

            days.forEach((day) => {
                const cell = document.createElement('td');
                const checkbox = document.createElement('input');
                checkbox.type = 'checkbox';
                checkbox.id = `${day}${hour}${half}`;
                cell.appendChild(checkbox);
                row.appendChild(cell);
            });

            const allCell = document.createElement('td');
            const allCheckbox = document.createElement('input');
            allCheckbox.type = 'checkbox';
            allCheckbox.id = `all${hour}${half}`;
            allCheckbox.addEventListener('change', function() {
                days.forEach(day => {
                    document.getElementById(`${day}${hour}${half}`).checked = this.checked;
                });
            });
            allCell.appendChild(allCheckbox);
            row.appendChild(allCell);

            tbody.appendChild(row);
        }
    }
</script>
</body>
