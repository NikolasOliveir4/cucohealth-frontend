
<template>
  <div>
    <b-table striped hover :items="clientes" :fields="fields">
      <template #cell(actions)="row">
        <a href="#" class="btn btn-warning" @click="editClient(row.item)"><b-icon icon="tools"></b-icon>Editar</a>
        <a href="#" class="btn btn-danger" @click="deleteClient(item.id,key)"><b-icon icon="trash-fill" aria-hidden="true"></b-icon>Deletar</a>
      </template>
    </b-table>
    <input type="text" name="search_name" id="search_name" v-model="search_name" @keyup="searchInput">
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
    v-bind:showModal="showModal"
    v-bind:data="clientSelected"
    />
  </div>
</template>

<script>

import axios from "axios";
import CardClient from "./CardClient"
  export default{
    name: 'Table',
    components:{
      CardClient
    },
    data() {
      return {
        clientes: [],
        search_name: "",
        timeout: null,
        showModal: false,
        clientSelected: null,
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
     this.clientes = res.data.clientes;
    } catch (error) {
      //Silent error
    }
      
    },

    async deleteClient(id, k){
      try { 
        const res = await axios.delete(`http://localhost:8000/clientes/${id}`);
        if(res.status == 200){
          this.clientes.splice(k,1);
        }
      } catch (error) {
        //Silent error
      }
    },

    editClient(e){
      console.log(e)
      this.showModal = true;
      this.clientSelected = e;
    },

    searchInput({target}){
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