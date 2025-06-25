<template>
  <q-page class="q-pa-md">
    <q-card class="container" style="max-width: 800px; margin: 0 auto;">
      <!-- section 1: header -->
       <q-card-section>
        <div class="text-h2">To-Do List</div>
       </q-card-section>

       <!-- section 2: form and submit -->
        <q-card-section>
          <div class="row q-gutter-md q-mb-lg">
           <div class="col">
            <q-input outlined v-model="taskName" aria-placeholder="Enter task name" dense @keyup.enter="addTask"/>
           </div>
           <div class="col-auto">
            <q-btn label="Submit" color="secondary" @click="addTask" :disable="!taskName.trim()" style="height: 40px;"/>
           </div>
          </div>
        </q-card-section>

        <!-- section 3: task list display -->
         <div class="q-gutter-md">
          <q-card 
            v-for="task in [...newTasks, ...tasks]"
            :key="task.id"
            flat
            bordered
            class="task-card"
          >

            <q-card-section class="row items-center q-pa-md">
              <div class="col">
                <div v-if="task.isEditing">
                <q-input v-model="task.title" 
                dense outlined 
                label="Edit Task"
                autofocused
                @keyup.enter="toggleEdit(task)"
                />
              </div>

              <div v-else class="text-body1">
                {{ task.title }}
              </div>
              </div>

              <!-- edit and delete -->
                <!-- edit button -->
              <div class="col-auto">
                <div class="row q-gutter-sm">
                    <div class="col-auto">
                      <q-btn flat 
                      :label="task.isEditing?'Save': 'Edit'" 
                      color="primary" 
                      dense
                      style="min-width: 60px;"
                      @click="toggleEdit(task)"/>
                      
                      <!-- delete button -->
                      <q-btn
                        flat 
                        dense
                        label="Delete" 
                        color="negative" 
                        @click="deleteTask(task.id)"
                        style="min-width: 60px;"/>
                    </div>
                </div>
              </div>
            </q-card-section>
              
          </q-card>
         </div>
        
    </q-card>
  </q-page>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'

// task types
interface Task{
  userId?: number
  id: number
  title: string
  completed: boolean
  isEditing: boolean
}

// type of ref
const taskName = ref<string>('')
const tasks = ref<Task[]>([])
const newTasks = ref<Task[]>([])
const savedTasks = localStorage.getItem('newTasks')

/*
* I am trying to use the local storage to keep the added tasks not
* found in typicode in display when the site refreshes
* Update: Still need more experience in dealing with this, especially in the PUT method
*/
if(savedTasks){
  try{
    newTasks.value = JSON.parse(savedTasks)
  } catch (error){
    console.warn('Invalid Local Storage data', error);
    localStorage.removeItem('newTasks')
  }
}

const API_URL = 'https://jsonplaceholder.typicode.com/todos'
let idCounter = 1000;

// const savedId = localStorage.getItem('idCounter');
// if(savedId){
//   idCounter = parseInt(savedId)
// }

/*
* GET part of the criteria using the todos in typicode
*/ 

// fetching tasks
onMounted(async () => {
  try{
    const res = await axios.get<Task[]>(`${API_URL}`)
    tasks.value = res.data
  } catch(error){
    console.error('Failed to load the tasks', error)
  }
})

// POST part of the criteria
async function addTask() : Promise<void> {
  try{
    const res = await axios.post<Task>(API_URL, {
      title: taskName.value.trim(),
      completed: false
    })

    const newTask: Task & { isEditing: boolean } = {
      ...res.data,
      id: idCounter++,
      isEditing: false
    }

    // tasks.value.unshift(res.data)
    newTasks.value.unshift(newTask)
    localStorage.setItem('newTasks', JSON.stringify(newTasks.value))

    taskName.value = ' '
  }catch(error){
    console.log('Failed to add task. Please try again', error)
  }

  if(!taskName.value.trim()) return

  console.log('Add task clicked', taskName.value)
}

// The PUT operation of the criteria
async function toggleEdit(task:Task & { isEditing: boolean }): Promise<void>{
  if(task.isEditing){
    try{
      await axios.put(`${API_URL}/${task.id}`, {
        title: task.title,
        completed: task.completed
      })
      
      localStorage.setItem('newTasks', JSON.stringify(newTasks.value))
      console.log(`Updated task ${task.id}`)
    } catch (error){
      console.error("Failed to update task", error)
    }
  }

  task.isEditing =  !task.isEditing
}

// DELETE function of the criteria
async function deleteTask(taskId:number): Promise<void> {
  try{
    await axios.delete(`${API_URL}/${taskId}`)

    tasks.value = tasks.value.filter(task=>task.id !== taskId)
    newTasks.value = newTasks.value.filter(task => task.id !== taskId)

    localStorage.setItem('newTasks', JSON.stringify(newTasks.value))

    console.log(`Deleted task ${taskId}`)
  }catch(error){
    console.log('Failed to delete task. Try again', error)
  }
}
</script>

<style scoped>
.task-card {
  transition: all 0.2s ease;
}

.task-card:hover {
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.container {
  font-family: 'Courier New', Courier, monospace;
}
</style>