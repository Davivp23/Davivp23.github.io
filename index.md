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
        const docRef = doc(db, "profesores", "jose");
        const docSnap = await getDoc(docRef);
        
        if (docSnap.exists()) {
            const schedule = docSnap.data().disponibilidad;
            const scheduleTable = document.getElementById('schedule-table');
            const days = ["Lunes", "Martes", "Miércoles", "Jueves", "Viernes"];
            let timeSlot = 0;
            
            for (let i = 0; i < 48; i++) { // 48 slots per day (30 min intervals)
                const row = document.createElement('tr');
                for (let j = 0; j < 5; j++) { // 5 days a week
                    const cell = document.createElement('td');
                    if (schedule[timeSlot]) {
                        cell.className = 'available';
                    }
                    cell.innerHTML = `${days[j]} ${Math.floor(i / 2)}:${i % 2 === 0 ? '00' : '30'}`;
                    row.appendChild(cell);
                    timeSlot++;
                }
                scheduleTable.appendChild(row);
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
