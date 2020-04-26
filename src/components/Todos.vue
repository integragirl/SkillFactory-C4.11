<template>
  <div class="container">
    <div class="col-sm-10">
      <h1>Задачи</h1>
      <confirmation :message="confirmationMessage"
        v-if="showConfirmation">
      </confirmation>

      <h2> Выполнено задач = {{ counterCompleted }}</h2>
      <h2> НЕ выполнено задач = {{ counterNoCompleted }}</h2>

      <button type="button" id="task-add"
      class="btn btn-success btn-sm align-left d-block" v-b-modal.todo-modal>
        Добавить задачу
      </button>

      <table class="table table-dark table-stripped table-hover">
        <thead class="thead-light">
          <tr>
            <th>Uid</th>
            <th>Описание</th>
            <th>Выполнена?</th>
            <th></th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="(todo, index) in todos" :key="index">
            <td class="todo-uid">{{ todo.uid }}</td>
            <td>{{ todo.description }}</td>
            <td>
              <span v-if="todo.is_completed">Выполнено</span>
              <span v-else>Не выполнено</span>
            </td>
            <td>
              <div class="btn-group" role="group">
              <button type="button"
                        class="btn btn-secondary btn-sm"
                        v-b-modal.todo-update-modal
                        @click="updateTodo(todo)"
                        >
                    Обновить
                </button>
                &nbsp;
                <button type="button"
                        class="btn btn-danger btn-sm"
                        @click="deleteTodo(todo)">
                  X
                </button>
              </div>
            </td>
          </tr>
        </tbody>

      </table>

      <b-modal ref="addTodoModal"
               id="todo-modal"
               title="Добавить задачу"
               hide-footer>
        <b-form @submit="onSubmit" @reset="onReset" class="w-100">
          <b-form-group id="form-description-group"
                        label="Описание:"
                        label-for="form-description-input">
            <b-form-input id="form-description-input"
                          type="text"
                          v-model="addTodoForm.description"
                          required
                          placeholder="Завести задачу">
            </b-form-input>
          </b-form-group>
          <b-form-group id="form-complete-group">
            <b-form-checkbox-group v-model="addTodoForm.is_completed" id="form-checks">
              <b-form-checkbox value="true">Задача выполнена?</b-form-checkbox>
            </b-form-checkbox-group>
            </b-form-group>
            <b-button type="submit" variant="primary">Добавить</b-button>
            <b-button type="reset" variant="danger">Сброс</b-button>
        </b-form>
      </b-modal>

      <b-modal ref="updateTodoModal"
               id="todo-update-modal"
               title="Update"
               hide-footer>
        <b-form @submit="onUpdateSubmit" @reset="onUpdateReset" class="w-100">
        <b-form-group id="form-update-description-group"
                      label="Описание:"
                      label-for="form-update-description-input">
          <b-form-input id="form-update-description-input"
                        type="text"
                        v-model="updateTodoForm.description"
                        required>
          </b-form-input>
        </b-form-group>
        <b-form-group id="form-update-complete-group">
          <b-form-checkbox-group v-model="updateTodoForm.is_completed" id="form-update-checks">
            <b-form-checkbox value="true">Задача выполнена?</b-form-checkbox>
          </b-form-checkbox-group>
        </b-form-group>
        <b-button-group>
          <b-button type="submit" variant="primary">Обновить</b-button>
          <b-button type="reset" variant="danger">Сброс</b-button>
        </b-button-group>
        </b-form>
      </b-modal>

    </div>
  </div>
</template>

<style>
button#task-add {
  margin-top: 20px;
  margin-bottom: 20px;
}
h1, td {
  text-align: left;
}
.todo-uid {
  text-align: right;
}
</style>

<script>
// import axios from 'axios';
import Confirmation from './Confirmation.vue';

// const todoListURL = 'http://localhost:5000/api/tasks/';

// const TodoAr = [];
// TodoAr.push({ uid: 1, description: '1111', is_completed: 'true' });
// TodoAr.push({ uid: 2, description: '2222', is_completed: 'true' });
// SaveTodoArToLocalStorage();

function SaveTodoArToLocalStorage() {
  localStorage.setItem('TodoAr', JSON.stringify(TodoAr));
}

function GetTodoArToLocalStorage() {
  return JSON.parse(localStorage.getItem('TodoAr'))
}

