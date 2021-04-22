<template>
  <v-data-table
    :headers="headers"
    :items="entradas"
    sort-by="id desc"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar
        flat
      >
        <v-toolbar-id>My WP CRUD</v-toolbar-id>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        <v-spacer></v-spacer>
        <v-dialog
          v-model="dialog"
          max-width="500px"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              color="primary"
              dark
              class="mb-2"
              v-bind="attrs"
              v-on="on"
            >
              New Item
            </v-btn>
          </template>
          <v-card>
            <v-card-id>
              <span class="headline">{{ formid }}</span>
            </v-card-id>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col
                    cols="12"
                    
                  >
                    <v-text-field
                      v-model="editedItem.title"
                      label="Titulo"
                    ></v-text-field>
                  </v-col>
                  <!-- <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.id"
                      label="id"
                    ></v-text-field>
                  </v-col> -->
                  <v-col
                    cols="12"
                    
                  >
                    <v-text-field
                      type="text"
                      v-model="editedItem.content"
                      label="Contenido"
                    ></v-text-field>
                  </v-col>
                  <!-- <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.date"
                      label="date (g)"
                    ></v-text-field>
                  </v-col> -->
                  <!-- <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.status"
                      label="status (g)"
                    ></v-text-field>
                  </v-col> -->
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="blue darken-1"
                text
                @click="close"
              >
                Cancel
              </v-btn>
              <v-btn
                color="blue darken-1"
                text
                @click="save"
              >
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-id class="headline">Are you sure you want to delete this item?</v-card-id>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <v-icon
        small
        class="mr-2"
        @click="editItem(item)"
        color="blue"
      >
        mdi-pencil
      </v-icon>
      <v-icon
        small
        @click="deleteItem(item)"
        color="red"
      >
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn
        color="primary"
        @click="initialize"
      >
        Reset
      </v-btn>
    </template>
  </v-data-table>
</template>
<script>
  export default {
    data: () => ({
    
    config:{
        headers:{
            Authorization: 'Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczpcL1wvZ3JlaXZpbi5uZXQiLCJpYXQiOjE2MTkxMDQ0MTAsIm5iZiI6MTYxOTEwNDQxMCwiZXhwIjoxNjE5NzA5MjEwLCJkYXRhIjp7InVzZXIiOnsiaWQiOiIxIn19fQ.WrF0pnEyeXFkV2F65oFXYZixGTs0G4qo1vZeR7NsLaQ'
        }
    },

      dialog: false,
      dialogDelete: false,
      headers: [
        {
          text: 'ID',
          align: 'start',
          sortable: false,
          value: 'id',
        },
        { text: 'Entradas', value: 'title' },
        //{ text: 'content (g)', value: 'content' },
        { text: 'Fecha', value: 'date' },
        { text: 'Estado', value: 'status' },
        { text: 'Acciones', value: 'actions', sortable: false },
      ],
      entradas: [],
      editedIndex: -1,
      editedItem: {
        title: 'Entrada WP',
        id: '',
        content: '',
        date: '',
        status: '',
      },
      defaultItem: {
        title: '',
        id: '',
        content: '',
        date: '',
        status: '',
      },
    }),

    computed: {
      formid () {
        return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
      },
    },

    watch: {
      dialog (val) {
        val || this.close()
      },
      dialogDelete (val) {
        val || this.closeDelete()
      },
    },

    created () {
      this.initialize()
    },

    methods: {
        // Para limpiar <p> del content
      limpiar(value){
        return value.replace(/<\/?[^>]+(>|$)/g, "")
      },
      async initialize () {

          
        try {
                const entradasDB = await this.axios.get('wp/v2/posts?filter[orderby]=date&order=asc');

                //filter[orderby]=date&order=desc'
                //console.log(entradasDB.data);

                await entradasDB.data.forEach(element => {
                    //console.log(element)
                    let  item={}
                    item.id = element.id
                    item.title = element.title.rendered;
                    item.content = element.content.rendered;
                    item.date = element.date;
                    item.status = element.status;
                    this.entradas.unshift(item);
                    //this.entradas.push(item);
                });

            } catch (error) {
                console.log(error)
            }
      },

      editItem (item) {
        this.editedIndex = this.entradas.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },

      async deleteItem (item) {
        
        /* Codigo Blue web
        const index = this.entradas.indexOf(item)
        const respuesta = confirm('Are you sure you want to delete this item?') && this.entradas.splice(index, 1)

        if(respuesta){
            await this.axios.delete(`wp/v2/posts/${item.id}`, this.config);
        }
        */
        
        this.editedIndex = this.entradas.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialogDelete = true
      },

      async deleteItemConfirm () {

        console.log(this.editedItem.id);

        await this.axios.delete(`wp/v2/posts/${this.editedItem.id}`, this.config);
        
        this.entradas.splice(this.editedIndex, 1)
        
        
        this.closeDelete()
      },

      close () {
        this.dialog = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },

      closeDelete () {
        this.dialogDelete = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },

      async save () {
        if (this.editedIndex > -1) {

            
            console.log(this.editedItem);
            try {
                const entradaNueva = await this.axios.post(`wp/v2/posts/${this.editedItem.id}`,this.editedItem, this.config);
                Object.assign(this.entradas[this.editedIndex], this.editedItem)       
            } catch (error) {
                console.log(error);
            }

          //const entradaDb = await this.axios.post(`wp/v2/posts/${this.editedItem.id}`,this.editedItem, this.config); 

        } else {
         
          // Agregar
            console.log(this.editedItem);
            const entrada = {
            title: this.editedItem.title,
            content: this.editedItem.content,
            status: 'publish'
            }
            try {
                const entradaNueva = await this.axios.post('wp/v2/posts',entrada, this.config);
                console.log(entradaNueva.data);
                this.editedItem.id = entradaNueva.data.id;  
                this.editedItem.date = entradaNueva.data.date;  
                this.editedItem.status = entradaNueva.data.status;  
            } catch (error) {
                console.log(error);
            }
            this.entradas.push(this.editedItem)
                    
          //this.entradas.push(this.editedItem)
        }
        this.close()
      },
    },
  }
</script>