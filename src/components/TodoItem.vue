<template>
  <li
    class="list-group-item d-flex align-items-center justify-content-between"
    :class="
      todo.priority === `1`
        ? `bg-primary bg-gradient`
        : todo.priority === `2`
        ? `bg-warning bg-gradient`
        : todo.priority === `3`
        ? `bg-danger bg-gradient`
        : `bg-light bg-gradient`
    "
  >
    <div>
      <input
        class="form-check-input me-3"
        type="checkbox"
        :checked="todo.done"
        @change="toggleTodo(todo)"
      />
    </div>
    <input
      class="form-control form-control-plaintext p-1"
      :class="{ 'text-primary': todo.done }"
      type="text"
      :value="todo.text"
      @keyup.enter="doneEdit"
      @keyup.esc="cancelEdit"
      @blur="doneEdit"
    />
    <button
      type="button"
      class="btn-close btn-sm ms-2"
      @click="open = true"
    ></button>
  </li>
  <Teleport to="body">
    <div class="modal-overlay" v-if="open">
      <div class="modal-content">
        <h1>Attention...</h1>
        <p>Do you want to remove task?</p>
        <div class="d-flex justify-content-evenly align-items-end mt-5">
          <button @click="open = false">Close</button>
          <button @click="removeTodo(todo)">Remove</button>
        </div>
      </div>
    </div>
  </Teleport>
</template>

<script>
import { mapActions } from "vuex";

export default {
  name: "TodoItem",

  props: {
    todo: Object,
  },
  data() {
    return {
      showModal: false,
      open: false,
    };
  },
  methods: {
    ...mapActions(["toggleTodo", "editTodo", "removeTodo"]),
    doneEdit(e) {
      const value = e.target.value.trim();
      const todo = this.todo;
      if (!value) {
        this.removeTodo(todo);
      } else {
        this.editTodo({
          todo,
          value,
        });
      }
    },
    cancelEdit(e) {
      e.target.blur();
    },
  },
};
</script>

<style>
.modal-overlay {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  display: grid;
  justify-content: center;
  align-items: center;
  background-color: rgba(0, 0, 0, 0.3);
}
.modal-content {
  padding: 20px;
  background: #fff;
  border-radius: 2px;
  display: inline-block;
  min-height: 150x;
  margin: 1rem;
  position: relative;
  min-width: 300px;
  box-shadow: 0 3px 6px rgba(0, 0, 0, 0.16), 0 3px 6px rgba(0, 0, 0, 0.23);
  justify-self: center;
}
</style>
