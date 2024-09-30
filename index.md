---
layout: default
---

# PopCar
¿Quién eres?

<head>
    <link href="lou-multi-select-57fb8d3/css/multi-select.css" media="screen" rel="stylesheet" type="text/css">
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
</head>
<body>
    <div class="ms-container" id="ms-pre-selected-options">
        <div class="ms-selectable">
            <ul class="ms-list" tabindex="-1">
                <li class="ms-elem-selectable" id="elem1">
                    <span>elem 1</span>
                </li>
                <li class="ms-elem-selectable" id="elem2">
                    <span>elem 2</span>
                </li>
                <li class="ms-elem-selectable" id="elem3">
                    <span>elem 3</span>
                </li>
            </ul>
        </div>
        <div class="ms-selection">
            <ul class="ms-list" tabindex="-1">
                <!-- Aquí se mostrará el elemento seleccionado -->
            </ul>
        </div>
    </div>
    <script src="lou-multi-select-57fb8d3/js/jquery.multi-select.js" type="text/javascript"></script>
    <script>
        $(document).ready(function() {
            $('.ms-elem-selectable').on('click', function() {
                $('.ms-elem-selectable').removeClass('ms-selected');
                $(this).addClass('ms-selected');
                $('.ms-selection .ms-list').html('<li class="ms-elem-selection ms-selected">' + $(this).html() + '</li>');
            });
        });
    </script>
</body>
