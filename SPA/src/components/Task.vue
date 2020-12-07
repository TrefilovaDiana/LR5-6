<template lang="html">
  <div class="task" v-if="task">
    <h1 class="task__name">{{ task.name }}</h1>
    <p class="task__body">{{ task.description }}</p>
    <p class="task__done">{{ task.complete_status ? "Выполнена" : "Не выполнена" }}</p>
    <button v-on:click="editTask">Изменить</button>
    <button v-on:click="deleteTask">Удалить</button>
  </div>
</template>

<script>
  import axios from 'axios';

  export default {
    props: ['id'],

    data() {
      return {
        task: null,	//текущий объект задачи
        endpoint: 'http://localhost:3000/tasks/',  //адрес сервера баз данныз
      }
    },

    methods: {
		//действие когда нажали на кнопку удалить задачу
		deleteTask(){
			//создаем в более главном компоненте событие с именем deleteTask и параметром task_id
			//говорим более главному компоненты, что удали задачу вот с таким id, дальше удаление задачи - забота главного компонента
        this.$emit('deleteTask', this.task.id);
      },

//действие когда нажали на кнопку редактировать задачу
      editTask()
      {
		  //создаем в более главном компоненте событие с именем startEdit и параметром task_id
			//говорим более главному компоненты, что начни редактировать задачу вот с таким id. Дальше главный компонент запускает механизм редактирования задачи
        this.$emit('startEdit', this.task.id);
      },

		//метод подгрузки задачи из БД
      getTask(id) {
		  //говорим axios какую задачу подгрузить
        axios(this.endpoint + id)
          .then(response => {
            this.task = response.data
          })
          .catch( error => {
            alert(error);
          })
      },
    },
    
	//при создании компонента подгружаем задачу из БД
    created() {
      this.getTask(this.id);
    },

	//Библиотечный код, который следит, что если задача была как-то изменена, то поменять данные на интерфейсе
	//лично я этого не писал, видимо это было в шаблоне
    watch: {
      '$route'() {
        this.getTask(this.id);
      }
    }
  }
</script>

<style lang="scss" scoped>
  .task {
    position: relative;
    max-width: 500px;
    margin: 0 auto;
    padding: 50px 20px 70px;
    &__name {
      position: relative;
      text-transform: uppercase;
    }
    &__body {
      position: relative;
    }
  }
</style>
