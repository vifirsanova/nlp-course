**jsPsych** — инструмент для создания тестов с замером времени (RT) и последующего бесплатного размещения в интернете.

## Часть 1. Создание теста (Пример HTML-файла)

1. Экран приветствия.
2. Тестовый вопрос с картинкой и вариантами ответа.
3. **Замер времени реакции** (от появления вопроса до нажатия кнопки).
4. Экран благодарности и вывод результатов.

**Создайте на компьютере папку `my_jspsych_test`.** Внутри создайте файл `index.html` и скопируйте туда этот код:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Мой jsPsych тест с таймером</title>
    <!-- Подключаем jsPsych из CDN -->
    <script src="https://unpkg.com/jspsych@7.3.4"></script>
    <!-- Подключаем стандартные плагины (для кнопок, html, etc.) -->
    <script src="https://unpkg.com/@jspsych/plugin-html-keyboard-response@1.1.3"></script>
    <script src="https://unpkg.com/@jspsych/plugin-html-button-response@1.1.3"></script>
    <!-- Подключаем CSS стили -->
    <link href="https://unpkg.com/jspsych@7.3.4/css/jspsych.css" rel="stylesheet" type="text/css" />
</head>
<body>
    <!-- Контейнер, куда jsPsych будет рендерить тест -->
    <div id="jspsych-target"></div>

    <script>
        // Инициализируем jsPsych
        var jsPsych = initJsPsych({
            display_element: 'jspsych-target',
            on_finish: function() {
                // Когда тест закончен, показываем данные в консоли и на экране
                var data = jsPsych.data.get().filter({trial_id: 'test_question'}).values()[0];
                if (data) {
                    document.body.innerHTML += '<h2>Результаты:</h2>' +
                        '<p>Ответ: ' + data.response + '</p>' +
                        '<p>Время реакции: ' + data.rt + ' мс</p>';
                }
                console.log(jsPsych.data.get().json());
            }
        });

        // 1. Экран приветствия
        var welcome = {
            type: jsPsychHtmlKeyboardResponse,
            stimulus: `
                <h1>Добро пожаловать!</h1>
                <p>Сейчас появится вопрос.</p>
                <p>Нажмите пробел, чтобы начать.</p>
            `,
            choices: [' '] // Ожидаем нажатие пробела
        };

        // 2. Тестовый вопрос с картинкой (здесь будет ссылка на пример картинки)
        var test_question = {
            type: jsPsychHtmlButtonResponse, // Плагин для кнопок (замеряет время реакции)
            stimulus: `
                <h2>Какой это цвет?</h2>
                <img src='https://via.placeholder.com/150/0000FF/808080 ?text=Blue' style='width:150px;height:150px;'>
                <p><i>(время пошло...)</i></p>
            `,
            choices: ['Синий', 'Красный', 'Зеленый', 'Желтый'], // Варианты ответа
            button_html: '<button class="jspsych-btn" style="margin:10px; padding:10px 20px;">%choice%</button>',
            data: {
                trial_id: 'test_question', // Метка, чтобы легко найти этот вопрос в данных
                correct_answer: 'Синий'
            },
            on_finish: function(data) {
                // Проверяем правильность ответа
                data.correct = data.response === 'Синий';
            }
        };

        // 3. Экран благодарности (с паузой)
        var thanks = {
            type: jsPsychHtmlKeyboardResponse,
            stimulus: `
                <h1>Спасибо за участие!</h1>
                <p>Ваши данные сохранены.</p>
            `,
            choices: [' '],
            trial_duration: 3000 // Показываем 3 секунды, потом конец
        };

        // Собираем все шаги в массив
        var timeline = [welcome, test_question, thanks];

        // Запускаем тест
        jsPsych.run(timeline);
    </script>
</body>
</html>
```

### Как это работает:
*   **Таймер:** Плагин `html-button-response` автоматически замеряет время между отображением стимула и нажатием кнопки. Это значение сохраняется в переменной `rt` (reaction time).
*   **Данные:** В консоли браузера (F12) вы увидите JSON со всеми ответами и временем реакции.

## Часть 2. Как разместить онлайн (Бесплатно)

Самый простой и надежный способ — **GitHub Pages**. Это бесплатный хостинг статических сайтов (HTML, CSS, JS) от GitHub.

### Инструкция:

1.  **Зарегистрируйтесь на GitHub** (если нет аккаунта): [github.com](https://github.com)
2.  **Создайте новый репозиторий:**
    *   Нажмите зеленую кнопку "New".
    *   Назовите его, например, `my-jspsych-test` (или `ваш-логин.github.io`, если хотите, чтобы сайт был главным).
    *   Оставьте публичным (Public).
    *   Нажмите "Create repository".

3.  **Загрузите ваш тест:**
    *   На странице репозитория нажмите "Add file" -> "Upload files".
    *   Перетащите туда папку `my_jspsych_test` или отдельно файл `index.html` (и картинки, если они у вас есть локально).
    *   Внизу нажмите "Commit changes".

4.  **Включите GitHub Pages:**
    *   Зайдите в репозиторий, откройте вкладку **Settings**.
    *   В левом меню найдите раздел **Pages**.
    *   В разделе "Branch" выберите ветку `main` (или `master`) и папку `/root`.
    *   Нажмите "Save".

5.  **Получите ссылку:**
    *   Через минуту страница перезагрузится, и вы увидите сообщение: *"Your site is published at `https://ваш-логин.github.io/my-jspsych-test/`"*

Готово! Теперь любой человек в мире может пройти ваш тест по этой ссылке. Данные пока будут сохраняться только в консоль браузера.

## Часть 3. Как сохранять результаты (Сбор данных)

Чтобы не копировать данные из консоли вручную, нужно настроить отправку результатов на сервер или в Google Таблицы.

### Самый простой способ: Google Таблицы (через SheetDB или Apps Script)

Используйте **SheetDB.io**

1.  Создайте Google Таблицу.
2.  Зайдите на [sheetdb.io](https://sheetdb.io/), нажмите "Create a SheetDB" и авторизуйте свой Google-аккаунт.
3.  Вы получите уникальный API-адрес (например, `https://sheetdb.io/api/v1/xxxxxxxxx`).
4.  Добавьте в ваш скрипт функцию отправки после окончания теста:

    ```javascript
    // Добавьте это в блок on_finish у jsPsych или создайте отдельную функцию
    function sendDataToSheet(data) {
        fetch('https://sheetdb.io/api/v1/ВАШ_КЛЮЧ', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({ data: [ { response: data.response, rt: data.rt, correct: data.correct } ] })
        });
    }
    ```

### Резюме

1.  Скопируйте **код из Части 1** в `index.html`.
2.  Залейте на **GitHub Pages** (Часть 2) для получения ссылки.
3.  Если нужно сохранять результаты, используйте **Cognition.run** (заливка ZIP-архива) — это займет 5 минут и даст готовую базу данных.
