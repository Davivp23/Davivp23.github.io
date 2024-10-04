---
layout: default
title: debug
---

back

<button id="add-student-btn">Añadir Alumno</button>

<script type="module">
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

    document.getElementById('add-student-btn').addEventListener('click', async () => {
        const studentName = prompt("Introduce el nombre del alumno:");
        if (studentName) {
            const studentData = {
                nombre: studentName,
                disponibilidad: Array(48).fill(false) // Array de 48 bools inicializados a false
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
</script>

# Horario

<table>
  <tr>
    <th>Hora</th>
    <th>Lunes</th>
    <th>Martes</th>
    <th>Miércoles</th>
    <th>Jueves</th>
    <th>Viernes</th>
  </tr>
  <tr>
    <td>08:00 - 09:00</td>
    <td><input type="checkbox" id="mon1"></td>
    <td><input type="checkbox" id="tue1"></td>
    <td><input type="checkbox" id="wed1"></td>
    <td><input type="checkbox" id="thu1"></td>
    <td><input type="checkbox" id="fri1"></td>
  </tr>
  <!-- Añade más filas según sea necesario -->
</table>

<button onclick="sendData()">Enviar</button>

<script>
  function sendData() {
    const schedule = [
      document.getElementById('mon1').checked,
      document.getElementById('tue1').checked,
      document.getElementById('wed1').checked,
      document.getElementById('thu1').checked,
      document.getElementById('fri1').checked,
      // Añade más elementos según sea necesario
    ];

    // Aquí puedes enviar el arreglo a tu base de datos
    console.log(schedule); // Por ahora, solo lo mostramos en la consola
  }
</script>
