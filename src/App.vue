<script>
import Header from "./components/TheHeader.vue";
import TodoContainer from "./components/TodoContainer.vue";
export default {
  name: "App",
  components: {
    Header,
    TodoContainer,
  },
  data() {
    return {
      newTodoCheck: false,
      newTodoInput: "",
      checkboxValue: "",
      listOfTodos: [],
      filterValue: "all",
    };
  },
  methods: {
    saveNewTodo() {
      this.listOfTodos.push([
        this.newTodoCheck,
        this.newTodoInput,
        Math.floor(Math.random() * 100),
      ]);
      this.newTodoInput = "";
      this.newTodoCheck = false;
      this.saveTodos();
    },
    clearCompleted() {
      this.listOfTodos = this.listOfTodos.filter((todo) => todo[0] == false);
      this.saveTodos();
    },
    testing() {
      console.log(this.test);
    },
    changeState(e) {
      for (let todo of this.listOfTodos) {
        if (todo[2] == e.target.id) {
          console.log(todo)
          todo[0] = !todo[0];
        }
      }
      this.saveTodos();
    },
    removeTodo(e) {
      this.listOfTodos = this.listOfTodos.filter(
        (todo) => todo[2] != e.target.dataset.todoId
      );
      e.target.parentNode.parentNode.remove();
      this.saveTodos();
    },
    saveTodos() {
      localStorage.setItem("listOfTodos", JSON.stringify(this.listOfTodos));
    },
    dragOver(e) {
      let afterElement;
      if (e.type == "touchmove") {
        let evt = typeof e.originalEvent === "undefined" ? e : e.originalEvent;
        let touch = evt.touches[0] || evt.changedTouches[0];
        afterElement = this.getDragAfterElement(touch.pageY);
      } else {
        afterElement = this.getDragAfterElement(e.clientY);
      }
      const draggable = document.querySelector(".dragging");
      const todoList = document.querySelector("#todoList");
      if (afterElement == null) {
        todoList.appendChild(draggable);
      } else {
        todoList.insertBefore(draggable, afterElement);
      }
    },
    getDragAfterElement(y) {
      const todoList = document.querySelector("section");

      const draggableElements = [
        ...todoList.querySelectorAll(".todo-container:not(.dragging)"),
      ];

      return draggableElements.reduce(
        (closest, child) => {
          const box = child.getBoundingClientRect();
          const offset = y - box.top - box.height / 2;
          if (offset < 0 && offset > closest.offset) {
            return { offset: offset, element: child };
          } else {
            return closest;
          }
        },
        { offset: Number.NEGATIVE_INFINITY }
      ).element;
    },
  },
  computed: {
    filteredListOfTodos() {
      if (this.filterValue == "all") {
        return this.listOfTodos;
      } else if (this.filterValue == "active") {
        return this.listOfTodos.filter((todo) => !todo[0]);
      } else {
        return this.listOfTodos.filter((todo) => todo[0]);
      }
    },
  },
  created() {
    if (JSON.parse(localStorage.getItem("listOfTodos"))) {
      this.listOfTodos = JSON.parse(localStorage.getItem("listOfTodos"));
      //  localStorage.setItem("listOfTodos", JSON.stringify([]))
    } else {
      this.listOfTodos = [];
    }
  },
};
</script>
<template>
  <Header />
  <form @submit.prevent="saveNewTodo">
    <input
      type="checkbox"
      name=""
      id=""
      v-model="newTodoCheck"
      class="done-checkbox"
    />
    <input
      type="text"
      placeholder="Create a new todo..."
      v-model="newTodoInput"
    />
  </form>
  <section>
    <div id="todoList">
      <TodoContainer 
        class="todo-container" 
        v-for="todo in filteredListOfTodos"
        :key="todo[2]" 
        @dragstart="$event.target.classList.add('dragging')"
        @dragend="$event.target.classList.remove('dragging')"
        @touchmove="$event.currentTarget.classList.add('dragging');dragOver($event)"
        @touchend="$event.currentTarget.classList.remove('dragging')"
        @dragover.prevent="dragOver" 
        @changeState="changeState"
        @removeTodo="removeTodo"
        :todo="todo" 
        draggable="true"
        />
    </div>
    <div class="section__footer">
      <p>{{ filteredListOfTodos.length }} items left</p>
      <div class="filter-todo desktop">
        <div>
          <input
            type="radio"
            name="selectionDesktop"
            id="all"
            value="all"
            v-model="filterValue"
            checked
          />
          <label for="all" class="radio-label">All</label>
        </div>
        <div>
          <input
            type="radio"
            name="selectionDesktop"
            id="active"
            v-model="filterValue"
            value="active"
          />
          <label for="active" class="space-left radio-label">Active</label>
        </div>
        <div>
          <input
            type="radio"
            name="selectionDesktop"
            id="completed"
            v-model="filterValue"
            value="completed"
          />
          <label for="completed" class="space-left radio-label"
            >Completed</label
          >
        </div>
      </div>
      <button class="btn" @click="clearCompleted">Clear Completed</button>
    </div>
  </section>

  <div class="filter-todo mobile">
    <div>
      <input
        type="radio"
        name="selection"
        id="all"
        value="all"
        v-model="filterValue"
        checked
      />
      <label for="all" class="radio-label">All</label>
    </div>
    <div>
      <input
        type="radio"
        name="selection"
        id="active"
        v-model="filterValue"
        value="active"
      />
      <label for="active" class="space-left radio-label">Active</label>
    </div>
    <div>
      <input
        type="radio"
        name="selection"
        id="completed"
        v-model="filterValue"
        value="completed"
      />
      <label for="completed" class="space-left radio-label">Completed</label>
    </div>
  </div>

  <span>Drag and drop to reorder list</span>
