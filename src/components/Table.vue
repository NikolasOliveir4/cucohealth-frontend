
<template>
  <div>
   <div>
      <div style="width: 20%" class="m-3">
        <!-- <label for="search_name" style="display: flex;">Filtrar por Nome ou CPF/CNPJ</label> -->
        <input class="w-100 mb-2" type="text" name="search_name" id="search_name" v-model="search_name" @keyup="searchInput" placeholder="Filtrar por Nome ou CPF/CNPJ">
        <a href="#" class="btn btn-success d-flex justify-content-start" @click="isEdit = false, showModal = true, clientSelected = null">+Novo Cliente</a>
      </div>

   </div>

    <b-table striped hover :items="clientes" :fields="fields">
      <template #cell(data_nascimento)="row">
        <div>{{ convertDate(row.item.data_nascimento) }}</div>
      </template>
      <template #cell(cpf_cnpj)="row" >
        <input v-if="row.item.tipo === 0" class="w-100 text-center border-0 bg-transparent outline-none" v-mask="'###.###.###-##'" v-bind:value="row.item.cpf_cnpj" readonly/>
        <input v-else class="w-100 text-center border-0 bg-transparent outline-none" v-mask="'##.###.###/####-##'" v-bind:value="row.item.cpf_cnpj" readonly/>
      </template>
     
      <template #cell(telefone)="row">
        <input class="w-100 text-center border-0 bg-transparent outline-none" v-mask="'+## (##) #####-####'" v-bind:value="row.item.telefone" readonly/>
      </template>
      <template #cell(actions)="row">
        <a href="#" class="btn btn-warning me-2" @click="editClient(row.item)"><b-icon icon="tools"></b-icon> Editar</a>
        <a href="#" class="btn btn-danger" @click="deleteClient(row.item.id,row.index)"><b-icon icon="trash-fill" aria-hidden="true"></b-icon> Deletar</a>
      </template>
    </b-table>
    <!-- <table>
      <thead>
        <tr>
          <th>Nome</th>
          <th>CPF/CNPJ</th>
          <th>Data Nascimento</th>
          <th>Email</th>
          <th>Telefone</th>
          <th>Ações</th>
        </tr>
      </thead>
      <tbody>
        <template v-for="(item, key) in clientes">
          <tr v-bind:key="item.id">
            <td>{{item}}</td>
            <td>{{item.cpf_cnpj}}</td>
            <td>{{item.data_nascimento}}</td>
            <td>{{item.email}}</td>
            <td>{{item.telefone}}</td>
            <td>
              <a href="#" class="btn btn-warning" @click="editClient(item)">Editar</a>
              <a href="#" class="btn btn-danger" @click="deleteClient(item.id,key)">Deletar</a>
            </td>
          </tr>
        </template>
      </tbody>
    </table> -->
    <CardClient 
      v-if="showModal"
      v-bind:showModal="showModal"
      v-bind:customer="clientSelected"
      v-bind:isEdit="isEdit"
      v-on:closeModal="showModal = false"
      v-on:updateClient="updateClient($event)"
      v-on:createClient="createClient($event)"
    />
  </div>
</template>

<script>
import {mask} from 'vue-the-mask'
import axios from "axios";
import CardClient from "./CardClient"
  export default{
    name: 'Table',
    components:{
      CardClient,
    },
    directives:{
      mask
    },
    data() {
      return {
        clientes: [],
        search_name: "",
        timeout: null,
        showModal: false,
        clientSelected: null,
        isEdit: null,
         fields: [
          {
            key: 'nome',
            sortable: true
          },
          {
            key: 'data_nascimento',
            label: 'Data de nascimento',
            sortable: true
          },
          {
            key: 'cpf_cnpj',
            label: 'CPF/CNPJ',
            // Variant applies to the whole column, including the header and footer
          },
          {
            key: 'email',
            label: 'Email',
            // Variant applies to the whole column, including the header and footer
          },
          {
            key: 'telefone',
            label: 'Telefone',
            // Variant applies to the whole column, including the header and footer
          },
          {
            key: 'actions',
            label: 'Ações',
            // Variant applies to the whole column, including the header and footer
          }
        ],
        // items: [
        //   { isActive: true, age: 40, nome: 'Dickerson', data_nascimento: 'Macdonald' },
        //   { isActive: false, age: 21, nome: 'Larsen', data_nascimento: 'Shaw' },
        //   { isActive: false, age: 89, nome: 'Geneva', data_nascimento: 'Wilson' },
        //   { isActive: true, age: 38, nome: 'Jami', data_nascimento: 'Carney' }
        // ]
      }
    },

  mounted(){
    this.getClients();
  },

  methods: {
   async getClients(){
    try {
     const res = await axios.get("http://localhost:8000/clientes");
      console.log(res.data.clientes);
      res.data.clientes.map((value)=>{

      })
      this.clientes = res.data.clientes;
    } catch (error) {
      //Silent error
    }
      
    },

    async deleteClient(id, k){
      console.log(k)
      const result = await this.$swal("Excluir cliente", "Você tem certeza?", "warning");
      if (!result.isConfirmed) return;
      try { 
        const res = await axios.delete(`http://localhost:8000/clientes/delete/${id}`);
        if(res.status == 200){
          const deleted = await this.$swal("Cliente excluído", "", "success");
          this.clientes.splice(k,1);
        }
      } catch (error) {
        //Silent error
      }
    },
    async createClient(client) {
        console.log(client,'CLIENTE');
      // if (!result.isConfirmed) return;
      try { 
        const res = await axios.post('http://localhost:8000/clientes',client);
        if(res.status == 200){
          this.clientes = res.data.clientes;
          const result = await this.$swal("Cliente inserido", "", "success");
          console.log(res.data.clientes);
        }
      } catch (error) {
        //Silent error
      }
    },
    async updateClient(client) {
      console.log(client);
      console.log('CHEGUEI NO UPDATE')
      // if (!result.isConfirmed) return;
      try { 
        const res = await axios.put(`http://localhost:8000/clientes/${client.id}`,client);
        if(res.status == 200){
          this.clientes = res.data.clientes;
          const result = await this.$swal("Cliente atualizado", "", "success");
          console.log(this.clientes);
        }
      } catch (error) {
        //Silent error
      }
    },
    editClient(e) {
      console.log(e)
      this.showModal = true;
      this.isEdit = true;
      this.clientSelected = e;
    },
    searchInput({target}) {
      clearTimeout(this.timeout);
      this.timeout = setTimeout(async ()=>{
        try {
          const res = await axios.get(`http://localhost:8000/clientes/search?q=${target.value}`);
          console.log(res);
          this.clientes = res.data.clientes;
        } catch (err) {
        	//Silent error
        }
      }, 300);
    },

    convertDate(date) {
      const newDate = new Date(date);
      return Intl.DateTimeFormat('pt-BR', { timeZone: 'UCT' }).format(newDate);
    },
  }
}
</script>

<style scoped lang="scss">
  ::v-deep .sr-only {
    display: none;
  }
  table {
    width: 100%;
  }
</style>