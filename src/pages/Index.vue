<template>
  <q-page padding>
    <q-form
      class="q-gutter-md"
    >
      <q-input
        filled
        v-model="task"
        label="Task Name"
      />

      <q-input
        filled
        v-model="description"
        label="Task Description"
      />

      <div>
        <q-btn v-if="!is_edit_form" class="full-width" icon="fas fa-plus-circle" label="Insert" @click="addTask()" color="primary"/>
        <q-btn v-if="is_edit_form" class="full-width" icon="fas fa-save" label="Update" @click="updateTask()" color="primary"/>
        <q-btn label="Reset" class="full-width q-mt-md" icon="fas fa-redo-alt" @click="resetForm()" color="secondary" />
      </div>
    </q-form>

    <q-list class="q-mt-md" bordered separator v-bind:key="index" v-for="(todo, index) in todos">
      <q-item  clickable v-ripple>
        <q-item-section>
          <q-item-label>{{ todo.task }}</q-item-label>
          <q-item-label caption lines="2">{{ todo.description }}</q-item-label>
        </q-item-section>

        <q-item-section side top>
          <q-btn-group push>
            <q-btn v-if="!todo.is_done" push size="sm" icon="fas fa-check"  @click="doneTask(index)" color="primary"/>
            <q-btn push size="sm" icon="fas fa-edit"  @click="editTask(index)" color="positive" />
            <q-btn push size="sm" icon="fas fa-trash"  @click="onDeleteDialog(index)" color="negative" />
          </q-btn-group>
        </q-item-section>
      </q-item>
    </q-list>

    <q-dialog v-model="show_delete_dialog">
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

  </q-page>
</template>

<style>
</style>

<script>
export default {
  name: 'PageIndex',
  data () {
    return {
      task: '',
      description: '',
      is_edit_form: false,
      show_delete_dialog: false,
      active_delete: {
        index: 0,
        task: ''
      },
      todos: [
        {
          task: 'Lorem ipsum',
          description: 'Lorem ipsum',
          is_edit: false,
          is_done: true
        },
        {
          task: 'Lorem ipsum 2',
          description: 'Lorem ipsum',
          is_edit: false,
          is_done: false
        }
      ]
    }
  },
  methods: {
    addTask: function () {
      if (this.task.length > 0) {
        this.todos.push({
          task: this.task,
          description: this.description,
          is_edit: false,
          is_done: false
        })

        this.$q.notify({
          message: 'Task Added!',
          color: 'positive'
        })

        this.resetForm()
      }
    },
    onDeleteDialog: function (index) {
      this.show_delete_dialog = true
      const selectedTodo = this.todos[index]
      this.active_delete = {
        index: index,
        task: selectedTodo.task
      }
    },
    removeTask: function () {
      const index = this.active_delete.index
      if (this.todos[index]) {
        this.todos.splice(index, 1)
        this.show_delete_dialog = false
        this.$q.notify({
          message: 'Task Deleted!',
          color: 'negative'
        })
      }
    },
    doneTask: function (index) {
      if (this.todos[index]) {
        this.todos[index].is_done = true

        this.$q.notify({
          message: 'Task Marked Done!',
          color: 'positive'
        })
      }
    },
    editTask: function (index) {
      const data = this.todos[index] || []
      if (data) {
        this.is_edit_form = true
        this.todos[index].is_edit = true
        this.task = data.task
        this.description = data.description
      }
    },
    updateTask: function () {
      const taskIndex = this.todos.findIndex(todo => todo.is_edit === true)

      this.todos[taskIndex].task = this.task
      this.todos[taskIndex].description = this.description

      this.resetForm()

      this.$q.notify({
        message: 'Task Updated!',
        color: 'positive'
      })
    },
    resetForm: function () {
      this.task = ''
      this.description = ''
      this.is_edit_form = false
    }
  }
}
</script>
