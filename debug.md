---
layout: default
title: debug
---


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

  [back](./)
</script>
