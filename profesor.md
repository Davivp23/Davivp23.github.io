---
layout: default
title: Página del profesor
---

<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Página del profesor</title>
<style>
    /* Estilos generales */
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #f4f4f9;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

#auth-container, #schedule-container {
    text-align: center;
    background: #fff;
    padding: 30px;
    box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.1);
    border-radius: 10px;
    width: 90%;
    max-width: 600px;
}

h1 {
    color: #333;
    margin-bottom: 20px;
}

input[type="password"] {
    padding: 10px;
    width: 80%;
    max-width: 300px;
    border: 1px solid #ccc;
    border-radius: 5px;
    margin-bottom: 20px;
    font-size: 16px;
}

table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
}

th, td {
    border: 1px solid #ddd;
    padding: 8px;
    text-align: center;
}

th {
    background-color: #4CAF50;
    color: white;
}

/* Botones */
button {
    padding: 10px 20px;
    font-size: 16px;
    color: #fff;
    background-color: #4CAF50;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    margin-top: 10px;
}

button:hover {
    background-color: #45a049;
}

button:disabled {
    background-color: #ccc;
    cursor: not-allowed;
}

#error-message {
    margin-top: 10px;
    color: red;
}

/* Flexbox para centrar */
#schedule-container {
    display: none; /* Oculto inicialmente */
}
</style>
</head>
<body>
<!-- Contenedor de autenticación -->
<div id="auth-container">
    <h1>Acceso al Horario</h1>
    <p>Por favor, introduce la contraseña para acceder.</p>
    <input type="password" id="password" placeholder="Contraseña">
    <button id="login-btn">Ingresar</button>
    <p id="error-message" style="display: none;">Contraseña incorrecta</p>
</div>

<!-- Contenedor principal (oculto hasta autenticación) -->
<div id="schedule-container">
    <button id="add-student-btn">Añadir Alumno</button>
    <h1>Horario</h1>
    <button onclick="sendData()">Enviar</button>
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
    <button onclick="sendData()">Enviar</button>
</div>

<script type="module">
    // Importaciones de Firebase
    import { initializeApp } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js";
    import { getFirestore, doc, getDoc, setDoc } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore.js";

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

    // Constantes y variables
    const profesores = "jose";
    const PASSWORD = "12345";

    // Referencias del DOM
    const authContainer = document.getElementById("auth-container");
    const scheduleContainer = document.getElementById("schedule-container");
    const loginBtn = document.getElementById("login-btn");
    const passwordInput = document.getElementById("password");
    const errorMessage = document.getElementById("error-message");

    // Función de autenticación
    loginBtn.addEventListener("click", () => {
        const enteredPassword = passwordInput.value;
        if (enteredPassword === PASSWORD) {
            authContainer.style.display = "none";
            scheduleContainer.style.display = "block";
            loadSchedule();
        } else {
            errorMessage.style.display = "block";
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

    // Añadir alumno
    document.getElementById('add-student-btn').addEventListener('click', async () => {
        const studentName = prompt("Introduce el nombre del alumno:");
        if (studentName) {
            const studentData = {
                nombre: studentName,
                disponibilidad: Array(240).fill(false),
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

    // Generar tabla
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
</html>
