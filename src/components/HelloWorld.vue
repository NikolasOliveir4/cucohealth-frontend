
<template>
  <div>
    <!-- <b-table striped hover :items="items" :fields="fields"></b-table> -->
    
    <table>
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
          <tr>
            <td>{{item.nome}}</td>
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
    </table>

  </div>
</template>

<script>

import axios from "axios";

  export default {
    data() {
      return{
        clientes: [],
      }
  },

  mounted() {
    this.getClients();
  },

  methods: {
    getClients(){
      var that = this;
      axios.get("http://localhost:8000/clientes").then(function(response){
        that.clientes = response.data.clientes
        console.log(that.clientes);

      });
      
    },

    deleteClient(id, k){
      var that = this;
      axios.delete("http://localhost:8000/clientes/"+id).then(function(response){
        // that.clientes = response.data.clientes
        console.log(that.clientes);
        that.clientes.splice(k,1)
      });
      
    }
  }
}
</script>