<script lang="ts">
    let todos = $state([
        {text:'read docs', done:false}
    ]);

    // cl reactive variable
    //$effect(() => {
    //    console.log(todos);
    //})

    function addTodo(event: KeyboardEvent) {
        if (event.key != 'Enter') return

        const todoElement = event.target as HTMLInputElement;
        // const id = window.crypto.randomUUID();
        const text = todoElement.value
        const done = false;
        todos=[...todos, {text, done}];

        todoElement.value = '';
    }

    function editTodo(event: Event) {
        const inputElement = event.target as HTMLInputElement;
        const index = +inputElement.dataset.index!;
        todos[index].text = inputElement.value;
    }

    function toggleTodo(event:Event) {
        const inputElement = event.target as HTMLInputElement;
        const index = +inputElement.dataset.index!;
        todos[index].done = !todos[index].done;
    }

</script>

<input onkeydown={addTodo} placeholder="Add todo" type="text">

<div class="todos">
    {#each todos as todo, i}
        <div class="todo">
            <input oninput={editTodo} data-index={i} value={todo.text} type="text"> 
            <input onchange={toggleTodo} data-index={i} checked={todo.done} type="checkbox">
        </div>
    {/each}
</div>

<style>
    .todos {
        display: grid;
        gap: 1rem;
        margin-block-start: 1rem;
    }
</style>