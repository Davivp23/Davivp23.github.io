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
</head>
<body>
    <div class="ms-container" id="ms-pre-selected-options">
        <div class="ms-selectable">
            <ul class="ms-list" tabindex="-1" id="students-list">
                <!-- Aquí se cargarán los nombres de los alumnos -->
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
        import { getFirestore, collection, getDocs, doc, getDoc } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore.js";
        
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
            const studentsList = document.getElementById('students-list');
            const querySnapshot = await getDocs(collection(db, "alumnos"));
            querySnapshot.forEach((doc) => {
                const student = doc.data();
                const li = document.createElement('li');
                li.className = 'ms-elem-selectable';
                li.id = doc.id;
                li.innerHTML = `<span>${student.nombre}</span>`;
                studentsList.appendChild(li);
            });
        
            // Añadir evento de clic a los elementos de la lista
            $('.ms-elem-selectable').on('click', function() {
                $('.ms-elem-selectable').removeClass('ms-selected');
                $(this).addClass('ms-selected');
                $('.ms-selection .ms-list').html('<li class="ms-elem-selection ms-selected">' + $(this).html() + '</li>');
                
                // Obtener el valor del elemento seleccionado
                var selectedValue = $(this).text();
                console.log("Elemento seleccionado: " + selectedValue);
                
                // Mostrar el valor seleccionado en la página
                $('#selected-output').text("Elemento seleccionado: " + selectedValue);
            });
        }
        
        async function loadSchedule() {
            const docRef = doc(db, "profesor", "jose");
            const docSnap = await getDoc(docRef);
            
            if (docSnap.exists()) {
                const schedule = docSnap.data().horario;
                console.log("Horario obtenido:", schedule); // Añadir esta línea para depuración
                if (Array.isArray(schedule) && schedule.length > 0) {
                    const scheduleTable = document.getElementById('schedule-table');
                    const days = ["Lunes", "Martes", "Miércoles", "Jueves", "Viernes"];
                    const times = ["00:00-00:30", "00:30-01:00", "01:00-01:30", "01:30-02:00", "02:00-02:30", "02:30-03:00", "03:00-03:30", "03:30-04:00", "04:00-04:30", "04:30-05:00", "05:00-05:30", "05:30-06:00", "06:00-06:30", "06:30-07:00", "07:00-07:30", "07:30-08:00", "08:00-08:30", "08:30-09:00", "09:00-09:30", "09:30-10:00", "10:00-10:30", "10:30-11:00", "11:00-11:30", "11:30-12:00", "12:00-12:30", "12:30-13:00", "13:00-13:30", "13:30-14:00", "14:00-14:30", "14:30-15:00", "15:00-15:30", "15:30-16:00", "16:00-16:30", "16:30-17:00", "17:00-17:30", "17:30-18:00", "18:00-18:30", "18:30-19:00", "19:00-19:30", "19:30-20:00", "20:00-20:30", "20:30-21:00", "21:00-21:30", "21:30-22:00", "22:00-22:30", "22:30-23:00", "23:00-23:30", "23:30-00:00"];
                    
                    for (let i = 0; i < times.length; i++) {
                        let rowAdded = false;
                        const row = document.createElement('tr');
                        const timeCell = document.createElement('td');
                        timeCell.innerHTML = times[i];
                        row.appendChild(timeCell);
                        
                        for (let j = 0; j < days.length; j++) {
                            const cell = document.createElement('td');
                            const checkbox = document.createElement('input');
                            checkbox.type = 'checkbox';
                            checkbox.className = 'available';
                            checkbox.checked = schedule[i * 5 + j];
                            cell.appendChild(checkbox);
                            row.appendChild(cell);
                            if (schedule[i * 5 + j]) {
                                rowAdded = true;
                            }
                        }
                        if (rowAdded) {
                            scheduleTable.appendChild(row);
                        }
                    }
                } else {
                    console.error("El arreglo de disponibilidad no es válido.");
                }
            } else {
                console.log("No such document!");
            }
        }
        
        $(document).ready(function() {
            loadStudents();
            loadSchedule();
        });
    </script>
    <div id="selected-output" style="margin-top: 20px; font-weight: bold;"></div>
    <table id="schedule-table" border="1" style="margin-top: 20px;">
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
</body>
</html>
