<!-- src/App.svelte -->
<script>
    import {onMount} from "svelte"

    let username = ''
    let password = ''
    let loggedIn = false
    let error = ''
    let currentQuestionIndex = 0 // Текущий индекс вопроса для отображения
    let answers = {}
    let selectedAnswer = null
    let testCompleted = false

    const users = [
        { username: 'Diana', password: '54489654321' },
        { username: 'Diana', password: '0101' }
        // Добавь сюда другие учетные записи
    ];

    const questions = [
        {
            id: 1,
            text: 'Как я предпочитаю проводить свободное время?',
            options: ['Чтение книг или просмотр фильмов', 'Активные виды отдыха: спорт, путешествия', 'Общение с друзьями на вечеринках или в кафе'],
            correctAnswer: 2
        },
        {
            id: 2,
            text: 'Что для меня важнее в отношениях?',
            options: ['Глубокие эмоциональные связи и поддержка', 'Совместные интересы и активности', 'Взаимное уважение'],
            correctAnswer: 2
        },
        {
            id: 3,
            text: 'Какой мой любимый способ общения?',
            options: ['Глубокие разговоры на разные темы', 'Веселые и легкие беседы, шутки', 'Общение без слов: жесты, прикосновения'],
            correctAnswer: 0
        },
        {
            id: 4,
            text: 'Как я представляю свой отдых?',
            options: ['Уютные домашние вечера вдвоем', 'Активный отдых на природе или на мероприятиях', 'Отдых с друзьями, на вечеринках'],
            correctAnswer: 0
        },
        {
            id: 5,
            text: 'Что для меня важнее в плане совместного проживания?',
            options: ['Общие цели и планы на будущее', 'Совместная деятельность и участие друг в жизни партнера', 'Личное пространство и самостоятельность'],
            correctAnswer: 1
        },
        {
            id: 6,
            text: 'Как я отношусь к планированию будущего?',
            options: ['Люблю строить планы и следовать им', 'Предпочитаю жить настоящим и адаптироваться к обстоятельствам', 'Планирую общие цели, но оставляю место для изменений'],
            correctAnswer: 2
        },
        {
            id: 7,
            text: 'Как я реагируете на конфликты?',
            options: ['Стремлюсь к мирному разрешению конфликтов', 'Предпочитаю обсуждать и найти компромисс', 'Могу быть эмоциональным в процессе конфликта'],
            correctAnswer: 1
        },
        {
            id: 8,
            text: 'Как я предпочитаю планировать отпуск?',
            options: ['Детально продумываю каждую часть путешествия', 'Предпочитаю гибкий план и импровизацию во время отпуска', 'Предпочитаю отдыхать без строгого планирования'],
            correctAnswer: 1
        },
        {
            id: 9,
            text: 'Как я отношусь к планированию финансов?',
            options: ['Строго следую бюджету и планирую каждую трату', 'Предпочитаю гибкий подход к расходам', 'Финансы - общее дело, принимаем решения вместе'],
            correctAnswer: 2
        },
        {
            id: 10,
            text: 'Как я отношусь к саморазвитию в отношениях?',
            options: ['Важно постоянно развиваться вместе', 'Предпочитаю личное развитие без прямой связи с отношениями', 'Считаю, что отношения сами по себе способствуют развитию личности'],
            correctAnswer: 0
        },
    ];

    // Проверка на наличие сохраненной авторизации при загрузке компонента
    onMount(() => {
        const storedUser = localStorage.getItem('user');
        if (storedUser) {
            const parsedUser = JSON.parse(storedUser);
            const user = users.find(u => u.username === parsedUser.username && u.password === parsedUser.password);
            if (user) {
                loggedIn = true;
            }
        }
    });

    function login() {
        const user = users.find(u => u.username === username && u.password === password);
        if (user) {
            loggedIn = true;
            localStorage.setItem('user', JSON.stringify({ username, password }));
            error = '';
        } else {
            error = 'Неверное имя пользователя или пароль.';
        }
    }

    function selectOption(index) {
        selectedAnswer = index;
    }

    function submitAnswer() {
        if (selectedAnswer !== null) {
            answers[`question${currentQuestionIndex + 1}`] = selectedAnswer;
            if (currentQuestionIndex < questions.length - 1) {
                currentQuestionIndex++;
                selectedAnswer = null; // Сбрасываем выбранный ответ после перехода к следующему вопросу
            } else {
                testCompleted = true; // Помечаем тест как завершенный, если это последний вопрос
            }
        }
    }

    function calculateScore() {
        let correctCount = 0;
        for (let i = 0; i < questions.length; i++) {
            const userAnswerIndex = answers[`question${i + 1}`];
            if (userAnswerIndex === questions[i].correctAnswer) {
                correctCount++;
            }
        }
        const totalQuestions = questions.length;
        const score = (correctCount / totalQuestions) * 100;
        return { correctCount, totalQuestions, score };
    }


    function submitTest() {
        let correctCount = 0;
        for (let i = 0; i < questions.length; i++) {
            const userAnswerIndex = questions[i].options.indexOf(answers[`question${i + 1}`]);
            if (userAnswerIndex === questions[i].correctAnswer) {
                correctCount++;
            }
        }
        const totalQuestions = questions.length;
        const score = (correctCount / totalQuestions) * 100;
        console.log(`Количество правильных ответов: ${correctCount}/${totalQuestions}`);
        console.log(`Ваш балл: ${score}%`);
    }
