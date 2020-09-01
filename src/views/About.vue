<template>
  <div class="about">
    <input type="text" v-model="todo" v-on:keyup.enter="addTodo(editAt)" />
    <button @click="addTodo(editAt)" :disabled="!todo">
      <template v-if="editAt > -1">Done</template>
      <template v-else>Add Todo</template>
    </button>
    <br />
    <button @click="todoList = []" :disabled="!todoList.length">Remove all</button>
    <ul class="list">
      <li v-for="(item, index) in todoList" :key="item">
        <input
          type="text"
          v-if="editAt === index"
          v-model="todo"
          v-on:keyup.enter="addTodo(index)"
        />
        <button
          @click="toggleComplete(index)"
          v-else
          :title="item && item?.isCompleted ? 'Mark as completed' : 'Mark as incomplete'"
          class="todo-title"
        >
          <del v-if="item.isCompleted">{{ item.title }}</del>
          <template v-else>{{ item.title }}</template>
        </button>
        <button @click="edit(index)" v-if="editAt !== index">Edit</button>
        <button @click="done" v-if="editAt === index">Done</button>
        <button @click="remove(index)">Remove</button>
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
interface TodoItem {
  isCompleted: boolean;
  title: string;
}
import { defineComponent } from "vue";
export default defineComponent({
  data: function() {
    return {
      todo: "",
      todoList: [] as TodoItem[],
      editAt: -1
    };
  },
  methods: {
    addTodo: function(index: number) {
      if (!this.todo || typeof index !== "number") {
        return;
      }
      if (index > -1 && index < this.todoList.length) {
        this.todoList[index].title = this.todo;
        this.done();
      } else {
        this.todoList = [...this.todoList, { title: this.todo, isCompleted: false }];
      }
      this.todo = "";
    },
    remove: function(index: number) {
      this.todoList = this.todoList.filter((_, idx) => idx !== index);
      this.done();
    },
    edit: function(index: number) {
      if (index > -1 && index < this.todoList.length) {
        this.todo = this.todoList[index].title;
        this.editAt = index;
      }
    },
    done: function() {
      this.editAt = -1;
      this.todo = "";
    },
    toggleComplete: function(index: number) {
      this.todoList[index].isCompleted = !this.todoList[index]?.isCompleted;
    }
  }
});
</script>

<style lang="scss" scoped>
.todo-title {
  appearance: none;
  background: transparent;
  border: 0;

  &:focus {
    outline: 0;
  }

  &:hover {
    cursor: pointer;
  }
}

.list {
  list-style-type: none;
}
</style>
