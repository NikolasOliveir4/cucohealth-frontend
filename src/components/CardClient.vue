<template>
    <b-modal id="modal-1" v-model="openModal" size="xl" footer-class="d-none" v-on:hidden="$emit('closeModal')">
        <h1 v-if="isEdit" class="d-flex justify-content-center" style="color: #2196f3">Atualizar dados</h1>
        <h1 v-else class="d-flex justify-content-center" style="color: #2196f3">Adicionar cliente</h1>
        <div class="container">
            <div class="usuario">
                <div class="foto">
                    <img src="../assets/images/profile.jpg" alt="">
                </div>
                <div class="info">
                    <div>
                        <p>Nome completo</p>
                        <input type="text" class="nome" v-model="cliente.nome" placeholder="Digite aqui...">
                    </div>
                    <div>
                        <p>Data de Nascimento</p>
                        <input type="date" id="data_nascimento" :max="today" class="data_nascimento" v-model="cliente.data_nascimento" autocomplete="">
                    </div>
                </div>
            </div>
            <div class="break"></div>
            <div class="docs">
                <div class="tipo">
                    <input v-if="cliente == {}" type="radio" id="cpf" v-model="cliente.tipo" name="cpf_cnpj" value="0" checked>
                    <input v-if="cliente.tipo == 0 || cliente.tipo == undefined" type="radio" id="cpf" v-model="cliente.tipo" name="cpf_cnpj" value="0" checked>
                    <input v-else type="radio" id="cpf" v-model="cliente.tipo" name="cpf_cnpj" value="0">
                    <label for="html">CPF</label><br>
                    <div class="break"></div>
                    <input v-if="cliente.tipo == 1" type="radio" id="cnpj" v-model="cliente.tipo" name="cpf_cnpj" value="1" checked>
                    <input v-else type="radio" id="cnpj" v-model="cliente.tipo" name="cpf_cnpj" value="1" >
                    <label for="css">CNPJ</label><br>
                </div>
                <div class="documento">
                    <template v-if="cliente.tipo*1 == 0">
                        <label id="cpf" v-if="cliente.tipo*1 == 0">CPF:</label>
                        <p v-if='message != ""'>CPF {{message}}</p>
                        <the-mask class='form-control ' :mask="['###.###.###-##']" v-model="cliente.cpf_cnpj" @keyup.native="searchInputCpfCnpj" placeholder="CPF"/>	
					</template>
                    <template v-if="cliente.tipo*1 == 1">
                        <label id="cnpj" v-if="cliente.tipo*1 == 1">CNPJ:</label>
                        <p v-if='message != ""'>CNPJ {{message}}</p>
                        <the-mask class='form-control ' :mask="['##.###.###/####-##']" v-model="cliente.cpf_cnpj" @keyup.native="searchInputCpfCnpj" placeholder="CNPJ"/>
                    </template>
                </div>
            </div>
            <div class="break"></div>
            <div class="contato">
                <div class="email">
                    <p>E-mail</p>
                    <input type="email" name="email" id="email" v-model="cliente.email">
                </div>
                <div class="telefone">
                    <p>Telefone</p>
                    <the-mask class='form-control ' :mask="['+## (##) #####-####']" v-model="cliente.telefone" placeholder="Telefone"/>

                </div>
            </div>
            <div class="break"></div>
            <div class="enviar" style="display: flex; justify-content: space-between; gap: 80px">
                <input v-if="isEdit" @click="validation('edit')" type="button" value="ENVIAR">
                <input v-else @click="validation('create')" type="button" value="ENVIAR">
                <input @click="closeModal" type="button" value="CANCELAR">
            </div>
        </div>
    </b-modal>
</template>
<script>
import axios from "axios";