</template>

<style>
@import "../public/style.css";
@import url("https://fonts.googleapis.com/css2?family=Josefin+Sans:ital,wght@0,400;1,700&display=swap");
*,
*::before,
*::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Josefin Sans", sans-serif;
}
body {
  display: flex;
  flex-direction: column;
  background: url("images/bg-mobile-light.jpg");
  background-repeat: no-repeat;
  background-size: 100% 200px;
  background-color: var(--light-gray);
  padding: 48px 24px;
  position: relative;
  transition: all 0.25s ease-in;
}
form {
  background-color: white;
  padding: 14px 20px;
  border-radius: 0.25rem;
  display: flex;
  align-items: center;
  width: 100%;
  max-width: 540px;
  margin: 0 auto;
}
input[type="text"] {
  margin-left: 1rem;
  width: 100%;
  border: none;
  font-size: 18px;
  height: 100%;
  background: transparent;
  caret-color: hsl(220, 98%, 61%);
}
input[type="text"]::placeholder {
  font-size: 14px;
}
input[type="text"]:focus {
  outline: none;
}
.btn {
  border: none;
  background-color: transparent;
  cursor: pointer;
}
section {
  background-color: #fff;
  display: flex;
  flex-direction: column;
  margin: 1rem auto;
  border-radius: 0.25rem;
  width: 100%;
  max-width: 540px;
}
.todo-container {
  display: flex;
  justify-content: space-between;
  padding: 12px 16px;
  border-bottom: 1px solid rgba(229 231 235 / 0.3);
}
.todo-container div {
  display: flex;
  align-items: center;
}
.todo-label {
  margin-left: 16px;
  cursor: pointer;
  color: var(--light-very-dark-grayish-blue);
}

.done-checkbox {
  appearance: none;
  border-radius: 50%;
  border: 1px solid rgba(229 231 235 / 0.5);
  position: relative;
  padding: 12px;
  cursor: pointer;
}
.todo-checkbox:checked,
.done-checkbox:checked {
  background: linear-gradient(
    to right bottom,
    hsl(192, 100%, 67%),
    hsl(280, 87%, 65%)
  );
}
.todo-checkbox:hover,
.done-checkbox:hover {
  border-color: linear-gradient(
    to right bottom,
    hsl(192, 100%, 67%),
    hsl(280, 87%, 65%)
  );
}
.todo-checkbox:checked + .todo-label {
  text-decoration: line-through;
  color: var(--light-grayish-blue);
}
.todo-checkbox:checked::after,
.done-checkbox:checked::after {
  content: url("images/icon-check.svg");
  width: 11px;
  height: 9px;
  position: absolute;
  top: 25%;
  right: 25%;
}
.section__footer {
  display: flex;
  justify-content: space-between;
  padding: 20px 24px;
  align-items: center;
  font-size: 12px;
  color: var(--light-dark-grayish-blue);
}
.section__footer .btn {
  color: var(--light-dark-grayish-blue);
}
.section__footer .btn:hover {
  color: var(--light-very-dark-grayish-blue);
}
.filter-todo {
  display: flex;
  background: white;
  width: 100%;
  max-width: 540px;
  border-radius: 0.25rem;
  padding: 14px 0;
  margin: 0 auto;
  justify-content: center;
}
.filter-todo input {
  display: none;
}
.radio-label {
  cursor: pointer;
  font-weight: 700;
  color: var(--light-dark-grayish-blue);
}
.radio-label:hover {
  color: var(--light-very-dark-grayish-blue);
}
.space-left {
  margin-left: 1.25rem;
}
.filter-todo input:checked + .radio-label {
  color: var(--primary-blue);
}
.dragging {
  opacity: 0.5;
}
.desktop {
  display: none;
}
span {
  display: flex;
  justify-content: center;
  margin: 44px;
  font-size: 14px;
  color: var(--light-dark-grayish-blue);
}
.dark {
  background: url("images/bg-mobile-dark.jpg");
  background-repeat: no-repeat;
  background-size: 100% 200px;
  background-color: var(--dark-blue);
  transition: all 0.25s ease-in;
}
.dark form {
  background-color: var(--dark-desaturated-blue);
}
.dark section {
  background-color: var(--dark-desaturated-blue);
}
.dark .filter-todo {
  background-color: var(--dark-desaturated-blue);
}
.dark input[type="text"],
.dark .todo-container .todo-label {
  color: var(--dark-grayish-blue);
}
.dark .todo-checkbox:checked + .todo-label {
  color: var(--dark-grayish-blue);
  opacity: 0.5;
}
.dark .radio-label:hover,
.dark .section__footer .btn:hover {
  color: var(--dark-grayish-blue-hover);
}
@media only screen and (min-width: 56.25rem) {
  body {
    background: url("images/bg-desktop-light.jpg");
    background-repeat: no-repeat;
    background-size: 100% 200px;
    background-color: var(--light-gray);
  }
  .dark {
    background: url("images/bg-desktop-dark.jpg");
    background-repeat: no-repeat;
    background-size: 100% 200px;
    background-color: var(--dark-blue);
  }
  .mobile {
    display: none;
  }
  .desktop {
    display: flex;
    width: auto;
    padding: 0;
  }
}
</style>
