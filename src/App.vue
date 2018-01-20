<template lang="pug">
  #app
    section.section
      .container
        .columns.is-desktop.is-centered
          .column.is-three-quarters-tablet.is-half-desktop
            input.input.is-large.has-text-centered(
              v-if="!name", 
              placeholder='Digita tu nombre y Enter', 
              v-model='newUser',
              @keyup.enter="addUser"
            )
            .panel(v-else)
              .panel-heading 
                h1 Tareas {{ name }}
              .panel-block
                input.input#input-task(placeholder="Nueva Tarea", ref="inputTaskName" v-model="newTask.name")
                input.input(placeholder="Horas", type="number", v-model="newTask.duration")
                template(v-if="hasTask()")
                  button.button.is-primary(@click="addTask") +
                  button.button.is-danger(@click="addTask") &times
              .panel-block(v-for="(task, i) in tasks") 
                .media
                  .media-content {{ task.name }} - {{ task.duration }} horas
                  .media-right
                    button.delete(@click="removeTask(i)")
              .panel-block
                span.has-text-info.is-pulled-right Total tiempo: {{totalTime}} horas

</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      name: '',
      newUser: '',
      tasks: [],
      newTask: {
        name: '',
        duration: ''
      }
    }
  },
  methods: {
    addTask () {
      if (!this.newTask.name || !this.newTask.duration) return
      this.tasks.push(this.newTask)
      this.newTask = {}
      this.$refs.inputTaskName.focus()
      window.localStorage.setItem('tasks', JSON.stringify(this.tasks))
    },
    removeTask (task) {
      this.tasks.splice(task, 1)
    },
    hasTask () {
      return this.newTask.name || this.newTask.duration
    },
    addUser () {
      if (!this.newUser) return
      this.name = this.newUser
      this.newUser = ''
      window.localStorage.setItem('name', JSON.stringify(this.name))
    }
  },
  computed: {
    totalTime () {
      return this.tasks.reduce((acumulado, task) => {
        return acumulado + parseInt(task.duration)
      }, 0)
    }
  },
  created () {
    this.tasks = JSON.parse(window.localStorage.getItem('tasks')) || []
    this.name = JSON.parse(window.localStorage.getItem('name')) || ''
  }
}
</script>

<style lang="sass">
  @import './sass/main.sass'
  #input-task
    width : 300%
</style>
