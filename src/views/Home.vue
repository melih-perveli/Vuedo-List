<template>
  <h1 class="text-secondary mt-5 mb-4">To-Do</h1>
  <form @keydown.enter="addTodo">
    <input
      class="form-control form-control-lg mb-3 text-dark"
      type="text"
      :class="
        obj.priority === `1`
          ? `bg-primary bg-gradient`
          : obj.priority === `2`
          ? `bg-warning bg-gradient`
          : obj.priority === `3`
          ? `bg-danger bg-gradient`
          : `bg-light bg-gradient`
      "
      v-model="obj.titletext"
      autocomplete="off"
      placeholder="Write a task"
    />

    <div class="form-check form-check-inline">
      <input
        type="radio"
        name="radio"
        v-model="obj.priority"
        value="1"
        id="Low"
      />
      <label for="Low">Low</label>
    </div>
    <div class="form-check form-check-inline">
      <input
        type="radio"
        name="radio"
        value="2"
        v-model="obj.priority"
        id="Medium"
      />
      <label for="Medium">Medium</label>
    </div>

    <div class="form-check form-check-inline">
      <input
        type="radio"
        name="radio"
        value="3"
        v-model="obj.priority"
        id="High"
      />
      <label for="High">High</label>
    </div>
  </form>
  <ul class="list-group mt-3 mb-3" v-show="todos.length">
    <li
      class="list-group-item d-flex justify-content-between align-items-center"
    >
      <div>
        <input
          class="form-check-input me-3"
          type="checkbox"
          :checked="allChecked"
          @change="toggleAll(!allChecked)"
        />
        <span v-if="remaining != 0" class="text-secondary px-1">
          {{ remaining }} {{ pluralize(remaining, "task") }} left
        </span>
      </div>
      <div class="dropdowns">
        <button class="dropbtns">Sort By</button>
        <div class="dropdown-contents">
          <a @click="OpenAsc = false">Name</a>
          <a @click="OpenAsc = true">Priority</a>
        </div>
      </div>
    </li>
    <div v-if="OpenAsc == true">
      <TodoItem
        v-for="todo in sortedPriority(todos)"
        :key="todo"
        :todo="todo"
      />
    </div>
    <div v-if="OpenAsc == false">
      <TodoItem v-for="todo in sortedNames(todos)" :key="todo" :todo="todo" />
    </div>
  </ul>
  <button
    type="button"
    class="btn btn-primary w-100"
    v-show="todos.length > remaining"
    @click="clearCompleted"
  >
    Clear completed
  </button>
</template>

<script>
import { mapActions } from "vuex";
import TodoItem from "@/components/TodoItem.vue";

export default {
  name: "Home",
  props: {
    filter: String,
  },
  components: {
    TodoItem,
  },
  data() {
    return {
      obj: {
        priority: "",
        titletext: "",
      },
      OpenAsc: true,
    };
  },
  computed: {
    todos() {
      return this.$store.state.todos;
    },
    allChecked() {
      return this.todos.every((todo) => todo.done);
    },
    remaining() {
      return this.todos.filter((todo) => !todo.done).length;
    },
  },
  methods: {
    ...mapActions(["toggleAll", "clearCompleted"]),
    pluralize(n, w) {
      return n === 1 ? w : w + "s";
    },
    capitalize(s) {
      return s.charAt(0).toUpperCase() + s.slice(1);
    },

    addTodo(e) {
      if (e) {
        this.$store.dispatch("addTodo", this.obj);
        e.target.value = "";
      }
    },
    sortedPriority: (e) => {
      function priList(a, b) {
        if (a.priority > b.priority) return -1;
        if (a.priority < b.priority) return 1;
        return 0;
      }
      return e.sort(priList);
    },
    sortedNames: (e) => {
      function textList(a, b) {
        if (a.text < b.text) return -1;
        if (a.text > b.text) return 1;
        return 0;
      }
      return e.sort(textList);
    },
  },
};
</script>

<style>
.dropbtns {
  background-color: #fff;
  color: black;
  border: none;
  cursor: pointer;
}

.dropdowns {
  position: relative;
  display: inline-block;
}

.dropdown-contents {
  display: none;
  position: absolute;
  background-color: #f9f9f9;
  min-width: 100px;
  box-shadow: 0px 8px 16px 0px rgba(0, 0, 0, 0.2);
  z-index: 1;
}

.dropdown-contents a {
  color: black;
  padding: 12px 16px;
  text-decoration: none;
  display: block;
}

.dropdown-contents a:hover {
  background-color: #f1f1f1;
}

.dropdowns:hover .dropdown-contents {
  display: block;
}

.dropdowns:hover .dropbtns {
  background-color: #23d6cd;
}
</style>
