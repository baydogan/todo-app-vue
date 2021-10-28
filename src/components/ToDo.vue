<template>
   <div class="container-sm">
       <div class="row">
           <div class="col-12 mt-5">
               <div class="col-12 d-flex justify-content-start">
                       <h4>Add New ToDo</h4>
                   </div>
               <div class="col-12 d-flex justify-content-center mt-2">
                   <input type="text" class="form-control me-1" v-model="todoInput"  @keydown.enter="addToDo">
                   <button class="btn btn-primary" v-on:click="addToDo" :disabled="todoInput.length === 0">Add</button>
               </div>
               <ul class="list-group mt-4 d-flex">
                   <li class="list-group-item d-flex justify-content-between align-items-center" v-for="todo in todoList" :key="todo.id" > 
                       
                       {{todo.description}}
                       <div class="col d-flex justify-content-end pe-2">
                           <span>{{todo.date}}</span>
                       </div>
                       <button class="btn btn-danger btn-sm" v-on:click="deleteToDo(todo.id)">Delete</button>
                    </li>
               </ul>
               <div v-show="todoList.length === 0" class="alert alert-primary" role="alert">
                Well done! There is no tasks to do!
                </div>
           </div>
           <h6 v-show="todoList.length !== 0" class="text-info mt-2">There is {{todoList.length}} tasks to do.</h6>
       </div>
   </div>
   
</template>

<script>
import ToDoServices from '@/services/ToDoServices.js'
import axios from 'axios'


export default {
    data(){
        return {
            todoList: [],
            todoInput: ''
        }  
    },

    methods: {
        addToDo(){
            const dateOptions  = { year: 'numeric', month: 'long', day: 'numeric' }
            const sendData = {
                description : this.todoInput,
                date : new Date().toLocaleString('en-US', dateOptions)
            }
            axios.post('http://localhost:3000/todoList', sendData).then(response_result =>{
                console.log(response_result)
                this.todoList.push(response_result.data)
                this.todoInput = ''

                
            })
            
        },

        deleteToDo(id){
            axios.delete(`http://localhost:3000/todoList/${id}`)
            .then(()=> {
                const index = this.todoList.map(todoList => todoList.id).indexOf(id);
                this.todoList.splice(index, 1)
            }).catch(error => {
                console.log(error)
            })

            
        }
    },

    created() {
            
            ToDoServices.getTodos()
            .then(response => {
                this.todoList = response.data
            })
            .catch(error => {
                console.log(console.log(error))
            })
        }
}
</script>