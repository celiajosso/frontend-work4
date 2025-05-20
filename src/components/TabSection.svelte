<script>
  let { title, list, updateTodo, deleteTodo } = $props();
</script>

<div class="todo-section">
  <h3 class="section-header">{title}</h3>
  <div class="todo-list row">
    <div class="col-sm-6 col-sm-offset-3">
      {#each list as todo}
        <div class="checkbox">
          <label>
            <input
              type="checkbox"
              onclick={() => updateTodo(todo._id)}
              checked={todo.done}
            />
            <span
              class:text-muted={todo.done}
              class:text-decoration-line-through={todo.done}>{todo.text}</span
            >
          </label>
          <span
            class="pull-right"
            style="cursor: pointer; color: red"
            onclick={() => deleteTodo(todo._id)}>X</span
          >
        </div>
      {/each}
    </div>
  </div>
</div>
