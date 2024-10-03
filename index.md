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
    <table id="students-table">
        <thead>
            <tr>
                <th>Nombre</th>
                <th>Seleccionar 1</th>
                <th>Seleccionar 2</th>
                <th>Seleccionar 3</th>
                <th>Seleccionar 4</th>
                <th>Seleccionar 5</th>
            </tr>
        </thead>
        <tbody>
            <!-- Aquí se cargarán los nombres de los alumnos -->
        </tbody>
    </table>
    <script src="lou-multi-select-57fb8d3/js/jquery.multi-select.js" type="text/javascript"></script>
    <script type="module">
        // Configuración de Firebase
        import { initializeApp } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js";
        import { getFirestore, collection, getDocs, doc, updateDoc, getDoc } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore.js";

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

        async function loadStudents() {
            const studentsTableBody = document.querySelector('#students-table tbody');
            const querySnapshot = await getDocs(collection(db, "alumnos"));
            querySnapshot.forEach((doc) => {
                const student = doc.data();
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td>${student.nombre}</td>
                    ${Array(5).fill().map((_, i) => `<td><input type="checkbox" data-id="${doc.id}" data-index="${i}" ${student.disponibilidad && student.disponibilidad[i] ? 'checked' : ''}></td>`).join('')}
                `;
                studentsTableBody.appendChild(tr);
            });

            // Añadir evento de clic a los checkboxes
            document.querySelectorAll('#students-table input[type="checkbox"]').forEach((checkbox) => {
                checkbox.addEventListener('click', async function() {
                    const docId = this.getAttribute('data-id');
                    const index = this.getAttribute('data-index');
                    const newValue = this.checked;
                    const docRef = doc(db, "alumnos", docId);
                    const docSnap = await getDoc(docRef);
                    const disponibilidad = docSnap.data().disponibilidad || Array(5).fill(false);
                    disponibilidad[index] = newValue;
                    await updateDoc(docRef, { disponibilidad });
                    console.log(`Elemento ${docId} actualizado en la posición ${index} a ${newValue}`);
                });
            });
        }

        $(document).ready(function() {
            loadStudents();
        });
    </script>
</body>
