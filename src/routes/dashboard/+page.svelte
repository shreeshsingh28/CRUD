<script>
    import { Alert } from 'flowbite-svelte';
    import { db } from "../../lib/firebase/firebase";
    import { authHandlers, authStore } from "../../store/store";
    import { getDoc, doc, setDoc } from "firebase/firestore";
    import TodoItem from "../../components/TodoItem.svelte";
    import "../../app.css";

    let todoList = [];
    let currTodo = "";
    let error = false;

    authStore.subscribe((curr) => {
        todoList = curr.data.todos;
    });

    let showModal = false;
    let backgroundColor1 = "#ffffff";
    let backgroundColor2 = "#ffffff"; // Initial background color
    let textColor = "#000000"; // Initial text color
    let buttonColor = "#0000ff"; // Initial button color

    function addTodo() {
        error = false;
        if (!currTodo) {
            error = true;
            return;
        }
        todoList = [...todoList, currTodo];
        currTodo = "";
    }

    function editTodo(index) {
        let newTodoList = [...todoList].filter((val, i) => i !== index);
        currTodo = todoList[index];
        todoList = newTodoList;
    }

    function removeTodo(index) {
        let newTodoList = [...todoList].filter((val, i) => i !== index);
        todoList = newTodoList;
    }

    async function saveTodos() {
        try {
            const userRef = doc(db, "users", $authStore.user.uid);
            await setDoc(userRef, { todos: todoList }, { merge: true });
        } catch (err) {
            console.log("There was an error saving your information", err);
        }
    }

    function switchToLightMode() {
        document.documentElement.classList.remove("dark");
        document.documentElement.classList.remove("custom");
    }

    function switchToDarkMode() {
        document.documentElement.classList.remove("custom");
        document.documentElement.classList.add("dark");
    }

    function openCustomOptions() {
        showModal = true;
    }

    function closeCustomOptions() {
        showModal = false;
    }

    function applyColors() {
        document.documentElement.classList.remove("dark");
        document.documentElement.classList.add("custom");
        document.documentElement.style.setProperty('--background-color1', backgroundColor1);
        document.documentElement.style.setProperty('--background-color2', backgroundColor2);
        document.documentElement.style.setProperty('--text-color', textColor);
        document.documentElement.style.setProperty('--button-color', buttonColor);
        closeCustomOptions(); // Close the modal after applying colors
    }
</script>

<div class="p-8">
    <Alert>
      <span class="font-medium">Info alert!</span>
      Change a few things up and try submitting again.
    </Alert>
  </div>

{#if !$authStore.loading}
    <div class="mainContainer">
        <div class="headerContainer">
            <h1>Todo List</h1>
            <div class="headerBtns">
                <!-- Existing Buttons -->
                <button on:click={saveTodos}>
                    <i class="fa-regular fa-floppy-disk"></i>
                    <p>Save</p>
                </button>
                <button on:click={authHandlers.logout}>
                    <i class="fa-solid fa-right-from-bracket"></i>
                    <p>Logout</p>
                </button>

                <!-- New Buttons -->
                <button on:click={switchToLightMode}>
                    <i class="fa-solid fa-sun"></i>
                    <p>Light</p>
                </button>
                <button on:click={switchToDarkMode}>
                    <i class="fa-solid fa-moon"></i>
                    <p>Dark</p>
                </button>
                <button on:click={openCustomOptions}>
                    <i class="fa-solid fa-cogs"></i>
                    <p>Custom</p>
                </button>
            </div>
        </div>

        <main>
            {#if todoList.length === 0}
                <p>You have nothing to do!</p>
            {/if}
            {#each todoList as todo, index}
                <TodoItem {todo} {index} {removeTodo} {editTodo} />
            {/each}
        </main>
        <div class={"enterTodo " + (error ? "errorBorder" : "")}>
            <input bind:value={currTodo} type="text" placeholder="Enter todo" />
            <button on:click={addTodo}>ADD</button>
        </div>
    </div>

    <!-- Custom Options Modal -->
    {#if showModal}
        <div class="fixed top-0 left-0 w-full h-full flex justify-center items-center bg-gray-800 bg-opacity-50">
            <div class="bg-white p-4 rounded-lg shadow-lg">
                <h2 class="text-lg font-bold mb-4">Customize Colors</h2>
                <div class="mb-4">
                    <label for="backgroundColor" class="block text-sm font-medium text-gray-700">
                        Background Color: {backgroundColor1}
                    </label>
                    <input
                        type="color"
                        id="backgroundColor"
                        bind:value={backgroundColor1}
                        class="w-full h-10 rounded border-gray-300 shadow-sm focus:border-blue-500 focus:ring focus:ring-blue-200 focus:ring-opacity-50"
                    />
                </div>
                <div class="mb-4">
                    <label for="backgroundColor" class="block text-sm font-medium text-gray-700">
                        Background Color: {backgroundColor2}
                    </label>
                    <input
                        type="color"
                        id="backgroundColor"
                        bind:value={backgroundColor2}
                        class="w-full h-10 rounded border-gray-300 shadow-sm focus:border-blue-500 focus:ring focus:ring-blue-200 focus:ring-opacity-50"
                    />
                </div>
                <div class="mb-4">
                    <label for="textColor" class="block text-sm font-medium text-gray-700">
                        Text Color: {textColor}
                    </label>
                    <input
                        type="color"
                        id="textColor"
                        bind:value={textColor}
                        class="w-full h-10 rounded border-gray-300 shadow-sm focus:border-blue-500 focus:ring focus:ring-blue-200 focus:ring-opacity-50"
                    />
                </div>
                <div class="mb-4">
                    <label for="buttonColor" class="block text-sm font-medium text-gray-700">
                        Button Color: {buttonColor}
                    </label>
                    <input
                        type="color"
                        id="buttonColor"
                        bind:value={buttonColor}
                        class="w-full h-10 rounded border-gray-300 shadow-sm focus:border-blue-500 focus:ring focus:ring-blue-200 focus:ring-opacity-50"
                    />
                </div>
                <div class="flex justify-end">
                    <button
                        class="bg-blue-500 text-white px-4 py-2 rounded-md shadow-sm hover:bg-blue-600"
                        on:click={applyColors}>Apply
                    </button>
                    <button
                        class="bg-gray-300 text-gray-700 px-4 py-2 ml-2 rounded-md shadow-sm hover:bg-gray-400"
                        on:click={closeCustomOptions}>Cancel
                    </button>
                </div>
            </div>
        </div>
    {/if}
{/if}

<style>
    .mainContainer {
        display: flex;
        flex-direction: column;
        min-height: 100vh;
        gap: 24px;
        padding: 24px;
        width: 100%;
        max-width: 1000px;
        margin: 0 auto;
    }

    main {
        display: flex;
        flex-direction: column;
        gap: 8px;
        flex: 1;
    }

    .errorBorder {
        border-color: coral !important;
    }
</style>
