<script>
  
  import { getTransitionRawChildren } from 'vue';
import Header from './components/Header.vue'


  export default{
    
    components: {
      Header
    },
    
    data(){
      return {
        change: false,
        changedToDo: {},
        id: 0,
        newToDo : '',
        todolist: [{id: 0, text: "Laravel", done: false, topic : 'Web'},
                   {id: 1, text: "VueJS", done: true, topic : 'Web'},
                   {id: 2, text: "WordPress", done: false, topic : 'Web'},
                  ],
        helper: "",
        hideCompleted: false,
        sideBar: false,
        topic: '',
      }
    },

    mounted(){
      if (localStorage.getItem('todolist')){
          this.todolist = JSON.parse(localStorage.getItem('todolist'))
      }
      if (localStorage.getItem('id')){
        this.id = JSON.parse(localStorage.getItem('id'));
      }
    }, 

    watch: {
      todolist: {
        handler(new_list, old_list){
          const parsed = JSON.stringify(new_list)
          console.log('New list = ', parsed)
          localStorage.setItem('todolist', parsed)
        },
        deep: true
      },
      id: {
        handler(new_id, old_id){
          const parsed = JSON.stringify(new_id)
          console.log('New id = ', parsed)
          localStorage.setItem('id', parsed)
        },
      }
    },

    methods: {

      toChange(){
        for (const todo in this.todolist){
          if(this.todolist[todo].id == this.changedToDo.id){
            console.log(this.todolist[todo].id)
            this.todolist[todo].text = this.changedToDo.text
            this.todolist[todo].topic = this.changedToDo.topic
            this.changedToDo = {}
            
            break
          }
          
          this.sideBar = false
          this.change = false
        }
      },  

      callSideBar(){
        this.sideBar = true
      },

      addToDo(){
        this.change = false
        if (!this.newToDo){
          this.helper = "Empty string!"
        }
        else{
          this.helper = ""
          this.todolist.push({id: this.id++, text: this.newToDo, done: false, topic : this.topic});
          this.newToDo = '';
          this.topic = '';
        }
      },
      
      removeToDo(todo){
        this.todolist = this.todolist.filter(task => task != todo);
      },

      clear(){
        this.todolist = []
        // this.id = 0
        localStorage.removeItem('todolist')
        localStorage.removeItem('id')
      },
      
      changeToDo(todo){
        this.change = true
        Object.assign(this.changedToDo, todo)
        this.callSideBar()
      }
    },


    computed: {
      filtredToDos(){
        if(this.hideCompleted){
          return this.todolist.filter(todo => todo.done == false)
        }
        
        return this.todolist
      }
    }
  }
</script>

<template>
    <Header />

    <div class="form">
      <ul>
        <li v-for="todo in filtredToDos">
          <div class="task" :class="{done : todo.done}" @click="changeToDo(todo)">
            <input @click="" type="checkbox" v-model="todo.done">
            <span :class="{done : todo.done}">{{todo.text}} 
              <span v-if="todo.topic" class="topic"> #{{todo.topic}}</span>
            </span>
            <div class="btn-container" @click="removeToDo(todo)">
              <div class="btn-remove"></div>
            </div>
          </div>
        </li>
      </ul>
      <div>
        <button class="add" @click="callSideBar(); change=false"> Add </button>
        <button class="add hideCompleted" @click="hideCompleted = !hideCompleted">{{hideCompleted ? 'Show all' : 'Hide completed'}}</button>
        <button class="add hideCompleted clear" @click="clear">Clear</button>
      </div>
    </div>

    
    <div class="sideBar" :class="{visible : sideBar}">
      <div class="btn-container sideBarClose" @click="sideBar = false; change=false">
          <div class="btn-remove"></div>
      </div>

      <form v-if="!change" @submit.prevent="addToDo" class="addForm">
        <input v-model="newToDo" placeholder="new toDo..."  class="addInput">
        <br>
        <br>
        <input v-model="topic" placeholder="topic..."  class="addInput">
        <div v-if="helper">
          {{helper}}
        </div>
        <button type="submit" @click="sideBar = false" class="submit-btn">Submit</button>
      </form>

      <form v-else @submit.prevent="toChange" class="addForm">
        <input v-model="changedToDo.text" class="addInput">
        <br>
        <br>
        <input v-model="changedToDo.topic" placeholder="topic..."  class="addInput">
        <div v-if="helper">
          {{helper}}
        </div>
        <button type="submit" class="submit-btn">Submit</button>
      </form>
    </div>
</template>

<style>

.sideBarClose{
  top: 20px;
}

.sideBar{
  display: none;
  position: absolute;
  top: 48px;
  right: 0;
  width: 20%;
  height: 90vh;
  background-color: #f3f3ff;
  z-index: 100;
  align-items: center;
}

.visible {
  display: inline-block;
  transition: 0.1s all;
}

.hideCompleted {
  width: 35%;
  background-color: rgb(150, 160, 160);
}

.clear {
  background-color: #df4343;
}


</style>