const TodoAr = GetTodoArToLocalStorage();

window.addEventListener('storage', function(e) {
  console.log('storage' + e);
  location.reload();
});

export default {
  name: 'Todo',
  data() {
    return {
      todos: [],
      addTodoForm: {
        description: '',
        is_completed: [],
      },
      updateTodoForm: {
        uid: 0,
        description: '',
        is_completed: [],
      },
      confirmationMessage: '',
      showConfirmation: false,
      counterCompleted: 0,
      counterNoCompleted: 0,
    };
  },
  methods: {
    getTodos() {
      // axios.get(todoListURL)
      //  .then((response) => {
      //    this.todos = response.data.tasks;
      //  });

      this.todos = GetTodoArToLocalStorage();

      for (let i = 0; i < TodoAr.length; ++i) {
        if (typeof TodoAr[i]['is_completed'] !== "undefined" && TodoAr[i].is_completed === 'true') {
          this.counterCompleted = this.counterCompleted + 1;
        }
        else {
          this.counterNoCompleted = this.counterNoCompleted + 1;
        }
      }

    },
    resetForm() {
      this.addTodoForm.description = '';
      this.addTodoForm.is_completed = [];
      this.updateTodoForm.description = '';
      this.updateTodoForm.is_completed = [];
    },
    onSubmit(event) {
      event.preventDefault();
      this.$refs.addTodoModal.hide();
      const requestData = {
        description: this.addTodoForm.description,
        is_completed: this.addTodoForm.is_completed[0],
      };
      // axios.post(todoListURL, requestData)
      //  .then(() => {
      //    this.getTodos();
      //    this.confirmationMessage = `Задача "${requestData.description}" добавлена`;
      //    this.showConfirmation = true;
      //  });

      // localStorage.setItem('updateForm', true);
      TodoAr.push({
        uid: TodoAr.length + 1,
        description: requestData.description,
        is_completed: requestData.is_completed,
      });
      localStorage.setItem('TodoAr', JSON.stringify(TodoAr));

      this.getTodos();
      this.confirmationMessage = `Задача "${requestData.description}" добавлена`;
      this.showConfirmation = true;

      this.resetForm();
    },
    onReset(event) {
      event.preventDefault();
      this.$refs.addTodoModal.hide();
      this.resetForm();
    },
    updateTodo(todo) {
      this.updateTodoForm = todo;
    },
    deleteTodo(todo) {
      // const todoURL = todoListURL + todo.uid;
      // axios.delete(todoURL)
      //  .then(() => {
      //    this.getTodos();
      //    this.confirmationMessage = 'Задача удалена из списка';
      //    this.showConfirmation = true;
      //  });

      for (let i = 0; i < TodoAr.length; ++i) {
        if (TodoAr[i].uid === todo.uid) {
          console.log('удалить ' + TodoAr[i].description);
          TodoAr.splice(i, 1);
        }
      }
      SaveTodoArToLocalStorage();

      this.getTodos();
      this.confirmationMessage = 'Задача удалена из списка';
      this.showConfirmation = true;
    },
    onUpdateSubmit(event) {
      event.preventDefault();
      this.$refs.updateTodoModal.hide();
      const requestData = {
        description: this.updateTodoForm.description,
        is_completed: this.updateTodoForm.is_completed[0],
      };
      // const todoURL = todoListURL + this.updateTodoForm.uid;
      // axios.put(todoURL, requestData)
      //  .then(() => {
      //    this.getTodos();
      //    this.confirmationMessage = 'Задача обновлена';
      //    this.showConfirmation = true;
      //  });

      for (let i = 0; i < TodoAr.length; ++i) {
        if (TodoAr[i].uid === this.updateTodoForm.uid) {
          console.log('обновить ' + TodoAr[i].description);
          TodoAr[i].description = requestData.description;
          TodoAr[i].is_completed = requestData.is_completed;
        }
      }
      SaveTodoArToLocalStorage();

      this.getTodos();
      this.confirmationMessage = 'Задача обновлена';
      this.showConfirmation = true;
    },
    onUpdateReset(event) {
      event.preventDefault();
      this.$refs.updateTodoModal.hide();
      this.resetForm();
    },
  },
  components: {
    confirmation: Confirmation,
  },
  created() {
    this.getTodos();
  },
};
</script>
