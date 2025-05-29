<script>
    import { onMount } from 'svelte';

    const history = $state([]);

    let calcContent = $state(0);
    let score = $derived(typeof calcContent == 'symbol');

</script>
<main>
    <h1>Calculator</h1>
    <section>
        <h3>{ score }</h3>
        <input type="text" bind:value={calcContent} />
        <article>
            <button onclick={() => calcContent += 1}>1</button>
            <button onclick={() => calcContent += 2}>2</button>
            <button onclick={() => calcContent += 3}>3</button>
            <button onclick={() => calcContent += 4}>4</button>
            <button onclick={() => calcContent += 5}>5</button>
            <button onclick={() => calcContent += 6}>6</button>
            <button onclick={() => calcContent += 7}>7</button>
            <button onclick={() => calcContent += 8}>8</button>
            <button onclick={() => calcContent += 9}>9</button>
            <button onclick={() => calcContent += '+'}>+</button>
            <button onclick={() => calcContent += '-'}>-</button>
        </article>
    </section>
</main>