</script>

<style>
    /* Общие стили */
    .container {
        max-width: 600px;
        width: 90%;
        text-align: center;
        background-color: #fff;
        padding: 20px;
        border-radius: 8px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }

    .options label {
        display: block;
        margin-bottom: 10px;
    }

    .options input[type="radio"] {
        margin-right: 5px;
        cursor: pointer;
    }

    input[type="text"],
    input[type="password"] {
        margin-bottom: 10px;
        padding: 8px;
        width: 100%;
        border: 1px solid #ccc;
        border-radius: 4px;
        box-sizing: border-box;
        font-size: 16px;
    }

    button {
        padding: 10px 20px;
        margin-top: 10px;
        border: none;
        border-radius: 4px;
        background-color: #007bff;
        color: #fff;
        cursor: pointer;
        font-size: 16px;
    }

    button:hover {
        background-color: #0056b3;
    }

    /* Стили для результатов теста */
    .results {
        margin-top: 20px;
        text-align: left;
    }

    .results-p {
        margin: 5px 0;
    }

    /* Стили для ошибки */
    .error {
        color: red;
        margin-top: 10px;
    }
</style>

<div class="container">
    {#if loggedIn}
        <p>Здравствуйте, {username}!</p>
        {#if !testCompleted && currentQuestionIndex < questions.length}
            <h2>{questions[currentQuestionIndex].text}</h2>
            <form on:submit|preventDefault={submitTest} class="options">
                {#each questions[currentQuestionIndex].options as option, index}
                    <label>
                        <input type="radio" name="options" value={index} on:change={() => selectOption(index)} />
                        {option}
                    </label>
                {/each}
                <button on:click={submitAnswer}>Подтвердить ответ</button>
            </form>
        {:else}
            {#if testCompleted}
                <h2 class="results">Результаты теста:</h2>
                {#if calculateScore()}
                    <p class="results-p">Количество правильных ответов: {calculateScore().correctCount}/{calculateScore().totalQuestions}</p>
                    <p class="results-p">Ваш балл: {calculateScore().score}%</p>
                {/if}
            {:else}
                <p>Тест завершен, спасибо!</p>
            {/if}
        {/if}
    {:else}
        <form on:submit|preventDefault={login}>
            <label>
                Имя пользователя:
                <input type="text" bind:value={username} />
            </label>
            <label>
                Пароль:
                <input type="password" bind:value={password} />
            </label>
            <button type="submit">Войти</button>
            {#if error}
                <p class="error">{error}</p>
            {/if}
        </form>
    {/if}
</div>

