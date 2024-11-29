---
layout: default
title: Página del profesor
---

<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Página del profesor</title>
</head>
<body>
<div id="auth-container">
    <h1>Acceso al Horario</h1>
    <p>Por favor, introduce la contraseña para acceder.</p>
    <input type="password" id="password" placeholder="Contraseña">
    <button id="login-btn">Ingresar</button>
    <p id="error-message" style="color: red; display: none;">Contraseña incorrecta</p>
</div>

<div id="schedule-container" style="display: none;">
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
    import { initializeApp } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js";
    import { getFirestore, doc, getDoc, setDoc } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore.js";

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

    const profesores = "jose"; // Cambia esto según corresponda

    // Contraseña predefinida (en un entorno real, debería manejarse en el servidor)
    const PASSWORD = "12345"; // Cambia esto por una contraseña segura

    // Elementos del DOM
    const authContainer = document.getElementById("auth-container");
    const scheduleContainer = document.getElementById("schedule-container");
    const loginBtn = document.getElementById("login-btn");
    const passwordInput = document.getElementById("password");
    const errorMessage = document.getElementById("error-message");

    // Validar contraseña
    loginBtn.addEventListener("click", () => {
        const enteredPassword = passwordInput.value;
        if (enteredPassword === PASSWORD) {
            authContainer.style.display = "none";
            scheduleContainer.style.display = "block";
            loadSchedule(); // Cargar el horario al iniciar sesión
        } else {
            errorMessage.style.display = "block";
        }
    });

    // Función para cargar el horario
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

    // Función para enviar los datos
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
</script>
</body>
</html>