export default {
    name: 'CardClient',
    props:{
        showModal: {
            type: Boolean,
            default: false,
        },
        customer: { type: Object },
        isEdit: { type: Boolean }
    },
    data(){
        return{
            openModal: this.showModal || false,
            cliente: {...this.customer},
            message: "",
            today: null,
        };
    },
    mounted(){
        this.today = this.getToday()
    },
    methods: {

        getToday() {
        const today = new Date();
        const year = today.getFullYear();
        const month = String(today.getMonth() + 1).padStart(2, '0');
        const day = String(today.getDate()).padStart(2, '0');
        const formattedDate = `${year}-${month}-${day}`;
        return formattedDate;
        },
        async searchInputCpfCnpj({target}){
            const query = target.value.replace(/\D/g, '');
            console.log(query.length)
            if(query.length > 10){
                if(query.length > 11){
                    if(this.validCnpj(query)){
                  
                        try {
                            const res = await axios.get(`http://localhost:8000/clientes/cpfcnpj?q=${query}`);
                            
                            this.message = res.data.message;
                        } catch (err) {
                                //Silent error
                        }
                    }else{
                        this.message = " Inválido!"
                    }
                }else{
                    if(this.validCpf(query)){
        
                        try {
                            const res = await axios.get(`http://localhost:8000/clientes/cpfcnpj?q=${query}`);
                            
                            this.message = res.data.message;
                        } catch (err) {
                                //Silent error
                        }
                    }else{
                        this.message = " Inválido!"
                    }
                }
            }else{
                this.message= "";
            }
        },
        closeModal() {
            this.$emit('closeModal');
        },
        sendData(type) {
            if(type == 'edit'){
                
                this.$emit('updateClient',this.cliente);
            }else{
                this.$emit('createClient',this.cliente);
            }
            this.$emit('closeModal');
            

        },
        async validation(type) {
            let erro = "";
            let erro_nome = [];
            if(this.cliente.nome){
                var nome_sobrenome = this.cliente.nome.split(" ")               
                if(nome_sobrenome[0] && nome_sobrenome[1]){
                    if((nome_sobrenome[1] == 'de' || nome_sobrenome[1] == 'da' || nome_sobrenome[1] == 'do') && nome_sobrenome[2]) {
                        if(nome_sobrenome[0].length >= 3 && nome_sobrenome[2].length >= 3) {
                        var nome_sem_espaco = this.cliente.nome.trim()
                        if(!nome_sem_espaco.match(/^[a-zA-Zà-úÀ-Ú ]{2,}$/)) {
                            erro += '<br>Nome inválido, preencha corretamente'
                        }
                        } else {
                            var nome_sem_espaco = this.cliente.nome.trim()
                            if(!nome_sem_espaco.match(/^[a-zA-Zà-úÀ-Ú ]{2,}$/)) {
                                erro += '<br>Nome inválido, preencha corretamente'
                            }else{
                                erro_nome = ['ok','Nome curto, você tem certeza?'] 
                            }
                        }
                    } else {
                        if(nome_sobrenome[0].length >= 3 && nome_sobrenome[1].length >= 3) {
                            var nome_sem_espaco = this.cliente.nome.trim()
                            if(!nome_sem_espaco.match(/^[a-zA-Zà-úÀ-Ú ]{2,}$/)) {
                                erro += '<br>Nome inválido, preencha corretamente'
                            }
                        } else {
                            var nome_sem_espaco = this.cliente.nome.trim()
                            if(!nome_sem_espaco.match(/^[a-zA-Zà-úÀ-Ú ]{2,}$/)) {
                                erro += '<br>Nome inválido, preencha corretamente'
                            }else{
                                erro_nome = ['ok','Nome curto, você tem certeza?'] 
                            }
                        }
                    }
                }else{
                    erro+='<br>Preencha o nome completo';
                }
            }else{
                erro+='<br>Preencha o nome completo';
            }

            if(!this.cliente.data_nascimento){
                erro+= '<br>Preencha a Data de nascimento'
            }

            if(this.cliente.cpf_cnpj){
                if(this.cliente.tipo == 0 && this.cliente.cpf_cnpj) {
                    const  teste1 = this.validCpf(this.cliente.cpf_cnpj);
                    if(teste1 == false){
                        erro += '\nCPF Inválido';
                    }
                };
                if(this.cliente.tipo == 1) {
                    const  teste1 = this.validCnpj(this.cliente.cpf_cnpj);
                    if(teste1 == false){
                        erro += '\nCNPJ Inválido';
                    }
                };
            }else{
                erro+= '<br>Preencha o CPF/CNPJ'
            }
            if(this.message == ' Já cadastrado!' && this.cliente.tipo == 0){
                erro += '<br>CPF' + this.message;
            }
            if(this.message == ' Já cadastrado!' && this.cliente.tipo == 1){
                erro += '<br>CNPJ' + this.message;
            }
            if(this.cliente.email){
                if(!this.validateEmail(this.cliente.email)) {
                    erro += '<br>Email inválido';
                }
            }else{
                erro += '<br>Preencha o Email';
            }
            if(!this.cliente.data_nascimento) {
                erro+= '<br>Preencha a Data de nascimento'
            }
            if(erro_nome[0] == 'ok'){
                this.$swal({
                title: 'Atenção!',
                text: erro_nome[1],
                icon: 'warning',
                showCancelButton: true,
                confirmButtonColor: '#3085d6',
                cancelButtonColor: '#d33',
                confirmButtonText: 'Confirmar',
                cancelButtonText: 'Cancelar'
                }).then((result) => {
                if (result.isConfirmed) {
                    if(erro != "") {
                        const result = this.$swal("Atenção!", erro, "warning");
    
                    } else {
                        this.sendData(type)
                    }
                }
                })
            }else{
                if(erro != "") {
                        const result = this.$swal("Atenção!", erro, "warning");
    
                    } else {
                        this.sendData(type)
                    }
            }
        },
        validCpf(cpf) {
		    if ( !cpf || cpf.length != 11
					|| cpf == "00000000000"
					|| cpf == "11111111111"
					|| cpf == "22222222222" 
					|| cpf == "33333333333" 
					|| cpf == "44444444444" 
					|| cpf == "55555555555" 
					|| cpf == "66666666666"
					|| cpf == "77777777777"
					|| cpf == "88888888888" 
					|| cpf == "99999999999" )
				return false
			var soma = 0
		    var resto
			for (var i = 1; i <= 9; i++) 
				soma = soma + parseInt(cpf.substring(i-1, i)) * (11 - i)
			resto = (soma * 10) % 11
		    if ((resto == 10) || (resto == 11))  resto = 0
		    if (resto != parseInt(cpf.substring(9, 10)) ) return false
			soma = 0
		    for (var i = 1; i <= 10; i++) 
		    	soma = soma + parseInt(cpf.substring(i-1, i)) * (12 - i)
		    resto = (soma * 10) % 11
		    if ((resto == 10) || (resto == 11))  resto = 0
		    if (resto != parseInt(cpf.substring(10, 11) ) ) return false
		    return true
		},
		validCnpj(cnpj) {
		    if ( !cnpj || cnpj.length != 14
	    			|| cnpj == "00000000000000" 
	    			|| cnpj == "11111111111111" 
	    			|| cnpj == "22222222222222" 
	    			|| cnpj == "33333333333333" 
	    			|| cnpj == "44444444444444" 
	    			|| cnpj == "55555555555555" 
	    			|| cnpj == "66666666666666" 
	    			|| cnpj == "77777777777777" 
	    			|| cnpj == "88888888888888" 
	    			|| cnpj == "99999999999999")
		        return false
		    var tamanho = cnpj.length - 2
		    var numeros = cnpj.substring(0,tamanho)
		    var digitos = cnpj.substring(tamanho)
		    var soma = 0
		    var pos = tamanho - 7
		    for (var i = tamanho; i >= 1; i--) {
		      soma += numeros.charAt(tamanho - i) * pos--
		      if (pos < 2) pos = 9
		    }
		    var resultado = soma % 11 < 2 ? 0 : 11 - soma % 11
		    if (resultado != digitos.charAt(0)) return false;
		    tamanho = tamanho + 1
		    numeros = cnpj.substring(0,tamanho)
		    soma = 0
		    pos = tamanho - 7
		    for (var i = tamanho; i >= 1; i--) {
		      soma += numeros.charAt(tamanho - i) * pos--
		      if (pos < 2) pos = 9
		    }
		    resultado = soma % 11 < 2 ? 0 : 11 - soma % 11
		    if (resultado != digitos.charAt(1)) return false
		    return true;
		},
        validateEmail(email) {
            return email.match(
                /^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
            );
        },
    },
};
</script>

<style>

</style>