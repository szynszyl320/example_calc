<script>
    import { onMount } from 'svelte';

    let history = $state([]);
    let calcContent = $state('0');
    let score = $derived(tryEval(calcContent));

    onMount(() => {
        loadHistory();
    });

    function tryEval(value) {
        try {
            const processed = value.replace(/\^/g, '**');
            const result = Function('return ' + processed)();
            return typeof result === 'number' ? result : 'calculating';
        } catch {
            return 'calculating';
        }
    }

    function appendDigit(digit) {
        calcContent = calcContent === '0' ? String(digit) : calcContent + digit;
    }

    function appendOperator(operator) {
        if (!(['+', '-', '*', '/', '^', '(', ')'].includes(calcContent.slice(-1)))) {
            calcContent = calcContent + operator;
        }
    }

    function saveToHistory() {
        history.push(
            {
                'score': score,
                'calculation': calcContent
            }
        );
        if (typeof window !== 'undefined') {
            localStorage.setItem('simpleCalculator', JSON.stringify(history));
        }
    }

    function clearHistory() {
        history = [];
        if (typeof window !== 'undefined') {
            localStorage.setItem('simpleCalculator', JSON.stringify(history));
        }
    }

    function loadHistory() {
        if (typeof window !== 'undefined') {
            const content = localStorage.getItem('simpleCalculator');
            history = content ? JSON.parse(content) : [];
        }
    }

    function clearInput() {
        calcContent = '0';
    }

</script>

<main class="calculator-container">
    <div class="calculator">
        <div class="display">
            <div class="result">{score}</div>
            <textarea 
                bind:value={calcContent}
                rows="1"
                spellcheck="false"
                oninput={(e) => {
                    e.target.style.height = 'auto';
                    e.target.style.height = e.target.scrollHeight + 'px';
                }}
            ></textarea>
        </div>
        <div class="keypad">
            <button class="btn operator" onclick={() => clearInput()}>AC</button>
            <button class="btn operator" onclick={() => appendOperator('(')}>(</button>
            <button class="btn operator" onclick={() => appendOperator(')')}>)</button>
            <button class="btn operator" onclick={() => appendOperator('/')}>/</button>

            <button class="btn" onclick={() => appendDigit(7)}>7</button>
            <button class="btn" onclick={() => appendDigit(8)}>8</button>
            <button class="btn" onclick={() => appendDigit(9)}>9</button>
            <button class="btn operator" onclick={() => appendOperator('*')}>×</button>

            <button class="btn" onclick={() => appendDigit(4)}>4</button>
            <button class="btn" onclick={() => appendDigit(5)}>5</button>
            <button class="btn" onclick={() => appendDigit(6)}>6</button>
            <button class="btn operator" onclick={() => appendOperator('-')}>−</button>

            <button class="btn" onclick={() => appendDigit(1)}>1</button>
            <button class="btn" onclick={() => appendDigit(2)}>2</button>
            <button class="btn" onclick={() => appendDigit(3)}>3</button>
            <button class="btn operator" onclick={() => appendOperator('+')}>+</button>

            <button class="btn" onclick={() => appendDigit(0)}>0</button>
            <button class="btn" onclick={() => appendDigit('.')}>.</button>
            <button class="btn operator" onclick={() => appendOperator('^')}>^</button>
            <button class="btn equals" onclick={() => saveToHistory()}>＝</button>
        </div>
    </div>

    <div class="history">
        <div class="history-header">
            <h3>History</h3>
            <button class="clear-history-btn" onclick={() => clearHistory()}>Clear</button>
        </div>
        {#each history as calculation}
            <div class="history-item">{calculation.calculation} = {calculation.score}</div>
        {/each}
    </div>
</main>

<style>
    .calculator-container {
        display: flex;
        gap: 2rem;
        justify-content: center;
        padding: 2rem;
    }

    .calculator {
        background: #1c1c1c;
        border-radius: 1rem;
        padding: 1.5rem;
        width: 320px;
        box-shadow: 0 0 20px rgba(0, 0, 0, 0.2);
    }

    .display {
        background: #3b3b3b;
        padding: 1rem;
        border-radius: 0.5rem;
        margin-bottom: 1rem;
        min-height: 100px;
        display: flex;
        flex-direction: column;
    }

    .display textarea {
        width: 100%;
        background: transparent;
        border: none;
        color: white;
        font-size: 1.5rem;
        text-align: right;
        outline: none;
        resize: none;
        overflow: hidden;
        font-family: inherit;
        line-height: 1.5;
        min-height: 2.25rem;
    }

    .result {
        color: #a0a0a0;
        text-align: right;
        font-size: 1rem;
        min-height: 1.5rem;
        margin-bottom: 0.5rem;
        word-break: break-all;
    }

    .keypad {
        display: grid;
        grid-template-columns: repeat(4, 1fr);
        gap: 0.5rem;
    }

    .btn {
        padding: 1rem;
        font-size: 1.25rem;
        border: none;
        border-radius: 0.5rem;
        background: #333;
        color: white;
        cursor: pointer;
        transition: background-color 0.2s;
    }

    .btn:hover {
        background: #444;
    }

    .operator {
        background: #ff9500;
    }

    .operator:hover {
        background: #ffaa33;
    }

    .equals {
        background: #217af4;
    }

    .equals:hover {
        background: #4495ff;
    }

    .history {
        background: #2a2a2a;
        padding: 1rem;
        border-radius: 1rem;
        min-width: 250px;
        max-height: 500px;
        overflow-y: auto;
    }

    .history-header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 1rem;
        padding-bottom: 0.5rem;
        border-bottom: 1px solid #3a3a3a;
    }

    .clear-history-btn {
        background: #444;
        color: #fff;
        border: none;
        padding: 0.5rem 1rem;
        border-radius: 0.25rem;
        cursor: pointer;
        font-size: 0.9rem;
        transition: background-color 0.2s;
    }

    .clear-history-btn:hover {
        background: #555;
    }

    .history-item {
        color: #ddd;
        padding: 0.5rem;
        border-bottom: 1px solid #3a3a3a;
    }
</style>
