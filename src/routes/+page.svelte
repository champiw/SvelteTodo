<script lang="ts">

    type Todo = {
        text: string
        done: boolean
    }

    type Filters = 'all' | 'active' | 'completed';

    let todos = $state<Todo[]>([]);
    let filter = $state<Filters>('all');
    let filteredTodos = $derived(filterTodos());

    let error = $state('');

    //cl reactive variable
    // $effect(() => {
    //    console.log(todos);
    //    console.log(filter);
    // })

    // Executes when component is mounted, gets todos from Localstorage if possible
    $effect(() => {
        const savedTodos = localStorage.getItem('todos');
        if (savedTodos) todos = JSON.parse(savedTodos);
    });
    
    // Executes when todos is changed to save in localstorage
    $effect(() => {
        localStorage.setItem('todos', JSON.stringify(todos));
    });

    function addTodo(event: KeyboardEvent) {
        if (event.key != 'Enter') return

        const todoElement = event.target as HTMLInputElement;
        // const id = window.crypto.randomUUID();
        const text = todoElement.value
        const done = false;

        if (text.length != 0) {
            error = '';

            todos=[...todos, {text, done}];

            todoElement.value = '';
        }
        else error = 'Please type something'

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

    function deleteTodo(event:Event) {
        const inputElement = event.target as HTMLInputElement;
        const index = +inputElement.dataset.index!;
        todos.splice(index,1);
    }

    function setFilter(newFilter: Filters) {filter = newFilter}

    function filterTodos() {
        switch (filter) {
            case 'all':
                return todos;
            case 'active':
                return todos.filter(todo => !todo.done);
            case "completed":
                return todos.filter(todo => todo.done);
        }
    }

    function remaining() {
        return todos.filter(todo => !todo.done).length;
    }

</script>

<input onkeydown={addTodo} placeholder="Add todo" type="text">

<p>{error}</p>

<div class="todos">
    {#each filteredTodos as todo, i}
        <div class:completed={todo.done} class="todo">
            <input oninput={editTodo} data-index={i} value={todo.text} type="text"> 
            <input onchange={toggleTodo} data-index={i} checked={todo.done} type="checkbox">
            <button onclick={deleteTodo} data-index={i}>Delete</button>
        </div>
    {/each}
</div>

<div class="filters">
    <button onclick={()=>setFilter('all')} aria-label='filter button'>All</button>
    <button onclick={()=>setFilter('active')} aria-label='filter button'>Active</button>
    <button onclick={()=>setFilter('completed')} aria-label='filter button'>Completed</button>
</div>

<p>{remaining()} remaining
</p>

<style>
    .todos {
        display: grid;
        gap: 1rem;
        margin-block-start: 1rem;
    }

    .todo {
        position: relative;
        transition: opacity 0.3s;
    }

    .completed {
        opacity: 0.4;
    }
</style>