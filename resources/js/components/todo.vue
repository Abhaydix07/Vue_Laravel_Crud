<template>
  <div class="container">
    <div class="row justify-content-center mt-5">
      <div class="col-md-8">
        <div class="card">
          <div class="card-header">Todo</div>

          <div class="card-body">
            <div class="input-group">
              <input
                type="text"
                placeholder="Todo.."
                class="form-control"
                v-model="todo_input"
              />
              <div class="input-group-append" aria-describedby="todo">
                <button
                  v-if="edit_todo_id"
                  type="button"
                  class="btn btn-info"
                  @click="updateTodo()"
                >
                  Update
                </button>
                <button
                  v-else
                  type="button"
                  class="btn btn-info"
                  @click="saveTodo()"
                >
                  Add
                </button>
              </div>
              <button
                type="button"
                class="btn btn-sm float-right"
                @click="resetTodo()"
              >
                Reset
              </button>

              <table class="table table-bordered mt-4">
                <thead>
                  <td>S. no</td>
                  <td>Name</td>
                  <td>Edit</td>
                  <td>Delete</td>
                </thead>
                <tbody v-for="(todo, index) in todos" :key="index">
                  <td>{{ ++index }}</td>
                  <td>{{ todo.name }}</td>

                  <td>
                    <button
                      @click="editTodo(--index)"
                      type="button"
                      class="btn btn-sm"
                      style="background-color: skyblue"
                    >
                      Update
                    </button>
                  </td>
                  <td>
                    <button
                      @click="deleteTodo(--index)"
                      type="button"
                      class="btn btn-danger btn-sm"
                      style="background-color: red"
                    >
                      Delete
                    </button>
                  </td>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      todos: [],
      api: "http://127.0.0.1:8000/api/todos",
      todo_input: "",
      edit_todo_id: "",
      edit_index: "",
    };
  },
  mounted() {
    this.axios.get(this.api).then((res) => {
      this.todos = res.data;
    });
  },
  methods: {
    saveTodo() {
      if (this.todo_input.length > 0) {
        let data = { name: this.todo_input };
        this.axios.post(this.api, data).then((res) => {
          this.todos.push(res.data);
          this.todo_input = "";
        });
      }
    },
    deleteTodo(index) {
      if (this.todos[index].id) {
        this.axios.delete(this.api + "/" + this.todos[index].id).then((res) => {
          if (res.status == 200) {
            this.axios.get(this.api).then((res) => {
              this.todos = res.data;
            });
          }
        });
      }
    },
    editTodo(index) {
      if (this.todos[index].id) {
        this.edit_todo_id = this.todos[index].id;
        this.todo_input = this.todos[index].name;
        this.edit_index = index;
      }
    },
    updateTodo() {
      if (this.todo_input.length > 0) {
        let data = { name: this.todo_input };
        this.axios
          .put(this.api + "/" + this.todos[this.edit_index].id, data)
          .then((res) => {
            if (res.status == 200) {
              this.edit_todo_id = "";
              this.todo_input = "";
              this.edit_index = "";
              this.axios.get(this.api).then((res) => {
                this.todos = res.data;
              });
            }
          });
      }
    },
    resetTodo() {
      this.edit_todo_id = "";
      this.todo_input = "";
      this.edit_index = "";
    },
  },
};
</script>
