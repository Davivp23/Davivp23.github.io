---
layout: default
title: debug
---

back

<button id="add-student-btn">Añadir Alumno</button>

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
    <tbody id="schedule"></tbody>
</table>

<script>
    const days = ['mon', 'tue', 'wed', 'thu', 'fri'];
    const tbody = document.getElementById('schedule');

    for (let hour = 0; hour < 24; hour++) {
        const row = document.createElement('tr');
        const timeCell = document.createElement('td');
        timeCell.textContent = `${hour.toString().padStart(2, '0')}:00 - ${(hour + 1).toString().padStart(2, '0')}:00`;
        row.appendChild(timeCell);

        days.forEach((day, index) => {
            const cell = document.createElement('td');
            const checkbox = document.createElement('input');
            checkbox.type = 'checkbox';
            checkbox.id = `${day}${hour}`;
            cell.appendChild(checkbox);
            row.appendChild(cell);
        });

        tbody.appendChild(row);
    }
</script>

<button onclick="sendData()">Enviar</button>

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
    
    function sendData() {
      const schedule = [];
      const days = ['mon', 'tue', 'wed', 'thu', 'fri'];
  
      for (let i = 1; i <= 24; i++) {
        days.forEach(day => {
          schedule.push(document.getElementById(`${day}${i}`).checked);
        });
        
      // Aquí puedes enviar el arreglo a tu base de datos
        try {
            await setDoc(doc(db, "profesor", profesores), horario);
            alert("Horario cambiado correctamente");
            }
        catch (error) {
            console.error("Cambiando el horario ", error);
            alert("Hubo un error al cambiar el horario");
            }
      }
    }


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
