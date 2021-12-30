<script>
export default {
  name: 'TodoContainer',
  props: {
    todo: Array
  },
  data() {
    return {
      propedTodo: JSON.parse(JSON.stringify(this.todo)),
    }
  },
}
</script>

<template>
  <div>
    <div class="container">
      <input
        type="checkbox"
        v-model="propedTodo[0]"
        :id="todo[2]"
        @change="$emit('changeState', $event)"
        class="todo-checkbox"
      />
      <label :for="todo[2]" class="todo-label">{{ todo[1] }}</label>
    </div>
    <button class="btn" @click="$emit('removeTodo', $event)">
      <img src="../images/icon-cross.svg" alt="" :data-todo-id="todo[2]" />
    </button>
  </div>
</template>

<style scoped>
.container{
  display: flex;
  align-items: center;
}
.todo-checkbox{
  appearance: none;
  border-radius: 50%;
  border: 1px solid rgba(229 231 235 / 0.5);
  position: relative;
  padding: 12px;
  cursor: pointer;
}
.todo-checkbox:checked{
  background: linear-gradient(
    to right bottom,
    hsl(192, 100%, 67%),
    hsl(280, 87%, 65%)
  );
}
.todo-checkbox:checked::after,
.done-checkbox:checked::after {
  content: url("../images/icon-check.svg");
  width: 11px;
  height: 9px;
  position: absolute;
  top: 25%;
  right: 25%;
}
.todo-checkbox:checked + .todo-label {
  text-decoration: line-through;
  color: var(--light-grayish-blue);
}
</style>
