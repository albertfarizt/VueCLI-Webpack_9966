<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">To Do List UGD</h3>

        <v-card>
            <v-card-title primary-title>
                <v-text-field
                v-model="search"
                append-icon="mdi-magnify"
                label="Search"
                single-line
                hide-details></v-text-field>
                <v-spacer></v-spacer>
                <v-btn color="success" dark @click="dialog = true">Tambah</v-btn>
            </v-card-title>
            
            <v-data-table :headers="headers" :items="filterPriority" :search="search" pagination.sync="pagination">   
                <template v-slot:[`item.priority`]="{ item }">
                    <v-card v-if="item.priority == 'Penting'" style="border-color:lightcoral; color:red; width:fit-content;" outlined>
                        {{ item.priority}}
                    </v-card>
                    <v-card v-else-if="item.priority == 'Biasa'" style="border-color:lightblue; color:blue; width:fit-content;" outlined>
                        {{ item.priority}}
                    </v-card>
                    <v-card v-else-if="item.priority == 'Tidak Penting'" style="border-color:lightgreen; color:green; width:fit-content;" outlined>
                        {{ item.priority}}
                    </v-card>
                </template>

                <template v-slot:[`item.actions`]="{ item }">
                    <v-btn small @click="editItem(item)">
                    edit
                    </v-btn>
                    <v-btn small @click="deleteItem(item)">
                    delete
                    </v-btn>
                </template>
            </v-data-table>
        </v-card>

        <v-dialog
            v-model="dialog"
            persistent
            max-width="600px"
            transition="dialog-transition"
        >
            <v-card>
                <v-card-title>
                    <span class="headline">From Todo</span>
                </v-card-title>
                <v-card-text>
                    <v-container>
                        <v-text-field
                            v-model="formTodo.task"
                            label="Task"
                            required
                        ></v-text-field>
                        <v-select
                            :items="['Penting', 'Biasa', 'Tidak Penting']"
                            v-model="formTodo.priority"
                            label="All Priority"
                            required
                        ></v-select>
                        <v-textarea
                        v-model="formTodo.note" label="Note" required>
                        </v-textarea>
                    </v-container>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">Cancel</v-btn>
                    <v-btn color="blue darken-1" text @click="save">Save</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-main>
</template>
<script>
export default {
    name:"List",
    data(){
        return {
            search:null,
            dialog:false,
            tempTodo:null,
            filters:"All Priority",
            selected: [],
            headers: [
                {
                    text:'Task',
                    align: 'start',
                    sortable: true,
                    value: 'task',
                },
                { text: "Priority", value:"priority"},
                { text: "Note", value:"note"},
                { text: "Actions", value:"actions"},
            ],
            todos: [
                {
                    task: "bernafas",
                    priority: "Penting",
                    note: "hufftts",
                },
                {
                    task: "nongkrong",
                    priority: "Tidak Penting",
                    note: "bersama teman teman"
                },
                {
                    task: "masak",
                    priority: "Biasa",
                    note: "masak air 500ml",
                },
            ],
            formTodo:  {
                task: null,
                priority: null,
                note: null,
            },
        };
    },
    methods: {
        save(){
            let index =  this.findIndexTodos(this.formTodo);
            if(index < 0){ // alias ga ketemu -1
                this.todos.push(this.formTodo);
            }else {
                this.todos[index] = this.formTodo;
            }
            this.resetForm();
            this.dialog = false;
        },
        cancel(){
            let index =  this.findIndexTodos(this.formTodo);
            if(index < 0){ // alias ga ketemu -1
                this.todos.push(this.tempTodo);
            }else {
                this.todos[index] = this.tempTodo;
            }
            console.log(this.tempTodo);
            this.resetForm();
            this.dialog = false;
        },
        resetForm(){
            this.formTodo = {
                task: null,
                priority: null,
                note: null,
            };
        },
        editItem(item){
            this.formTodo = item;
            this.dialog = true;
        },
        deleteItem(item){
            let x = window.confirm("Apa yakin ingin menghapus?  ");
            if(x === true){
                let index = this.findIndexTodos(item);
                this.todos.splice(index,1);
            }
        },
        findIndexTodos(item){
            return this.todos.findIndex(obj => obj.task === item.task);
        },
        
    },
    computed: {
        filterPriority(){
            let finds = this.filters;
            if(finds == "All Priority"){
                return this.todos;
            }else {
                var fil = this.todos.filter(function(x){
                    return x.priority == finds;
                })
                return fil;
            }
            
        },
    }
    
};
</script>