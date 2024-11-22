<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js";
    import { getFirestore, collection, getDocs, doc, getDoc, setDoc } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore.js";

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

    async function loadStudents() {
        const studentsList = document.getElementById('students-list');
        const querySnapshot = await getDocs(collection(db, "alumnos"));
        querySnapshot.forEach((doc) => {
            const student = doc.data();
            const li = document.createElement('li');
            li.className = 'ms-elem-selectable'; // Clase necesaria para multi-select
            li.dataset.id = doc.id; // Usar atributo `data-id` para el ID
            li.innerHTML = `<span>${student.nombre}</span>`;
            studentsList.appendChild(li);
        });

        // Configurar eventos de selección
        $('.ms-elem-selectable').on('click', function () {
            $('.ms-elem-selectable').removeClass('ms-selected');
            $(this).addClass('ms-selected');
            selectedValue = $(this).data('id');
            $('#selected-output').text(`Elemento seleccionado: ${selectedValue}`);
        });
    }

    async function loadSchedule() {
        const docRef = doc(db, "profesor", "jose");
        const docSnap = await getDoc(docRef);

        if (docSnap.exists()) {
            const schedule = docSnap.data().horario;
            if (Array.isArray(schedule) && schedule.length > 0) {
                const scheduleTable = document.getElementById('schedule-table');
                const days = ["Lunes", "Martes", "Miércoles", "Jueves", "Viernes"];
                const times = Array.from({ length: 48 }, (_, i) => {
                    const hour = String(Math.floor(i / 2)).padStart(2, '0');
                    const minute = i % 2 === 0 ? "00" : "30";
                    return `${hour}:${minute}-${hour}:${minute === "00" ? "30" : "00"}`;
                });

                times.forEach((time, i) => {
                    const row = document.createElement('tr');
                    const timeCell = document.createElement('td');
                    timeCell.textContent = time;
                    row.appendChild(timeCell);

                    days.forEach((_, j) => {
                        const cell = document.createElement('td');
                        const checkbox = document.createElement('input');
                        checkbox.type = 'checkbox';
                        checkbox.dataset.index = i * 5 + j;
                        checkbox.checked = schedule[i * 5 + j] || false;
                        checkbox.disabled = true;
                        cell.appendChild(checkbox);
                        row.appendChild(cell);
                    });

                    scheduleTable.querySelector('tbody').appendChild(row);
                });
            } else {
                console.error("El arreglo de disponibilidad no es válido.");
            }
        } else {
            console.log("No se encontró el documento.");
        }
    }

    async function saveCheckboxValues() {
        if (!selectedValue) {
            alert("Por favor, selecciona un estudiante.");
            return;
        }

        const checkboxes = document.querySelectorAll('input[type="checkbox"]:not([disabled])');
        const boolArray = Array.from({ length: 240 }, (_, i) => checkboxes[i]?.checked || false);

        try {
            await setDoc(doc(db, "alumnos", selectedValue), { disponibilidad: boolArray });
            alert("Horario cambiado correctamente");
        } catch (error) {
            console.error("Error al guardar el horario:", error);
            alert("Hubo un error al guardar el horario.");
        }
    }

    $(document).ready(function () {
        loadStudents();
        loadSchedule();
    });
</script>
