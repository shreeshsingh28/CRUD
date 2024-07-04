<script>
    export let index;
    export let todo;
    export let editTodo;
    export let removeTodo;
</script>

<div class="todo">
    <p>
        {index + 1}. {todo}
    </p>
    <div class="actions">
        <i
            on:click={() => {
                editTodo(index);
            }}
            on:keydown={() => {}}
            class="fa-regular fa-pen-to-square"
        />
        <i
            on:click={() => {
                removeTodo(index);
            }}
            on:keydown={() => {}}
            class="fa-regular fa-trash-can"
        />
    </div>
</div>

<style>

    .actions {
        display: flex;
        align-items: center;
        gap: 14px;
        font-size: 1.3rem;
    }

    .actions i {
        cursor: pointer;
    }

    .actions i:hover {
        color: coral;
    }
</style>
