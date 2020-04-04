<template>
  <div class="">
    <v-card v-for="(task, idx) in tasks" :key="idx" style="margin:5px;">
      <v-card-title><h4>{{task.title}}</h4></v-card-title>
      <v-divider></v-divider>
      <v-card-text>
        {{task.description}}
      </v-card-text>
      <v-card-text>
        Point : {{task.point}}
      </v-card-text>
      <v-card-text>
        Assigned to : {{task.assignedto}}
      </v-card-text>
      <v-divider></v-divider>
      <v-card-actions>

      <v-layout row justify-center>
          <v-dialog v-model="dialog[idx]" persistent max-width="600px">
            <template v-slot:activator="{ on }">
                <v-btn color="success" dark v-on="on">Show Details</v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="headline">Detail Task: {{task.title}}</span>
              </v-card-title>
              <v-card-text>
                <v-divider></v-divider>
                <v-container grid-list-md>
                  <v-layout wrap>
                    <v-card-text>
                      <strong>Description:</strong> {{task.description}}
                    </v-card-text>
                    <v-card-text>
                      <strong>Point:</strong> {{task.point}}
                    </v-card-text>
                    <v-card-text>
                      <strong>Assigned to:</strong> {{task.assignedto}}
                    </v-card-text>
                    <v-card-text>
                      <strong>Status:</strong> {{task.status}}
                    </v-card-text>
                  </v-layout>
                </v-container>
                <v-divider></v-divider>
                <v-btn color="danger" dark @click="deleteTask(task._id)">Delete</v-btn>
              </v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn v-if="status == 'To-Do'" color="primary" dark @click="changeStatus(task._id, 'Back-Log')">Back-Log</v-btn>
                <v-btn v-if="status == 'Back-Log' || status == 'Doing'" color="info" dark @click="changeStatus(task._id, 'To-Do')">To-Do</v-btn>
                <v-btn v-if="status == 'To-Do' || status == 'Done'" color="warning" dark @click="changeStatus(task._id, 'Doing')">Doing</v-btn>
                <v-btn v-if="status == 'Doing'" color="success" dark @click="changeStatus(task._id, 'Done')">Done</v-btn>
                <v-btn color="blue darken-1" dark @click="dialog = [false]">Cancel</v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-layout>

      </v-card-actions>
    </v-card>
  </div>
</template>

<script>
import axios from 'axios'
import io from 'socket.io-client'

const socket = io('http://localhost:3000');

  export default {
    data: () => ({
      dialog: [],
      url: "http://localhost:3000"
    }),
    methods: {
      changeStatus(id, newStatus) {
        axios.patch(`${this.url}/kanban/${id}`, {status: newStatus})
            .then(() => {
                console.log('berhasil mengupdate status')
                socket.emit('kanban')
                this.dialog = [false]
            })
            .catch((error) => {
                console.log(error.message)
            })
      },
      deleteTask(id) {
        axios.delete(`${this.url}/kanban/${id}`)
            .then(() => {
                console.log('berhasil mendelete')
                socket.emit('kanban')
                this.dialog = [false]
            })  
            .catch((error) => {
                console.log(error.message)
            })
      }
    },
    props: ['cards', 'tasks', 'status']
  }
</script>

<style>
</style>