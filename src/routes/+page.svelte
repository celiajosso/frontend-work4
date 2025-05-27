<script>
  import { onMount } from "svelte";
  import TabSection from "$lib/components/TabSection.svelte";
  let todos = [];
  let formData = { text: "" };
  let cos = "";
  let currentTab = "all";

  onMount(async () => {
    const res = await fetch("http://localhost:4000/api/todos");
    todos = await res.json();
  });

  async function createTodo(event) {
    event.preventDefault();
    const res = await fetch("http://localhost:4000/api/todos", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(formData),
    });
    todos = await res.json();
    formData.text = "";
  }

  async function updateTodo(id) {
    const todo = todos.find((t) => t._id === id);
    todo.done = !todo.done;
    const res = await fetch(`http://localhost:4000/api/todos/${id}`, {
      method: "PUT",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ done: todo.done }),
    });
    todos = await res.json();
  }

  async function deleteTodo(id) {
    const res = await fetch(`http://localhost:4000/api/todos/${id}`, {
      method: "DELETE",
    });
    todos = await res.json();
  }

  $: activeTodos = todos.filter((t) => !t.done);
  $: completedTodos = todos.filter((t) => t.done);

  function setTab(tab) {
    currentTab = tab;
  }
</script>

<div class="container">
  <!-- HEADER AND TODO COUNT -->
  <div class="jumbotron text-center">
    <h1>
      nodeTODO {cos}
      <span class="label label-info">{todos.length}</span>
    </h1>
    <div class="row" id="todo-form">
      <div class="col-sm-8 col-sm-offset-2 text-center">
        <input
          type="text"
          class="form-control text-center"
          bind:value={cos}
          placeholder="TODO name"
        />
      </div>
    </div>
  </div>

  <!-- FORM TO CREATE TODOS -->
  <div id="todo-form" class="row">
    <div class="col-sm-8 col-sm-offset-2 text-center">
      <form on:submit={createTodo}>
        <div class="form-group">
          <input
            id="newTodo"
            type="text"
            class="form-control input-lg text-center"
            placeholder="What do you need to do?"
            bind:value={formData.text}
          />
        </div>
        <button type="submit" class="btn btn-primary btn-lg">Add</button>
      </form>
    </div>
  </div>

  <!-- NAVIGATION TABS -->
  <ul class="nav nav-tabs">
    <li class={currentTab === "all" ? "active" : ""}>
      <a href="#" on:click|preventDefault={() => setTab("all")}>
        All ({todos.length})
      </a>
    </li>
    <li class={currentTab === "todo" ? "active" : ""}>
      <a href="#" on:click|preventDefault={() => setTab("todo")}>
        Todo ({activeTodos.length})
      </a>
    </li>
    <li class={currentTab === "done" ? "active" : ""}>
      <a href="#" on:click|preventDefault={() => setTab("done")}>
        Done ({completedTodos.length})
      </a>
    </li>
  </ul>

  <!-- TABS CONTENT -->
  <div class="tab-content">
    {#if currentTab === "all"}
      <div class="tab-pane active">
        <TabSection title="All Tasks" list={todos} {updateTodo} {deleteTodo} />
      </div>
    {:else if currentTab === "todo"}
      <div class="tab-pane active">
        <TabSection
          title="Todo Tasks"
          list={activeTodos}
          {updateTodo}
          {deleteTodo}
        />
      </div>
    {:else if currentTab === "done"}
      <div class="tab-pane active">
        <TabSection
          title="Done Tasks"
          list={completedTodos}
          {updateTodo}
          {deleteTodo}
        />
      </div>
    {/if}
  </div>
</div>
