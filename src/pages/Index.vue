<template>
  <q-page padding>

    <q-page-sticky v-if="todos.length > 0" position="bottom-right" :offset="[18, 70]">
      <q-btn round color="positive" icon="check" @click="onDoneTask()">
        <q-tooltip>Mark your Task!</q-tooltip>
      </q-btn>
    </q-page-sticky>

    <q-page-sticky position="bottom-right" :offset="[18, 18]">
      <q-btn round color="primary" icon="add" @click="onAddForm()">
        <q-tooltip>Add your Task!</q-tooltip>
      </q-btn>
    </q-page-sticky>

    <div v-if="todos.length > 0">
      <q-list class="q-mt-md" bordered separator v-bind:key="index" v-for="(todo, index) in todos">
        <q-item @click="onClickItem(index)" clickable v-ripple>
          <q-item-section side top>
            <q-checkbox v-model="todo.is_done" />
          </q-item-section>
          <q-item-section>
            <q-item-label>{{ todo.task }}</q-item-label>
            <q-item-label caption lines="2">{{ todo.description }}</q-item-label>
          </q-item-section>

          <q-item-section side>
            <q-btn round size="sm" icon="edit" @click="editTask(index)" color="positive" />
          </q-item-section>
          <q-item-section side>
            <q-btn round size="sm" icon="delete_outline" @click="onDeleteDialog(index)" color="negative" />
          </q-item-section>
        </q-item>
      </q-list>
    </div>
    <div v-else>
      <h6 class="text-center">No Tasks.</h6>
    </div>

    <q-dialog v-model="show.delete_form">
      <q-card>
        <q-card-section>
          <div class="text-h6" color="negative">Delete Task ({{active_delete.task}})?</div>
        </q-card-section>

        <q-card-section>
          <i>Once deleted, this action can't be undone.</i>
        </q-card-section>

        <q-card-actions align="right" class="text-primary">
          <q-btn flat icon="fas fa-trash" color="negative" label="Delete" @click="removeTask(1)" />
          <q-btn flat label="Close" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>

    <q-dialog v-model="show.form" persistent>
      <q-card style="min-width: 400px">
        <q-card-section>
          <div class="text-h6">Task Details</div>
        </q-card-section>

        <q-card-section>
          <q-input v-model="form.task" label="Task"/>
        </q-card-section>

        <q-card-section>
          <q-input v-model="form.description" label="Description"/>
        </q-card-section>

        <q-card-actions align="right" class="text-primary">
          <q-btn v-if="!is_edit_mode" class="full-width" icon="add_circle" label="Insert" @click="addTask()" color="primary" />
          <q-btn v-if="is_edit_mode" class="full-width" icon="fas fa-save" label="Save" @click="updateTask()" color="primary"/>
          <q-btn class="full-width" flat label="Cancel" @click="resetForm()" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>

  </q-page>
</template>

<style>
</style>

<script>
export default {
  name: 'PageIndex',
  data () {
    return {
      is_edit_mode: false,
      show: {
        form: false,
        delete_form: false
      },
      form: {
        task: '',
        description: '',
        is_done: false
      },
      active_delete: {
        index: 0,
        task: ''
      },
      todos: [
        {
          task: 'Go to the Mall',
          description: 'Go shopping',
          is_done: false
        },
        {
          task: 'Learn Kwaesar',
          description: 'Kwarsar nambawan',
          is_done: false
        }
      ]
    }
  },
  methods: {
    addTask () {
      const form = this.form
      if (form.task.length > 0) {
        this.todos.push({
          task: form.task,
          description: form.description,
          is_done: false
        })

        this.$q.notify({
          message: 'Task Added!',
          color: 'positive',
          position: 'bottom-right',
          timeout: 3000,
          actions: [{
            icon: 'check',
            color: 'white'
          }]
        })

        this.resetForm()
      }
    },
    onAddForm () {
      this.resetForm()
      this.show.form = true
    },
    onDeleteDialog (index) {
      console.log(this.show)
      this.show.delete_form = true
      const selectedTodo = this.todos[index]
      if (selectedTodo) {
        this.active_delete = {
          index: index,
          task: selectedTodo.task
        }
      }
    },
    onDoneTask () {
      this.todos.forEach(el => {
        console.log('el', el)
      })
    },
    onClickItem (index) {
      console.log('onClickItem')
      if (this.todos[index]) {
        const todo = this.todos[index]
        todo.is_done = !todo.is_done
      }
    },
    removeTask () {
      const index = this.active_delete.index
      if (this.todos[index]) {
        this.todos.splice(index, 1)
        this.show.delete_form = false
        this.$q.notify({
          message: 'Task Deleted!',
          color: 'negative',
          position: 'bottom-right',
          timeout: 3000,
          actions: [{
            icon: 'delete_outline',
            color: 'white'
          }]
        })
      }
    },
    // doneTask: function (index) {
    //   if (this.todos[index]) {
    //     this.todos[index].is_done = true

    //     this.$q.notify({
    //       message: 'Task Marked Done!',
    //       color: 'positive'
    //     })
    //   }
    // },
    editTask: function (index) {
      const data = this.todos[index] || []
      if (data) {
        this.show.form = true
        this.is_edit_mode = true
        this.form = {
          index: index,
          task: data.task,
          description: data.description
        }
      }
    },
    updateTask () {
      const form = this.form
      const taskIndex = form.index || 0

      this.todos[taskIndex].task = form.task
      this.todos[taskIndex].description = form.description

      this.resetForm()

      this.$q.notify({
        message: 'Task Updated!',
        color: 'positive',
        position: 'bottom-right',
        timeout: 3000,
        actions: [{
          icon: 'check',
          color: 'white'
        }]
      })
    },
    resetForm () {
      this.is_edit_mode = false
      this.form = {
        task: '',
        description: '',
        is_done: false
      }
      this.show = {
        form: false,
        delete_form: false
      }
    }
  }
}
</script>
