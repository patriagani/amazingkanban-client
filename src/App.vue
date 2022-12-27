<template>
  <v-app>
    <v-content>
      <v-container>
        <br>
        <v-row>
          <v-col>
            <h2>Amazing Kanban</h2>
          </v-col>
          <v-spacer/>
          <v-col>
            <v-layout row justify-center>
            <v-dialog v-model="dialog" persistent max-width="600px">
              <template v-slot:activator="{ on }">
              <v-btn dark color="blue darken-1" v-on="on">Create Task</v-btn>
              </template>
              <v-card>
                <v-card-title>
                  <span class="headline">Create New Task</span>
                </v-card-title>
                <v-card-text>
                  <v-container grid-list-md>
                    <v-layout wrap>
                      <v-flex xs12>
                        <v-text-field v-model="title" label="Title*" required></v-text-field>
                      </v-flex>
                      <v-flex xs12>
                        <v-text-field v-model="description" label="Description*" required></v-text-field>
                      </v-flex>
                      <v-flex xs12>
                        <v-text-field v-model="point" label="Point*" required type="number"></v-text-field>
                      </v-flex>
                      <v-flex xs12>
                        <v-text-field v-model="assignedto" label="Assigned to*" required></v-text-field>
                      </v-flex>
                    </v-layout>
                  </v-container>
                  <small>*indicates required field</small>
                </v-card-text>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn dark color="blue darken-1" flat @click="dialog = false">Close</v-btn>
                  <v-btn dark color="blue darken-1" flat @click="createNewTask">Save</v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
            </v-layout>
          </v-col>
        </v-row>
        <br>
        <v-row>
          <v-col>
            <Board :color="'primary'" :title="'Back-Log'" :tasks="backlog"/>
          </v-col>
          <v-col>
            <Board :color="'info'" :title="'To-Do'" :tasks="todo"/>
          </v-col>
          <v-col>
            <Board :color="'warning'" :title="'Doing'" :tasks="doing"/>
          </v-col>
          <v-col>
            <Board :color="'success'" :title="'Done'" :tasks="done"/>
          </v-col>
        </v-row>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import Board from './components/Board';
import axios from 'axios'
import io from 'socket.io-client'

const socket = io(process.env.VUE_APP_API_URL);

export default {
  name: 'App',

  components: {
    Board,
  },

  data: () => ({
    url: process.env.VUE_APP_API_URL,
    dialog: false,
    title: "",
    description: "",
    point: 0,
    assignedto: "",

    backlog: [
      
    ],
    todo: [
      
    ],
    doing: [
      
    ],
    done: [

    ]
  }),

  methods: {
    createNewTask() {
      let obj = {
        title: this.title,
        description: this.description,
        point: this.point,
        assignedto: this.assignedto
      }

      axios.post(`${this.url}/kanban`, obj)
        .then(() => {
          this.title = ""
          this.description = ""
          this.point = 0
          this.assignedto = ""
          this.dialog = false
          socket.emit('kanban')
          console.log('berhasil menambah task')
        })
        .catch((error) => {
          console.log(error.message)
        })
    },

    getAllTask() {
      axios.get(`${this.url}/kanban`)
        .then((response) => {
          this.backlog = []
          this.todo = []
          this.doing = []
          this.done = []
          response.data.forEach(element => {
            if (element.status == "Back-Log") {
              this.backlog.push(element)
            }
            else if (element.status == "To-Do") {
              this.todo.push(element)
            }
            else if (element.status == "Doing") {
              this.doing.push(element)
            }
            else if (element.status == "Done") {
              this.done.push(element)
            }
          });
        })
        .catch((error) => {
          console.log(error.message)
        })
    }
  },

  created() {
    this.getAllTask()
    socket.on('update-kanban', () => {
      this.getAllTask()
      console.log('Kanban Board Updated');
    });
  }
};
</script>
