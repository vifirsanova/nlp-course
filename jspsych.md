**jsPsych** — инструмент для создания тестов с замером времени (RT) 

## Часть 1. Создание теста

1. Экран приветствия
2. Тестовый вопрос с картинкой и вариантами ответа
3. **Замер времени реакции** (от появления вопроса до нажатия кнопки)
4. Экран благодарности и вывод результатов

Создайте файл `index.html`. Образец файла:

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
*   **Таймер:** плагин `html-button-response` автоматически замеряет время между отображением стимула и нажатием кнопки. Это значение сохраняется в переменной `rt` (reaction time)
*   **Данные:** в консоли браузера (F12) вы увидите JSON со всеми ответами и временем реакции

## Часть 2. Как разместить онлайн (бесплатно)

Самый простой и надежный способ — **GitHub Pages**. Это бесплатный хостинг статических сайтов (HTML, CSS, JS) от GitHub

### Инструкция:

1.  **Зарегистрируйтесь на GitHub** (если нет аккаунта): [github.com](https://github.com)
2.  **Создайте новый репозиторий:**
    *   Нажмите зеленую кнопку "New"
    *   Назовите его, например, `my-jspsych-test`
    *   Оставьте публичным (Public)
    *   Нажмите "Create repository"

3.  **Загрузите ваш тест:**
    *   На странице репозитория нажмите "Add file" -> "Upload files"
    *   Создайте `index.html` с кодом теста
    *   Нажмите "Commit changes"

4.  **Включите GitHub Pages:**
    *   Зайдите в репозиторий, откройте вкладку **Settings**
    *   В левом меню найдите раздел **Pages**
    *   В разделе "Source" выберите **GitHub Actions** и **Deploy HTML**
    *   "Commit", и через минутку ваш тест появится на https://ваш_юзернейм.github.io/название_репозитория
